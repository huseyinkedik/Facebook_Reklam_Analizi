# Facebook Reklam Veri Analizi

Bu proje, Facebook reklam kampanyalarıyla ilgili bir veri setinin analiz edilmesi ve görselleştirilmesi amacıyla geliştirilmiştir. Bu doküman, projenin aşamalarını, kullanılan fonksiyonları ve analiz süreçlerini açıklamaktadır. Veri seti, günlük reklam harcamalarını, gösterimleri, tıklamaları ve ROMI gibi önemli performans göstergelerini içermektedir. Veri seti olarak **[facebook_ads_data (2.0)](https://drive.google.com/file/d/1P51VI_-GaHs8OU4OyHPPWFx70_PY2yxN/view?usp=sharing)** dosyasından yararlanılmıştır. 

Proje boyunca, aşağıdaki sorulara cevap aranmaktadır:

### Sorular:

1. Verileri günlere göre gruplandırın ve şu iki grafiği oluşturun:
    - 2021 yılındaki günlük reklam harcamalarının toplamını gösteren bir grafik.
    - 2021 yılındaki günlük ROMI'yi gösteren bir grafik.

2. Verileri kampanya adına göre gruplayarak iki grafik oluşturun:
    - Her bir kampanyadaki toplam reklam harcamalarının grafiği.
    - Her bir kampanyadaki genel ROMI grafiği.

3. Her kampanyada günlük ROMI'nin dağılımını box plot kullanarak belirleyin.

4. facebook_ads_data.csv tablosundaki ROMI değerlerinin dağılımını gösteren bir histogram oluşturun.

5. facebook_ads_data.csv tablosundaki tüm sayısal göstergeler arasındaki korelasyonun ısı haritasını çıkarın. Hangi göstergeler en yüksek ve en düşük korelasyona sahip? `total_value` ile hangi faktörler arasında korelasyon var?

6. Değişkenler arasındaki ilişkiyi görselleştirmek için `total_spend` ve `total_value` verileri üzerinden oluşturulan doğrusal regresyonlu bir nokta grafiği oluşturun (`lmplot()` fonksiyonunu kullanabilirsiniz).

---
## Kullanılan Yöntemler ve Açıklamalar

### 1. Verileri günlere göre gruplandırın ve şu iki grafiği oluşturun:
- 2021 yılındaki günlük reklam harcamalarının toplamı
- 2021 yılındaki günlük ROMI grafiği


**Veri Yükleme ve Hazırlık**  

Projeye başlarken ilk adım, veriyi doğru bir şekilde okumak ve incelemektir. Burada, CSV formatındaki Facebook reklam verilerini pandas kütüphanesi aracılığıyla Python’a aktarıyoruz. Bu işlem, veri setimizi analiz edebilmemiz için gerekli olan temel adımlardan biridir.

```python
df = pd.read_csv(r"C:\Users\husey\Downloads\facebook_ads_data (2.0).csv")
```

Bu kod ile dosyadan verileri alıp, `df` adını verdiğimiz bir DataFrame’e (tablo yapısı) aktarıyoruz. CSV dosyası, Facebook reklam kampanyalarının her gününe dair harcama, gösterim ve tıklama gibi temel bilgileri içerir. Veri okuma işlemi başarıyla tamamlandıktan sonra, artık veriler üzerinde çeşitli analizler ve görselleştirmeler yapabiliriz.

---

**Günlük İstatistiklerin Gruplanması**  

Bir reklam kampanyası için günlük performansı anlamak çok önemlidir. Bu aşamada, tarih (`ad_date`) bazında veriyi grupluyoruz ve her gün için toplam harcama, gösterim, tıklama ve elde edilen değeri topluyoruz.

```python
daily_stats = df.groupby('ad_date')[['total_spend', 'total_impressions', 'total_clicks', 'total_value']].sum()
```

Bu gruplama işlemi, veri setinin günlük performansını daha anlaşılır hale getirir. Her bir tarih için toplam harcamalar ve diğer metrikler toplandıktan sonra, artık kampanyaların günlük başarılarına dair genel bir bakış elde ederiz. Bu bize, reklam kampanyasının hangi günlerde daha etkin olduğunu ve hangi günlerde daha fazla bütçe harcandığını analiz etme imkanı sağlar.

---

**ROMI (Return on Marketing Investment) Hesaplaması**   

Bir reklam kampanyasının başarılı olup olmadığını anlamak için sadece harcamalara değil, bu harcamaların karşılığında ne kadar değer elde edildiğine de bakmak gerekir. İşte bu noktada ROMI devreye giriyor. ROMI, yapılan harcamaların ne kadar değer yarattığını ölçen bir metrik.

```python
daily_stats['romi'] = daily_stats['total_value'] / daily_stats['total_spend']
```

Bu formül, toplam harcamaların elde edilen değere oranını gösterir. Daha basit bir ifadeyle, 1 birim harcama yaptığınızda ne kadar değer elde ettiğinizi öğrenmiş olursunuz. ROMI yüksekse, harcamalarınızın geri dönüşü de yüksektir. Bu, reklam performansını anlamak için oldukça kritik bir göstergedir.

---

**Verinin Daha İyi Şekillendirilmesi**  

Gruplama ve hesaplamalar sonrası, veriyi analiz etmek ve görselleştirmek için daha rahat çalışabilir hale getirmek gerekiyor. Bunun için tabloyu yeniden şekillendirip, tarih sütununu tekrar ekliyoruz.

```python
daily_stats = daily_stats.reset_index()
```

Bu adım, tarih bilgisini tabloya bir sütun olarak ekler ve diğer sütunlarla birlikte kolayca erişilebilir hale getirir. Artık tablo üzerinde daha rahat bir şekilde filtreleme ve analiz işlemleri yapabiliriz.

---

**2021 Yılını Filtreleme**  

Şimdi, analiz etmek istediğimiz zaman aralığını belirlemeliyiz. Örneğin, 2021 yılına odaklanmak istiyorsak, sadece bu yılın verilerini çekmemiz gerekiyor.

```python
daily_stats_2021 = daily_stats[(daily_stats['ad_date'] >= '2021-01-01') & (daily_stats['ad_date'] <= '2021-12-31')]
```

Bu filtreleme işlemi ile sadece 2021 yılına ait reklam verilerini seçiyoruz. Böylece, yalnızca belirli bir dönemin performansını daha detaylı inceleyebiliriz. Bu dönem, özellikle pandemi sonrası dönemdeki reklam kampanyalarının nasıl bir performans gösterdiğini anlamak için önemlidir.

---

**Reklam Harcamalarının Zaman İçindeki Değişimi**  

Bir reklam kampanyasının bütçesi zaman içinde dalgalanabilir. Bu dalgalanmaları görselleştirmek için zaman serisi grafikleri oldukça faydalıdır. Harcama miktarlarını daha anlaşılır hale getirmek için 10 günlük hareketli ortalama uyguluyoruz.

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(8,5), dpi=100)

plt.plot(daily_stats_2021['ad_date'], daily_stats_2021['total_spend'].rolling(10).mean(), label='Total spend')

plt.legend()

plt.title('Facebook advertising costs in 2021')

plt.xlabel('Date')
plt.ylabel('Spend, $')

plt.xticks(list(daily_stats_2021['ad_date'])[::100])

plt.show()
```

<img src="https://github.com/huseyinkedik/Facebook_Reklam_-Veri_Analizi/blob/main/Facebook%20advertising%20costs%20in%202021.png">

Bu grafik, her gün için yapılan reklam harcamalarının 10 günlük ortalamasını gösterir. Hareketli ortalama kullanmamızın sebebi, günlük dalgalanmaları biraz daha yumuşatarak genel eğilimi daha net bir şekilde görebilmek. Bu sayede, reklam harcamalarının hangi dönemlerde arttığını ya da azaldığını kolayca fark edebiliriz.

---

**ROMI’nin Zaman İçindeki Değişimi**   

Harcama miktarları kadar, bu harcamaların geri dönüşü de önemlidir. ROMI, kampanyanın ne kadar verimli olduğunu gösterdiği için, zaman içindeki değişimini analiz etmek gerekir.

```python
plt.figure(figsize=(8,5), dpi=100)

plt.plot(daily_stats_2021['ad_date'], daily_stats_2021['romi'].rolling(10).mean(), label='Total spend')

plt.legend()

plt.title('Facebook Advertising ROMI in 2021')

plt.xlabel('Date')
plt.ylabel('ROMI')

plt.xticks(list(daily_stats_2021['ad_date'])[::60])

plt.show()
```

<img src="https://github.com/huseyinkedik/Facebook_Reklam_-Veri_Analizi/blob/main/Facebook%20Advertising%20ROMI%20in%202021.png">

Bu grafikte, 2021 yılı boyunca ROMI’nin nasıl değiştiğini görebiliyoruz. Yine 10 günlük hareketli ortalama kullanarak, dalgalanmaları yumuşatıp genel eğilimleri anlamaya çalışıyoruz. Eğer ROMI belirli bir dönemde artıyorsa, o dönem harcamaların geri dönüşünün daha verimli olduğu anlamına gelir. Bu grafik, hangi dönemlerde reklamların daha verimli olduğunu anlamak için önemli bir göstergedir.

---

**Hareketli Ortalama Neden Kullanılıyor?**   

Grafiklerde **hareketli ortalama** (moving average) kullanmamızın sebebi, ham verideki günlük dalgalanmaları yumuşatmak ve daha genel bir eğilimi ortaya çıkarmaktır. Günlük harcamalar veya ROMI (Return on Marketing Investment) bazı günlerde çok ani değişiklikler gösterebilir. Bu tür ani değişimler bazen geçici olabilir ve büyük resme bakıldığında anlamlı olmayabilir. 

Hareketli ortalama kullanarak, her günün verisini, çevresindeki (örneğin 10 günlük) verilerle ortalama alıyoruz. Bu da, grafikte gördüğümüz eğilimin daha düzgün ve anlaşılır olmasını sağlıyor. Özellikle büyük reklam kampanyaları analiz edilirken, bu tür eğilimleri gözden kaçırmamak çok önemlidir.

---

**Zaman Serisi Verilerinde Doğru Görselleştirme**   

Zaman serisi verilerinde görselleştirme yaparken, tarihler arasındaki boşlukları ve veri sayısını göz önünde bulundurmak önemlidir. Özellikle binlerce veri noktası olan bir veri setiyle çalışıyorsanız, çok fazla tarih etiketi kullanmak grafiği sıkışık ve okunamaz hale getirebilir. 

```python
plt.xticks(list(daily_stats_2021['ad_date'])[::100])
```

Burada, her 100 günde bir tarih etiketi ekleyerek grafiği daha temiz ve okunabilir hale getiriyoruz. Bu adım özellikle uzun zaman aralıklarına sahip veri setlerinde büyük önem taşır. Kullanıcı grafiğe baktığında, zaman serisini net bir şekilde görebilmeli ve kampanyanın nasıl bir performans gösterdiğini anlayabilmelidir.

---

**ROMI’nin İşletme İçin Önemi**  

ROMI, iş dünyasında reklam performansını anlamak için kullanılan en kritik metriklerden biridir. Yatırım getirisi (ROI) gibi düşünülse de, ROMI özellikle pazarlama harcamalarına odaklanır. Bir reklam kampanyasında yapılan her harcamanın, işletmeye ne kadar kazandırdığını bilmek, pazarlama stratejilerini şekillendirmek açısından son derece değerlidir.

Örneğin, ROMI’nin 1’in üzerinde olduğu günlerde, yapılan reklam harcaması, elde edilen gelirin üzerinde bir geri dönüş sağlamış demektir. Eğer ROMI 1'in altındaysa, bu harcamaların karşılığında yeterli değer elde edilemiyor demektir. Bu durum, kampanya optimizasyonu veya harcama stratejilerinde değişiklik yapmayı gerektirebilir.

Bu analiz, özellikle reklam bütçelerinin sıkı takip edildiği büyük işletmeler için vazgeçilmez bir araçtır. Ayrıca, birden fazla kampanyayı karşılaştırmak ve hangilerinin daha verimli olduğunu anlamak için de kullanılır.

---

**Alternatif Metrikler ve Derinlemesine Analiz**   

ROMI haricinde başka metrikler de analiz sürecinde kullanılabilir. Örneğin, **CTR** (Click-Through Rate), yani reklam tıklanma oranı, kampanyanın çekiciliği hakkında önemli bilgiler sunabilir. Bir kampanyada CTR ne kadar yüksekse, o kadar çok kişi reklamla etkileşimde bulunmuş demektir. Aynı şekilde **CPM** (Cost Per Mille - Bin Gösterim Başına Maliyet) ise kampanyanın maliyet verimliliğini gösteren bir metriktir.

Bu gibi metrikleri hesaba katarak reklam kampanyalarını daha detaylı analiz etmek, stratejilerin uzun vadede daha başarılı olmasını sağlayabilir. Hangi metriklerin iş hedefleriyle uyumlu olduğunu anlamak, doğru optimizasyon kararları almak açısından çok önemlidir.

---

**Sezonluk Etkiler ve Kampanya Dönemleri**   

Veri analizinde, reklam harcamalarının ve ROMI’nin dönemsel olarak nasıl değiştiğini anlamak da büyük önem taşır. Örneğin, belirli kampanya dönemlerinde (Black Friday, yılbaşı gibi) reklam harcamalarının ve ROMI’nin nasıl değiştiği, mevsimsel trendleri anlamada önemli bir role sahiptir. 2021 yılına baktığımızda, pandeminin etkisiyle dijital pazarlamanın daha yoğun kullanıldığı bir dönem olduğunu göz önünde bulundurabiliriz. 

Grafiklerde bu tür zirve noktaları fark edilebilir. Örneğin, Black Friday gibi özel alışveriş dönemlerinde harcamalar artabilir, aynı zamanda bu artışların geri dönüşleri de (ROMI) değişkenlik gösterebilir. İşte bu tür zaman dilimlerini analiz ederek işletmeler, bütçelerini daha etkili bir şekilde kullanabilir ve mevsimsel fırsatlardan daha fazla yararlanabilirler.

---

**Sonuçlar ve Gelecek İçin İpuçları**  

Son olarak, bu analizlerden çıkarılacak birkaç önemli nokta var. Veriyi anlamak ve doğru şekilde yorumlamak, sadece geçmiş performansı incelemek için değil, gelecekteki stratejileri planlamak için de çok önemlidir. Örneğin:

- **Yüksek ROMI’li Dönemler**: Hangi dönemlerde ROMI’nin yüksek olduğunu belirlemek, gelecekte benzer stratejiler uygulamak açısından faydalıdır.
- **Harcamaların Yoğun Olduğu Dönemler**: Harcamaların çok yoğun olduğu dönemlerde ROMI'nin düşük olduğu görülüyorsa, bütçe optimizasyonu yapılabilir.
- **Sezonluk Etkiler**: Belirli dönemlerde reklam verimliliği artabilir. Bu dönemleri belirleyip gelecekteki kampanyalar için planlama yapmak, daha başarılı pazarlama kampanyaları oluşturmanıza yardımcı olabilir.


### 2. Verileri kampanya adına göre gruplayarak iki grafik oluşturun:


**Kampanyalar Bazında Harcamaların ve Değerin Gruplandırılması**  

Reklam kampanyalarını tek tek analiz etmek, hangi kampanyanın ne kadar harcama yaptığı ve bu harcamalardan ne kadar geri dönüş aldığı konusunda önemli bilgiler sağlar. Kampanya bazında analiz yapmak, genel stratejinin hangi kampanyalar tarafından daha fazla desteklendiğini anlamamıza yardımcı olur. İşte bu nedenle, önce veriyi kampanya isimlerine göre gruplandırıyoruz.

```python
campaign_stats = df.groupby('campaign_name')[['total_spend', 'total_value']].sum()
```

Bu kod parçası, her kampanyanın toplam reklam harcamasını (`total_spend`) ve toplam elde ettiği değeri (`total_value`) toplar. Bu, her kampanyanın ne kadar bütçe harcadığını ve bu bütçenin karşılığında ne kadar değer (gelir, satış ya da başka bir metrik) elde ettiğini gösteren bir tablo oluşturur.

Bu adım, hangi kampanyaların daha fazla harcama yaptığını görmek ve hangi kampanyaların daha yüksek değer sağladığını karşılaştırmak açısından önemlidir. Reklam bütçesinin nerede daha etkin kullanıldığına dair önemli ipuçları sağlar.

---

**Kampanyalar İçin ROMI Hesaplaması**  

Reklam kampanyalarının başarısını değerlendirmenin bir diğer yolu, bu harcamaların geri dönüşünü ölçen ROMI (Return on Marketing Investment) değerini hesaplamaktır. ROMI, her kampanya için ne kadar geri dönüş sağlandığını gösteren en kritik metriklerden biridir.

```python
campaign_stats['romi'] = campaign_stats['total_value'] / campaign_stats['total_spend']
```

Bu satır, her kampanya için ROMI değerini hesaplar. Daha önce olduğu gibi, formül şu şekildedir:
\[ \text{ROMI} = \frac{\text{toplam değer}}{\text{toplam harcama}} \]

Bu hesaplama, kampanyaların bütçe karşılığında ne kadar değer ürettiğini ortaya koyar. Eğer bir kampanyanın ROMI’si 1’in üzerindeyse, yapılan harcamanın üstünde bir geri dönüş sağlanmıştır demektir. Bu da o kampanyanın başarılı olduğu anlamına gelir. Aksi halde, yani ROMI 1'in altındaysa, harcamalar yeterince verimli olmamış demektir.

---

**Verinin Yeniden Şekillendirilmesi**  

Veri üzerinde çeşitli işlemler yaptıktan sonra, analiz sürecini daha da kolaylaştırmak için veriyi yeniden şekillendirmemiz gerekir. Bu adımda kampanya isimlerini indeks olarak tutmak yerine, onları bir sütun haline getiriyoruz.

```python
campaign_stats = campaign_stats.reset_index()
```

Bu kod, kampanya isimlerini (`campaign_name`) bir sütun olarak ekler. Böylece, kampanya bazında oluşturduğumuz veriyi grafikler üzerinde daha rahat analiz edebilir ve görselleştirebiliriz.

---

**Kampanyalara Göre Reklam Harcamalarının Görselleştirilmesi**  

Reklam harcamalarının hangi kampanyalar üzerinde yoğunlaştığını anlamak için bir bar grafiği kullanmak oldukça etkili bir yöntemdir. Bar grafikleri, kampanyaların ne kadar bütçe kullandığını hızlıca görmemizi sağlar.

```python
import seaborn as sns

sns.set_theme()

sns.barplot(x='total_spend', y='campaign_name', data=campaign_stats, hue="campaign_name")
```

<img src="https://github.com/huseyinkedik/Facebook_Reklam_-Veri_Analizi/blob/main/1.png">

Bu grafik, her kampanyanın ne kadar harcama yaptığını görsel olarak ifade eder. Burada `x` ekseninde toplam harcama (`total_spend`), `y` ekseninde ise kampanya isimleri (`campaign_name`) yer alır. **Seaborn** kütüphanesi kullanarak oluşturduğumuz bu grafik, harcamaların kampanyalara göre nasıl dağıldığını net bir şekilde gösterir.

Grafikte, her kampanya için ayrı bir renk tonu kullanarak kampanyaların görsel olarak ayırt edilmesi sağlanır. Bu sayede, hangi kampanyaların daha fazla bütçe kullandığını ve diğerlerine göre nasıl bir harcama dağılımı olduğunu kolayca gözlemleyebiliriz.

---

**Kampanyalara Göre ROMI’nin Görselleştirilmesi**   

Harcamalardan sonra, ROMI’yi de analiz etmek oldukça önemlidir. Bir kampanya ne kadar fazla harcama yaparsa yapsın, eğer geri dönüşü düşükse o kampanyanın verimli olmadığı anlaşılır. Bu yüzden her kampanyanın ROMI değerini karşılaştırmak, stratejik kararlar için kritik bir adımdır.

```python
sns.barplot(x='romi', y='campaign_name', data=campaign_stats, color="r")
```

<img src="https://github.com/huseyinkedik/Facebook_Reklam_-Veri_Analizi/blob/main/2.png">


Bu grafik, kampanyaların ROMI değerlerini gösterir. **Kırmızı renk** (color="r") kullanarak, her kampanyanın ne kadar geri dönüş sağladığını görsel olarak ifade ediyoruz. Yatay eksende ROMI değeri (`romi`), dikey eksende ise kampanya isimleri (`campaign_name`) yer alır.

Buradaki amaç, hangi kampanyaların harcadıkları bütçeyi verimli kullanıp kullanmadığını görsel olarak analiz etmektir. Eğer bir kampanyanın ROMI değeri diğerlerine göre yüksekse, o kampanya daha verimli bir geri dönüş sağlamış demektir. Bu grafik, işletmenin gelecekteki pazarlama stratejilerini şekillendirmek için hangi kampanyalara daha fazla odaklanması gerektiği konusunda önemli ipuçları sunar.

---

**Görselleştirmenin İşletme İçin Önemi**  

Görselleştirme, veriyi anlamanın en etkili yollarından biridir. Kampanyaların harcamalarını ve ROMI’lerini grafiklerle sunmak, yöneticilerin ve pazarlama ekiplerinin hızlı ve doğru kararlar almasına yardımcı olur. Özellikle birçok kampanya yürütülüyorsa, her kampanyanın performansını detaylı analiz etmek zor olabilir. Ancak bu tür görseller sayesinde, hangi kampanyaların daha fazla bütçe harcadığı ve hangi kampanyaların daha verimli geri dönüş sağladığı net bir şekilde ortaya konabilir.

Özetle:
- **Reklam Harcamaları Grafiği**, hangi kampanyaların daha fazla bütçe kullandığını gösterir.
- **ROMI Grafiği**, kampanyaların bütçe karşılığında ne kadar değer sağladığını görsel olarak sunar.

Bu iki grafik, kampanya bazlı reklam analizlerinde en temel iki metrik olan harcama ve geri dönüşün nasıl optimize edilebileceği konusunda işletmelere yol gösterir.

---



### 3. Her kampanyada günlük ROMI'nin dağılımını box plot kullanarak belirleyin:


**Kampanyalar Bazında Günlük ROMI’nin Dağılımı**  

Her kampanyanın genel ROMI'sini analiz etmek, bize kampanyaların ortalama performansı hakkında genel bir fikir verir. Ancak, kampanyalar içindeki günlük ROMI değerlerinin nasıl dağıldığını bilmek de çok önemli olabilir. Her günün performansı farklı olabilir ve bazı günlerde büyük sapmalar görülebilir. Bu gibi durumları anlamanın en iyi yollarından biri de **box plot** kullanmaktır.

Box plot, veri setinin dağılımını görsel olarak sunar ve veri setinde medyanı, alt ve üst çeyrekleri, dış değerleri (outliers) açık bir şekilde görmemizi sağlar. Bu, kampanyalardaki performansın ne kadar tutarlı olduğunu ve hangi günlerde beklenmedik sapmalar olduğunu anlamamızı kolaylaştırır.

---

**Box Plot ile ROMI Dağılımını Görselleştirme**  

Şimdi, kampanyalardaki günlük ROMI’nin nasıl bir dağılıma sahip olduğunu analiz etmek için bir **box plot** oluşturalım. Bu grafikte, her kampanyadaki günlük ROMI değerlerinin yayılımını göreceğiz. Box plot, her kampanya için ROMI değerlerini alt çeyrek (Q1), üst çeyrek (Q3), medyan ve dış değerler (outliers) olarak ayırır.

```python
plt.figure(figsize=(15,5), dpi=100)

sns.boxplot(x="campaign_name", y="romi", data=df, hue = "campaign_name" )
```
<img src="https://github.com/huseyinkedik/Facebook_Reklam_-Veri_Analizi/blob/main/3.png">


**Adım Adım Açıklama:** 

- **`plt.figure(figsize=(15,5), dpi=100)`**: İlk olarak, grafik boyutunu ayarlıyoruz. Burada 15x5 bir figür oluşturuyoruz, yani grafiğin yatay genişliği fazla çünkü birden fazla kampanya ismini görüntülemek için geniş bir alana ihtiyacımız var. Ayrıca, yüksek çözünürlüklü bir grafik elde etmek için DPI değerini 100 olarak ayarlıyoruz.
  
- **`sns.boxplot(x="campaign_name", y="romi", data=df, hue="campaign_name")`**: Burada **Seaborn** kütüphanesini kullanarak kampanyalar bazında ROMI’nin box plotunu oluşturuyoruz. 
    - `x="campaign_name"`: X ekseninde kampanya isimlerini (campaign_name) gösteriyoruz. Her kampanya ayrı bir kategorik değişken olarak temsil ediliyor.
    - `y="romi"`: Y ekseninde ROMI değerlerini yerleştiriyoruz. Bu eksen kampanyalardaki günlük ROMI'nin nasıl değiştiğini görmemizi sağlar.
    - `data=df`: Veri seti olarak daha önce tanımladığımız `df` (Facebook reklam verisi) kullanılıyor.
    - `hue="campaign_name"`: Kampanyaları farklı renklerle ayırarak grafiği görsel olarak daha net hale getiriyoruz.

---

**Box Plot’un İşlevi ve Yorumlanması**  

**Box plot** (kutu grafiği) bize aşağıdaki bilgileri sağlar:

- **Medyan Çizgisi**: Her kutunun ortasındaki çizgi, kampanyanın ROMI değerlerinin medyanını gösterir. Medyan, veri setindeki merkezi eğilimi temsil eder.
  
- **Kutular (Q1 ve Q3)**: Kutunun alt kısmı **alt çeyrek (Q1)**, üst kısmı ise **üst çeyrek (Q3)** olarak adlandırılır. Bu iki değer, verinin %50'sinin (orta kısmı) bu kutu içinde olduğunu gösterir. Eğer kutu uzun ise, kampanya içindeki ROMI dağılımı geniş bir yelpazeye yayılmıştır. Kutu dar ise, dağılım daha tutarlıdır.

- **Dış Değerler (Outliers)**: Kutu dışındaki noktalara dikkat edin, bunlar **dış değerler** olarak adlandırılır. Kampanya içindeki bazı günlerde ROMI'nin çok yüksek veya çok düşük olduğunu gösterir. Bu dış değerler genellikle beklenmedik performanslardır, örneğin bazı günlerde aşırı kazanç sağlanmış olabilir ya da tam tersi başarısızlık yaşanmış olabilir.

Bu grafikte her bir kampanyanın ROMI'sinin genel olarak ne kadar değişkenlik gösterdiğini, hangi kampanyaların daha tutarlı olduğunu ve hangi kampanyaların daha fazla sapma gösterdiğini görmüş oluruz. Örneğin, bir kampanyanın kutusu diğerlerine göre daha uzun ve çok fazla dış değeri varsa, o kampanyanın performansı günlük bazda daha fazla dalgalanma göstermiş demektir.

---

**ROMI’nin Günlük Dağılımının İşletmeye Etkisi**  

Bu grafiği kullanarak her kampanyanın günlük performansını analiz etmek, işletme açısından son derece önemli stratejik bilgiler sunar. Şu noktalar özellikle dikkat çeker:

- **Performans Tutarlılığı**: Eğer bir kampanyanın box plotu dar ve medyan değeri yüksekse, o kampanya gün be gün tutarlı ve başarılı bir performans sergiliyor demektir. Bu tür kampanyalar genellikle daha güvenilirdir.
  
- **Beklenmedik Performanslar**: Kampanyalar içindeki dış değerler, beklenmedik başarılar ya da başarısızlıklar hakkında bilgi verir. Örneğin, bir kampanyanın çok yüksek bir dış değeri varsa, o gün yapılan reklam harcamasının çok yüksek bir geri dönüş sağladığını görebiliriz. Aynı şekilde, düşük dış değerler de başarısız geçen günleri temsil eder.

- **Kampanyalar Arasındaki Karşılaştırmalar**: Her kampanyanın ROMI dağılımını bu grafikte karşılaştırarak, hangi kampanyaların genel olarak daha verimli olduğunu hızlıca tespit edebiliriz. Özellikle hangi kampanyaların daha fazla dış değere sahip olduğu, hangi kampanyaların daha dengeli bir performans sergilediği gibi önemli sonuçlar çıkarılabilir.

---

**Sonuç ve Öneriler**  

Bu analizle, her kampanyanın günlük ROMI dağılımını detaylıca inceledik. Box plot ile görselleştirilen veriler, hem kampanyaların genel performansını hem de gün gün ne kadar tutarlı olduklarını anlamamıza yardımcı oldu. Özellikle dış değerler ve çeyrekler arası farklar, hangi kampanyalarda stratejik düzeltmeler yapılabileceğine dair ipuçları sunabilir. Bu dağılımı göz önünde bulundurarak, performansı düşük veya dengesiz kampanyalar için optimizasyon stratejileri geliştirilebilir.


### 4. Facebook_ads_data.csv tablosundaki ROMI değerlerinin dağılımını gösteren bir histogram oluşturun.


**ROMI Değerlerinin Dağılımını Anlamak**  

Reklam kampanyalarının verimliliğini ölçmek için kullandığımız en önemli metriklerden biri olan **ROMI** değerinin dağılımını anlamak, genel olarak pazarlama yatırımlarının ne kadar geri dönüş sağladığını gösterebilir. Kampanya düzeyinde ROMI analizini yaptığımız gibi, şimdi tüm kampanyaların ROMI değerlerini bir arada inceleyerek, genel performansın nasıl dağıldığını göreceğiz.

Bu dağılımı anlamanın en etkili yollarından biri ise **histogram** kullanmaktır. Histogram, bir veri setindeki değerlerin hangi aralıklarda yoğunlaştığını gösteren bir grafiktir. ROMI dağılımını histogram ile inceleyerek, verinin hangi bölümlerinde daha fazla toplandığını ve olası uç noktaları kolayca tespit edebiliriz.

---

**ROMI Değerlerinin Histogramı**  

Şimdi, verisetindeki **ROMI değerlerinin dağılımını** görmek için bir histogram oluşturacağız. Histogram bize, hangi ROMI aralıklarında daha fazla kampanyanın yer aldığını gösterecek. 

```python
sns.displot(data=df, x="romi")
```

<img src="https://github.com/huseyinkedik/Facebook_Reklam_-Veri_Analizi/blob/main/4.png">


**Adım Adım Açıklama:**  

- **`sns.displot(data=df, x="romi")`**: Burada **Seaborn** kütüphanesi ile ROMI değerlerinin dağılımını bir histogram ile görselleştiriyoruz.
  - `data=df`: Analiz yapacağımız veri seti `df`, yani Facebook reklam verileri. Bu veri seti içerisindeki **ROMI** değerlerini kullanarak dağılımı göstereceğiz.
  - `x="romi"`: X ekseninde **ROMI** değerleri yer alıyor. Bu eksen ROMI’nin hangi aralıklarda yoğunlaştığını gösteriyor.

Histogram, tüm veri setindeki ROMI değerlerini çeşitli aralıklara (buckets) bölerek, bu aralıklarda kaç tane veri noktası olduğunu gösterir. Bu sayede, ROMI’nin genel dağılımını kolayca analiz edebiliriz.

---

**Histogramın İşlevi ve Yorumlanması**  

**Histogram**, bir veri setindeki değerlerin hangi aralıklarda yoğunlaştığını göstermek için kullanılan etkili bir araçtır. ROMI dağılımı açısından histogramın işlevi şu şekildedir:

- **Yoğunluk Gösterimi**: ROMI’nin hangi aralıklarda daha fazla yoğunlaştığını hızlıca görebiliriz. Eğer belirli ROMI aralıklarında daha fazla kampanya varsa, bu aralıkta çok sayıda başarılı ya da başarısız kampanyanın olduğunu gösterir.
  
- **Uç Noktalar (Outliers)**: Histogramın sağ veya sol uçlarında yer alan az sayıda veri, ROMI değerlerinin aşırı düşük veya aşırı yüksek olduğu nadir durumları temsil eder. Bu noktalar genellikle beklenmedik başarılar veya başarısızlıkları gösterir.

- **Normal Dağılım veya Çarpıklık**: Verinin şekline bakarak, ROMI dağılımının normal mi yoksa çarpık mı olduğunu anlayabiliriz. Eğer histogram simetrik bir dağılıma sahipse, ROMI değerlerinin ortalama etrafında yoğunlaştığını görebiliriz. Eğer dağılım sağa ya da sola çarpıksa, ROMI değerlerinin büyük kısmının düşük ya da yüksek aralıklarda toplandığını anlarız.

---

**ROMI Dağılımının İşletmeye Etkisi**  

Veri setindeki ROMI değerlerinin genel dağılımı, işletmenin pazarlama yatırımlarının ne kadar verimli olduğuna dair genel bir bakış sunar. Histogramda görülen dağılım, bize şu açılardan yardımcı olur:

- **ROMI'nin Ortalaması**: Eğer ROMI değerlerinin çoğu 1'in üzerindeyse, genel olarak kampanyalar iyi bir geri dönüş sağlıyor demektir. Aksine, ROMI’nin büyük kısmı 1’in altındaysa, işletme pazarlama yatırımlarından beklenen geri dönüşü alamıyor olabilir.
  
- **Dağılımın Yayılımı**: ROMI değerlerinin geniş bir aralığa yayılması, kampanyalar arasında büyük performans farklılıkları olduğunu gösterir. Bu durumda, bazı kampanyaların aşırı başarılı, bazılarının ise başarısız olduğu sonucuna varabiliriz. Eğer ROMI dağılımı dar bir aralıkta toplanmışsa, kampanyalar arasında daha tutarlı bir performans olduğunu anlarız.

- **Performans Dengesizliği**: Histogramdaki dış noktalara (outliers) dikkat etmek gerekir. Eğer ROMI değerlerinin bir kısmı çok yüksek ya da çok düşük seviyelerde yoğunlaşmışsa, işletme bazı kampanyalarda büyük başarılar veya büyük kayıplar yaşamış olabilir.

---

**Görselleştirmenin Sonuçları ve Stratejik Kararlar**  

ROMI değerlerinin histogramı, işletme için çok değerli stratejik bilgiler sunar. Pazarlama ekipleri bu grafiği kullanarak:

- **Kampanya Performanslarını Optimize Etme**: Eğer ROMI değerlerinin büyük kısmı düşükse, pazarlama stratejileri gözden geçirilerek, bütçenin daha verimli kullanılması sağlanabilir.
  
- **Başarılı Kampanyaları Ön Plana Çıkarma**: Histogramda yüksek ROMI değerlerinin yoğunlaştığı bölgeleri belirleyerek, hangi kampanyaların daha iyi performans gösterdiği anlaşılabilir ve bu kampanyalar üzerinde daha fazla durulabilir.

- **Zayıf Performans Gösteren Kampanyaları Revize Etme**: Düşük ROMI değerlerine sahip kampanyalar tespit edilerek, bu kampanyalar üzerinde revizyonlar yapılabilir. Böylece, bütçenin daha etkin kullanılması sağlanır.

---


### 5. Facebook_ads_data.csv tablosundaki tüm sayısal göstergeler arasındaki korelasyonun ısı haritasını çıkarın.

**Korelasyon Nedir ve Neden Önemlidir?**  

**Korelasyon**, iki değişken arasındaki ilişkinin yönünü ve gücünü ölçen bir istatistiksel göstergedir. Eğer iki değişken arasında pozitif bir korelasyon varsa, bir değişken arttıkça diğerinin de artma eğiliminde olduğunu gösterir. Negatif korelasyon ise bir değişken artarken diğerinin azaldığını gösterir. Korelasyon katsayısı, -1 ile +1 arasında bir değer alır:
- **+1**: Mükemmel pozitif korelasyon
- **-1**: Mükemmel negatif korelasyon
- **0**: Hiçbir korelasyon yok, yani değişkenler arasında ilişki yok

Reklam verileri ile çalışırken korelasyonu anlamak, pazarlama stratejilerinin etkinliğini anlamada büyük fayda sağlar. Özellikle, hangi faktörlerin birbirini nasıl etkilediğini görmek, hangi alanlarda iyileştirme yapılması gerektiğini anlamaya yardımcı olur.

---

**Sayısal Göstergeler Arasındaki Korelasyonu Görselleştirme**  

Facebook reklam verisindeki tüm sayısal göstergeler arasındaki korelasyonu analiz etmek için bir **ısı haritası** kullanacağız. Bu harita, değişkenlerin birbirleriyle olan ilişkilerini renkler ile gösterir. Daha güçlü pozitif korelasyonlar koyu renkte, daha zayıf ya da negatif korelasyonlar ise açık renkte görünür.

```python
cmap = sns.diverging_palette(230, 20, as_cmap=True)
sns.heatmap(df.corr(numeric_only=True), annot=True, fmt='.0%', cmap= 'crest_r')
```

<img src="https://github.com/huseyinkedik/Facebook_Reklam_-Veri_Analizi/blob/main/5.png">


**Adım Adım Açıklama:**  

- **`df.corr(numeric_only=True)`**: Bu kısım, veri setindeki tüm **sayısal değişkenler** arasındaki korelasyonu hesaplar. Korelasyon, her iki sayısal değişken arasında hesaplanan ilişki katsayısıdır. 
  - `numeric_only=True`: Sadece sayısal veriler kullanılarak korelasyon hesaplanır. Bu, veri setindeki kategorik değişkenleri göz ardı eder.

- **`sns.heatmap(df.corr(numeric_only=True), annot=True, fmt='.0%', cmap='crest_r')`**: 
  - **`sns.heatmap`**: Korelasyon matrisi, **Seaborn** kütüphanesindeki `heatmap` fonksiyonu ile görselleştiriliyor. Isı haritası, korelasyonları renklerle gösterir.
  - **`annot=True`**: Her hücredeki korelasyon değerinin sayısal olarak gösterilmesini sağlar. Bu sayede, görsel olarak renkler ile birlikte her iki değişkenin korelasyonunu yüzde cinsinden açıkça görebiliriz.
  - **`fmt='.0%'`**: Korelasyon değerlerinin yüzde formatında gösterilmesini sağlar.
  - **`cmap='crest_r'`**: Isı haritası için kullanılan renk paletidir. Burada `crest_r`, renk tonlarının koyudan açığa doğru gradyan oluşturmasını sağlar.

---

**Korelasyon Isı Haritasının Yorumlanması**  

Isı haritası bize, hangi göstergelerin birbiriyle pozitif ya da negatif korelasyona sahip olduğunu gösterir. Aşağıdaki adımlarla bu grafiği inceleyebiliriz:

- **Pozitif Korelasyon (Koyu Renk)**: Eğer iki gösterge arasında koyu renkler görüyorsanız, bu iki gösterge arasında **pozitif bir ilişki** olduğunu gösterir. Örneğin, reklam harcamaları arttıkça, toplam tıklama sayısının da artması beklenir. Bu tür ilişkiler, başarılı kampanyaların temel göstergelerinden biridir.

- **Negatif Korelasyon (Açık Renk)**: Isı haritasında daha açık renkler, **negatif bir korelasyon** olduğunu gösterir. Yani, bir değişken artarken diğeri azalma eğilimindedir. Bu, bazı olumsuz ilişkilere işaret edebilir. Örneğin, gösterim sayısının artmasıyla birlikte tıklama başına maliyetin azalması olası bir negatif korelasyon olabilir.

- **Korelasyonu Zayıf Göstergeler (Sıfıra Yakın Değerler)**: Eğer iki gösterge arasında korelasyon değeri sıfıra yakınsa (rengi orta tonda), bu, iki gösterge arasında **çok zayıf bir ilişki** olduğunu veya hiç bir ilişkinin olmadığını gösterir.

---

**"Total_value" ile Korelasyon**  

Isı haritasındaki önemli analizlerden biri de **"total_value"** değişkeninin diğer değişkenlerle olan korelasyonunu incelemektir. **Total_value**, genellikle kampanyanın ne kadar gelir sağladığını veya değer yarattığını gösterir. Bu yüzden, hangi faktörlerin toplam değeri en çok etkilediğini anlamak, pazarlama stratejileri açısından büyük önem taşır.

Isı haritasında **"total_value"** ile diğer göstergeler arasındaki korelasyonlara bakıldığında, şu sonuçlar çıkarılabilir:
- **Total_spend ile Korelasyon**: Eğer "total_spend" ile "total_value" arasında yüksek pozitif bir korelasyon varsa, bu, harcama arttıkça elde edilen değerlerin de arttığını gösterir. Ancak, bu korelasyonun gücüne bakmak önemlidir. Eğer çok yüksekse, daha fazla harcama yaparak değeri arttırmak mümkün olabilir.
  
- **Total_clicks ile Korelasyon**: Tıklama sayısı ile elde edilen toplam değer arasında pozitif bir ilişki beklenir. Eğer bu korelasyon yüksekse, tıklama başına maliyetin optimize edilmesiyle daha fazla değer yaratmak mümkün olabilir.

- **Total_impressions ile Korelasyon**: Gösterim sayısı ile toplam değer arasında korelasyon düşük olabilir. Bu, her gösterimin satışa veya tıklamaya dönüşmediğini gösterir.

---

**Korelasyon Analizinin İşletmeye Katkısı**  

Bu korelasyon analizi, işletmelerin reklam kampanyalarını optimize etmeleri açısından son derece önemlidir. Korelasyon haritasına bakarak şu stratejiler geliştirilebilir:

- **Yüksek Korelasyonlu Faktörleri Optimize Etme**: Eğer belirli faktörler (örneğin, "total_spend") ile "total_value" arasında yüksek pozitif korelasyon varsa, bu faktörlere daha fazla odaklanılabilir ve yatırım yapılabilir. Bu, kampanya performansını arttırabilir.
  
- **Düşük ve Negatif Korelasyonlu Faktörleri İyileştirme**: Eğer bazı göstergeler "total_value" ile zayıf ya da negatif korelasyon gösteriyorsa, bu faktörler gözden geçirilmeli ve stratejik olarak iyileştirilmelidir. Örneğin, tıklama başına maliyetin yüksek olduğu durumlarda bu maliyetleri azaltacak yollar aranabilir.

- **Yatırımın Yönlendirilmesi**: Korelasyon haritası, hangi alanlara daha fazla yatırım yapılması gerektiği konusunda yönlendirme sağlar. Yüksek korelasyona sahip göstergeler, reklam stratejisinde önceliklendirilmelidir.

---

**Sonuç ve Öneriler**  

Isı haritası yardımıyla yapılan korelasyon analizi, verisetindeki tüm sayısal göstergeler arasındaki ilişkileri görselleştirmemizi sağladı. Özellikle **"total_value"** değişkeninin diğer göstergelerle olan ilişkisi, hangi faktörlerin reklam başarısına daha çok katkı sağladığını ortaya koyar. Korelasyonu yüksek faktörler üzerinde yoğunlaşarak, reklam bütçesi daha verimli kullanılabilir ve kampanya sonuçları optimize edilebilir.

---

### 6. Değişkenler arasındaki ilişkiyi görselleştirmek için "total_spend" ve "total_value" verileri üzerinden oluşturulan doğrusal regresyonlu bir nokta grafiği oluşturun.

**Doğrusal Regresyon Nedir?**  

**Doğrusal regresyon**, iki değişken arasındaki ilişkiyi analiz etmek için kullanılan bir istatistiksel yöntemdir. Amaç, bağımlı değişkeni (örneğin, toplam değer **"total_value"**) bağımsız bir değişken (örneğin, toplam harcama **"total_spend"**) ile açıklayabilmektir. Bu yöntemde, iki değişken arasındaki ilişkiyi en iyi şekilde temsil eden **doğrusal bir çizgi** hesaplanır.

Bu çizgi, veri noktalarının genel eğilimini gösterir. Eğer veri noktaları bu çizgi etrafında yoğunlaşıyorsa, değişkenler arasında güçlü bir ilişki var demektir. Veri noktaları çizgiden uzaksa, ilişki daha zayıf olabilir.

---

**Doğrusal Regresyon ve Nokta Grafiği**  

Şimdi, **"total_spend"** ve **"total_value"** arasındaki ilişkiyi incelemek için bir **scatter plot** (nokta grafiği) oluşturarak üzerine doğrusal regresyon çizgisi ekleyeceğiz. Bu, toplam harcama arttıkça elde edilen toplam değerin nasıl değiştiğini gözlemlememizi sağlayacak.

Bunu yaparken, **Seaborn** kütüphanesindeki **`lmplot()`** fonksiyonunu kullanacağız. Bu fonksiyon, hem noktaları hem de doğrusal regresyon çizgisini aynı grafikte birleştirir.

```python
sns.lmplot(data=df, x="total_spend", y="total_value")
```

<img src="https://github.com/huseyinkedik/Facebook_Reklam_-Veri_Analizi/blob/main/6.png">


**Adım Adım Açıklama:**  

- **`sns.lmplot()`**: Bu fonksiyon, iki değişken arasındaki ilişkiyi hem bir nokta grafiği (scatter plot) ile hem de bir doğrusal regresyon çizgisi ile görselleştirir.
  
  - **`data=df`**: Kullanacağımız veri seti, `df` (Facebook reklam verileri). Bu veri seti içerisindeki **toplam harcama** ve **toplam değer** verileri üzerinden analiz yapacağız.

  - **`x="total_spend"`**: X ekseninde yer alacak olan değişken, toplam reklam harcamalarıdır (**"total_spend"**). Bu, kampanyalarda yapılan harcamaları temsil eder.

  - **`y="total_value"`**: Y ekseninde yer alacak olan değişken, toplam değerdir (**"total_value"**). Bu, yapılan harcamalar sonucunda elde edilen değeri gösterir.

---

**Grafik Üzerinde İlişkinin İncelenmesi**  

Bu grafikte iki önemli bileşeni analiz edeceğiz: **noktalar (scatter plot)** ve **doğrusal regresyon çizgisi**.

- **Noktalar**: Grafik üzerinde her bir nokta, bir kampanyayı temsil eder. X ekseni boyunca kampanyaya yapılan harcama miktarını, Y ekseni boyunca ise o kampanyadan elde edilen değeri gösterir.
  
- **Doğrusal Regresyon Çizgisi**: Bu çizgi, veri noktalarının genel eğilimini temsil eder. Eğer harcama ile elde edilen değer arasında güçlü bir ilişki varsa, çizgiye yakın bir dağılım görürüz. Çizginin eğimi, harcama arttıkça değerin ne kadar arttığını gösterir.

---

**Sonuçların Yorumlanması**  

Bu grafik sayesinde, **harcama (total_spend)** ile **elde edilen değer (total_value)** arasındaki ilişkiyi net bir şekilde gözlemleyebiliriz.

- **Pozitif İlişki**: Eğer doğrusal regresyon çizgisi yukarıya doğru eğimliyse, bu iki değişken arasında **pozitif bir ilişki** olduğunu gösterir. Yani, harcama arttıkça elde edilen değer de artmaktadır. Pozitif korelasyonun derecesi, bu eğimin dikliğine bağlıdır; eğim ne kadar dikse, ilişki o kadar güçlüdür.
  
- **Negatif İlişki**: Eğer çizgi aşağıya doğru eğimliyse, bu iki değişken arasında **negatif bir ilişki** var demektir. Yani, harcama arttıkça elde edilen değer azalıyor olabilir. Bu tür bir sonuç genellikle istenmeyen bir durumdur.

- **Zayıf veya Güçlü İlişki**: Veri noktalarının doğrusal regresyon çizgisine olan uzaklığı, ilişkinin ne kadar güçlü olduğunu gösterir. Eğer noktalar çizgiye yakınsa, ilişki güçlüdür ve iki değişken arasında tutarlı bir ilişki vardır. Eğer noktalar çizgiden uzaksa, ilişki daha zayıftır ve değişkenler arasındaki bağlantı belirsiz olabilir.

---

**Stratejik Yorumlar**  

Bu tür bir analiz, pazarlama kampanyalarının etkinliğini anlamak açısından büyük fayda sağlar. Harcamalar arttıkça değerin nasıl değiştiğini anlamak, işletmenin reklam stratejilerini optimize etmesine yardımcı olabilir.

- **Yüksek Pozitif İlişki**: Eğer harcama ile elde edilen değer arasında güçlü bir pozitif ilişki varsa, bu, reklama daha fazla yatırım yapmanın mantıklı olabileceğini gösterir. Harcamaların artmasıyla birlikte elde edilen değer de artmaktadır.

- **Düşük veya Negatif İlişki**: Eğer ilişki zayıf ya da negatifse, harcamaların artırılmasının her zaman daha fazla değer getirmediğini gösterir. Bu durumda, harcamalar daha dikkatli bir şekilde yönetilmeli ve kampanyaların performansı optimize edilmelidir.

- **Doğrusal Olmayan İlişkiler**: Bazen, ilişki doğrusal olmayabilir. Bu durumda, harcama ile elde edilen değer arasında karmaşık bir ilişki olabilir. Bu tür durumlarda, farklı analiz yöntemlerine başvurmak gerekebilir.

---

**Pazarlama Stratejilerine Etkisi**  

Bu grafik, pazarlama ekiplerinin stratejik kararlar almasına yardımcı olabilir. Özellikle harcama ve getiriler arasındaki ilişkiyi net bir şekilde gösterdiği için, bütçe planlaması ve kampanya optimizasyonu açısından değerli bilgiler sunar:

- **Bütçe Yönetimi**: Eğer harcamalar ile elde edilen değer arasında güçlü bir ilişki varsa, reklam bütçesi daha verimli bir şekilde dağıtılabilir ve performansın daha tutarlı olması sağlanabilir.
  
- **Kampanya Performansını Anlama**: Kampanyalar arasındaki farklılıkları bu grafikle gözlemleyerek, hangi kampanyaların daha etkili olduğunu ve neden diğer kampanyaların başarılı olmadığını anlamak mümkün olur.

---

**Sonuç ve Öneriler**  

**"total_spend"** ile **"total_value"** arasındaki ilişkiyi doğrusal regresyonlu bir scatter plot ile inceledik. Bu grafik, reklam harcamalarının genel olarak elde edilen değer üzerindeki etkisini analiz etmemize yardımcı oldu. Eğer harcamalar ile değer arasında güçlü bir ilişki varsa, bu stratejiye devam edilebilir. Ancak, zayıf ya da negatif bir ilişki varsa, harcamalar yeniden gözden geçirilmeli ve kampanya performansları optimize edilmelidir.

---

  
Kullanılan veri dosyasını [buradan](https://drive.google.com/file/d/1P51VI_-GaHs8OU4OyHPPWFx70_PY2yxN/view?usp=sharing) indirebilirsin.
