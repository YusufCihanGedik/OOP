# Akademik Makale İnceleme Raporu: Microservice Reference Architecture

> **Öğrenci:** Yusuf Cihan GEDİK <br>
> **Öğrenci No:** Y245060014 <br>
> **Ders:** Nesneye Dayalı Programlama <br>
> **Durum:** ✅ Tamamlandı (%100)

---

## 📋 İçindekiler
1. [Makale Künyesi](#1-makale-künyesi)
2. [Giriş: Problem Tanımı ve Motivasyon](#2-giriş-problem-tanımı-ve-motivasyon)
3. [Metodoloji: Teoriden Pratiğe Geçiş](#3-metodoloji-teoriden-pratiğe-geçiş)
4. [Önerilen Referans Mimarinin Analizi](#4-önerilen-referans-mimarinin-analizi)
5. [Dağıtım ve Operasyonel Görünüm](#5-dağıtım-ve-operasyonel-görünüm)
6. [Vaka Çalışmaları: Teorinin Doğrulanması](#6-vaka-çalışmaları-teorinin-doğrulanması)
7. [Tartışma ve Sonuç](#7-tartışma-ve-sonuç)
8. [Nesneye Dayalı Programlama (OOP) ile İlişkiler](#8-nesneye-dayalı-programlama-oop-ile-ilişkiler)
9. [Kişisel Değerlendirme ve Eleştiri](#9-kişisel-değerlendirme-ve-eleştiri)

---

## 1. Makale Künyesi

| Özellik | Detay |
| :--- | :--- |
| **Makale Adı** | Microservice reference architecture design: A multi-case study |
| **Yazarlar** | Mehmet Söylemez, Bedir Tekinerdogan, Ayça Kolukısa Tarhan |
| **Yayın Yeri** | Software: Practice and Experience (Wiley) |
| **Yıl** | 2024 |
| **DOI** | [10.1002/spe.3241](https://doi.org/10.1002/spe.3241) |
| **İndeks** | SCI-Expanded |
| **İnceleme Tarihi** | 06.12.2024 |

---

## 2. Giriş: Problem Tanımı ve Motivasyon

Yazılım dünyasında projeler büyüdükçe, geleneksel **Monolitik (Monolithic)** mimarilerin hantallığı, ekiplerin hızını kesen en büyük engel haline gelmiştir. Makalede de vurgulandığı üzere, tüm işlevlerin tek bir kod tabanında (single codebase) ve sıkı sıkıya bağlı (tightly coupled) olması şu kritik darboğazları yaratmaktadır:

* **Domino Etkisi:** Küçük bir modüldeki hata (örn: fatura servisi), tüm uygulamanın çökmesine neden olabilmektedir.
* **Ölçekleme Verimsizliği:** Sadece ödeme sistemi yoğunluk yaşıyor olsa bile, monolitik yapıda tüm sunucuyu büyütmek zorunda kalırsınız; bu da ciddi bir kaynak israfıdır.
* **Teknoloji Hapsi (Vendor Lock-in):** Ekip, projeye başladığı programlama diline veya framework'e mahkum kalır; yeni teknolojileri entegre etmek neredeyse imkansızdır.

**Çözüm Arayışı:** Mikroservis Mimarisi (MSA) bu sorunlara ilaç gibi gelse de, endüstride "bu işin standardı nedir?" sorusuna cevap verecek, kapsamlı ve doğrulanmış bir **Referans Mimari** eksikliği bulunmaktadır. Yazarlar bu çalışmada, sadece akademik değil, endüstriyel pratiklerle de örtüşen uygulanabilir bir şablon sunmayı hedeflemiştir.

---

## 3. Metodoloji: Teoriden Pratiğe Geçiş

Yazarlar, masa başında teorik bir model üretmek yerine **Alan Mühendisliği (Domain Engineering)** yöntemlerini kullanarak, sahadaki gerçeklerden beslenen hibrit bir yol izlemiştir:

### A. Devlerin Omuzlarında Yükselmek (Vendor Analysis)
Sadece literatür taranmamış; **AWS, Google Cloud ve Microsoft Azure** gibi sektör devlerinin mimari çözümleri mercek altına alınmıştır.
* **Tespit:** Her sağlayıcı farklı isimlendirmeler kullansa da (örn: AWS Lambda vs Azure Functions), temeldeki yapı taşlarının (API Gateway, Messaging, Discovery) ortak olduğu görülmüştür. Bu sayede önerilen mimari, **platformdan bağımsız (vendor-agnostic)** bir yapıya kavuşmuştur.

### B. Özellik Modellemesi (Feature Modeling)
MSA'nın karmaşık yapısı, bir "Özellik Ağacı" ile sadeleştirilmiştir. Özellikler şu mantıkla sınıflandırılmıştır:
* **Olmazsa Olmazlar (Mandatory):** Service Discovery, Load Balancing.
* **Duruma Göre (Optional):** Circuit Breaker (Hata toleransı gerekiyorsa), Distributed Tracing.
* **Seçenekler (Alternative):** Veri tutarlılığı için SAGA mı yoksa 2PC mi kullanılacak?

---

## 4. Önerilen Referans Mimarinin Analizi

Önerilen mimari, karmaşıklığı yönetmek için sistemi mantıksal parçalara böler.

> **[BURAYA ŞEKİL 3 EKLENECEK: Decomposition View Diyagramı]** > *Şekil 1: Önerilen mimarinin ayrıştırma görünümü.*

### 4.1. Kapı Bekçisi: İletişim Katmanı
Dış dünya ile iç servisler arasındaki temas **API Gateway** üzerinden sağlanır. Bu katman sadece yönlendirme yapmakla kalmaz; kimlik doğrulama ve protokol dönüşümü (HTTPS -> HTTP) gibi yükleri servislerin üzerinden alır.

### 4.2. Veri Yönetimi: En Zorlu Sınav
Monolitik yapıdaki "tek veritabanı" rahatlığı burada yoktur. Her servis kendi veritabanına sahiptir (**Database-per-Service**).
* **Zorluk:** Farklı servislerdeki verilerin tutarlı kalması (Consistency).
* **Çözüm:** Makale, klasik transaction yönetimi yerine **SAGA Pattern** (zincirleme işlemler) ve **Event Sourcing** (olay tabanlı kayıt) kullanılmasını önermektedir.

### 4.3. Dayanıklılık ve Gözlemlenebilirlik
Dağıtık sistemlerde ağ hataları kaçınılmazdır.
* **Circuit Breaker:** Bir servis cevap veremez hale geldiğinde, hatanın tüm sisteme yayılmasını engellemek için "sigortayı attırır" ve trafiği keser.
* **Gözlemlenebilirlik:** Yüzlerce servisin olduğu bir ortamda hatayı bulmak samanlıkta iğne aramaya benzer. Bu yüzden **Distributed Tracing (Dağıtık İzleme)** ve **Merkezi Loglama** mimarinin zorunlu bir parçasıdır.

---

## 5. Dağıtım ve Operasyonel Görünüm

> **[BURAYA ŞEKİL 5 EKLENECEK: Deployment View Diyagramı]** > *Şekil 2: Konteyner tabanlı dağıtım mimarisi.*

Makale, modern dağıtım standartlarını mimariye entegre etmiştir:
* **Konteynerizasyon:** Servislerin Docker gibi teknolojilerle paketlenmesi.
* **Orkestrasyon:** Kubernetes benzeri yapılarla bu konteynerlerin yönetimi, otomatik ölçeklenmesi (Auto-scaling) ve sağlık kontrollerinin yapılması.
* **Bağımsızlık:** Her birim (Loglama, Auth, İş Servisi) birbirinden bağımsız güncellenebilir ve dağıtılabilir.

---

## 6. Vaka Çalışmaları: Teorinin Doğrulanması

Önerilen mimari sadece kağıt üzerinde kalmamış, iki farklı gerçek dünya projesinde test edilmiştir.

### Vaka 1: Ulaşım Yönetim Sistemi (Lojistik)
* **Ortam:** AWS Bulutu.
* **İlginç Bir Mimari Karar:** Ekip, AWS maliyetlerini düşürmek için **API Gateway kullanmamış**, bunun yerine Load Balancer ile doğrudan servislerine erişim vermiştir. Bu, referans mimarinin esnek olduğunu, duruma göre bazı parçaların çıkarılabileceğini gösterir.

### Vaka 2: Uzaktan Ekip Yönetimi (İK)
* **Ortam:** Google Cloud Platform (GCP).
* **Farklılık:** Burada performans kritik olduğu için **Caching (Önbellekleme)** katmanı eklenmiş ve API Gateway aktif olarak kullanılmıştır.

---

## 7. Tartışma ve Sonuç

Yapılan değerlendirmeler (Likert ölçeği anketleri) sonucunda, bu referans mimarinin geliştirici ekiplerin **öğrenme eğrisini düşürdüğü** ve karmaşık kavramları (Service Mesh, Sidecar vb.) daha anlaşılır kıldığı görülmüştür.

**Sonuç olarak;** Bu çalışma, monolitik yapıdan mikroservislere geçişte yaşanan "nereden başlamalıyım?" sorusuna, alan bilgisine dayalı, sistematik ve doğrulanmış bir cevap niteliğindedir.

---

## 8. Nesneye Dayalı Programlama (OOP) ile İlişkiler

Bu makale, OOP prensiplerinin sistem mimarisi seviyesine (Macro-Architecture) taşınmış halidir:

* **Modülerlik ve Encapsulation (Kapsülleme):** Nasıl ki bir sınıfın `private` değişkenlerine dışarıdan erişilemezse, mikroservis mimarisinde de her servis verisini dış dünyadan saklar. Erişim sadece tanımlı API'ler üzerinden olur.
* **Interface Segregation (Arayüz Ayrımı):** Servisler birbirlerinin iç yapısını (veritabanı şemasını) bilmez. Sadece birbirlerine sundukları kontratlar (Interface/API) üzerinden haberleşirler.
* **Single Responsibility (Tek Sorumluluk):** Her mikroservisin sadece tek bir iş alanına (Bounded Context) odaklanması, OOP'deki SRP ilkesinin mimari karşılığıdır.
* **Proxy ve Observer Desenleri:** API Gateway bir **Proxy** gibi davranırken, asenkron iletişimdeki mesaj kuyrukları **Observer (Pub/Sub)** desenini uygular.

---

## 9. Kişisel Değerlendirme ve Eleştiri

Makaleyi endüstriyel tecrübelerim ve aldığım eğitim ışığında değerlendirdiğimde şu noktalar dikkatimi çekmiştir:

1.  **Güçlü Yönler:** Çalışmanın en güçlü yanı, "tek bir doğru yol yoktur" felsefesini benimsemesidir. Özellik modellemesinde "zorunlu" ve "seçimlik" özellikleri ayırması, mimarinin esnekliğini artırmıştır. Vaka çalışmalarında maliyet kaygısıyla API Gateway'in çıkarılması gibi gerçekçi senaryoların paylaşılması çok değerlidir.
2.  **Gelişime Açık Alanlar:** Makalede güvenlik (Security) başlığına değinilse de, özellikle mikroservisler arası güvenli iletişim (mTLS) ve "Zero Trust" yaklaşımları daha detaylı ele alınabilirdi.
3.  **Yorumum:** Bir Yapay Zeka Mühendisi adayı olarak, gelecekte bu referans mimarinin içerisine "AI/ML Model Serving" katmanının da eklenmesi gerektiğini düşünüyorum. Model eğitimi ve sunumu, standart mikroservislerden farklı kaynak gereksinimlerine sahip olduğu için özel bir mimari bileşen olarak ele alınmalıdır.

---

### 📝 Sonraki Adımlar
- [x] Rapor içeriği tamamlandı ve kontrol edildi.
- [ ] **UNUTMA:** Vaka çalışmaları ve mimari görünümlere ait şekiller (Şekil 3 ve Şekil 5) repoya `images/` klasörü altına eklenecek ve linklenecek.
- [ ] PDF formatına dönüştürülüp sisteme yüklenecek.