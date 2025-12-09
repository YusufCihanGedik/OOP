# Akademik Makale İnceleme Raporu: Microservice Reference Architecture

> **Öğrenci:** Yusuf Cihan GEDİK <br>
> **Öğrenci No:** Y245060014 <br>
> **Ders:** Nesneye Dayalı Programlama <br>


---

## 📋 İçindekiler
1. [Makale Künyesi](#1-makale-künyesi)
2. [Problem Tanımı ve Motivasyon](#2-problem-tanımı-ve-motivasyon)
3. [Metodoloji: Hibrit ve Alan Odaklı Yaklaşım](#3-metodoloji-hibrit-ve-alan-odaklı-yaklaşım)
4. [Önerilen Referans Mimarinin Detaylı Analizi](#4-önerilen-referans-mimarinin-detaylı-analizi)
5. [Dağıtım Mimarisi (Deployment View)](#5-dağıtım-mimarisi-deployment-view)
6. [Vaka Çalışmaları ve Doğrulama](#6-vaka-çalışmaları-ve-doğrulama-case-studies)
7. [Tartışma ve Değerlendirme](#7-tartışma-ve-değerlendirme-discussion--evaluation)
8. [Literatür Karşılaştırması](#8-literatür-karşılaştırması-related-work)
9. [Sonuç](#9-sonuç-conclusion)
10. [Nesneye Dayalı Programlama (OOP) ile İlişkiler](#10-nesneye-dayalı-programlama-oop-ile-ilişkiler)
11. [Sonraki Adımlar](#-sonraki-adımlar)

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

## 2. Problem Tanımı ve Motivasyon

Yazılım endüstrisinde sistemler karmaşıklaştıkça ve kullanıcı talepleri arttıkça, geleneksel **Monolitik (Monolithic)** mimarilerin sürdürülebilirliği zorlaşmaktadır. Makalede detaylandırıldığı üzere, monolitik yapılarda tüm işlevlerin tek bir kod tabanında (single codebase) toplanması ve bileşenlerin **"Sıkı Bağlı" (Tightly Coupled)** olması şu kritik sorunlara yol açmaktadır:

* **Derleme ve Dağıtım Sorunları:** Küçük bir kod değişikliğinde bile (örneğin tek bir satır) tüm uygulamanın yeniden derlenmesi ve test edilmesi gerekmekte, bu da "Sürekli Dağıtım" (Continuous Delivery) süreçlerini aksatmaktadır.
* **Ölçeklenebilirlik (Scalability) Kısıtları:** Sistemin sadece belirli bir modülü (örn: Ödeme Servisi) yoğun yük altında kalsa bile, monolitik yapıda tüm sunucunun kaynakları artırılmak zorunda kalınır. Bu durum kaynak israfına ve maliyet artışına neden olur.
* **Teknoloji Bağımlılığı (Vendor Lock-in):** Tüm sistemin tek bir programlama dili veya framework'e mahkum olması, ekibin işin gerektirdiği en uygun teknolojiyi (örn: AI için Python, Backend için Java) seçmesini engeller.

**Çözüm Arayışı:** Mikroservis Mimarisi (MSA), sistemleri **gevşek bağlı (loosely coupled)** ve **yüksek bütünlüklü (highly cohesive)** servislere bölerek bu sorunları çözer. Ancak, servis sınırlarının belirlenmesi, orkestrasyon ve dağıtık veri yönetimi gibi zorluklar nedeniyle endüstride kabul görmüş standart bir **Referans Mimari** eksikliği bulunmaktadır.

Bu noktada makale, yalnızca “mikroservise geçelim” demekle yetinmeyip, mikroservisler için alan bilgisine dayalı, tekrar kullanılabilir ve platformdan bağımsız bir referans mimari tanımlamayı hedeflemektedir. Yazarlar, MSA modellemesinin tüm yönlerini (veri yönetimi, gözlemlenebilirlik, güvenlik, dağıtım, test, hata toleransı vb.) bir arada ele alan çalışmaların sınırlı olduğunu vurgulamakta ve bu boşluğu doldurmayı amaçlamaktadır.

---

## 3. Metodoloji: Hibrit ve Alan Odaklı Yaklaşım

Makale yazarları, referans mimariyi oluşturmak için **Alan Mühendisliği (Domain Engineering)** yöntemlerini kullanmış ve süreci üç aşamada gerçekleştirmiştir. Amaç, sadece teorik bir model değil, endüstriyel gerçeklerle örtüşen uygulanabilir bir şablon üretmektir.

Bu süreç kabaca şu adımlardan oluşmaktadır:
1.  Büyük bulut sağlayıcılarının mikroservis çözümlerinin analiz edilmesi,
2.  Bu çözümlerden ve literatürden elde edilen kavramların özellik modeli hâline getirilmesi,
3.  Bu modelden hareketle farklı mimari görünümler aracılığıyla referans mimarinin tasarlanması,
4.  Çoklu vaka çalışmalarıyla bu mimarinin değerlendirilmesi.

### A. Endüstriyel Satıcı Analizi (Vendor Analysis)
Yazarlar, **AWS, Google Cloud ve Microsoft Azure** gibi dev bulut sağlayıcılarının sunduğu mimari çözümleri karşılaştırmalı olarak analiz etmiştir.

* **Bulgular:** İsimlendirmeler farklı olsa da temel yapı taşlarının ortak olduğu saptanmıştır. Örneğin, "Container Orchestration" için her üç sağlayıcı da Kubernetes tabanlı çözümler (EKS, GKE, AKS) sunmaktadır.
* **Ortak Bileşenler:** API Gateway, Load Balancing, Service Discovery ve Distributed Tracing tüm sağlayıcılarda standart olarak bulunmaktadır.
* **Platform Bağımsızlığı:** Bu analiz sonucunda, önerilen mimarinin belirli bir markaya bağımlı olmayan (**Vendor-Agnostic**) bir yapıda olması sağlanmıştır.

> Makaledeki **Tablo 1**, bu analiz sonuçlarını sistematik hâle getirerek iletişim stili, API ağ geçidi, hizmet düzenleme, dağıtım/CI-CD, gözlemlenebilirlik, güvenlik ve provisioning gibi başlıklar altında üç tedarikçinin sunduğu çözümleri yan yana göstermektedir.

### B. Özellik Modellemesi (Feature Modeling)
MSA'nın bileşenleri bir "Özellik Ağacı" (Feature Diagram) üzerinde modellenmiştir. Özellikler şu şekilde sınıflandırılmıştır:

* **Zorunlu (Mandatory):** Service Discovery, API Gateway, Load Balancing.
* **Opsiyonel (Optional):** Circuit Breaker (Hata Toleransı), Distributed Tracing (İzlenebilirlik).
* **Alternatifli (XOR):** Veri tutarlılığı için "SAGA" veya "Two-Phase Commit (2PC)" seçeneklerinden biri seçilmelidir.

Önceki çalışmalarına dayanan aile özellik modeli (family feature model), mikroservis alanını temsil eden 10 temel kavram etrafında şekillenmektedir. Yazarlar, bu aile özellik modelinden her proje için bir **uygulama özellik modeli (application feature model)** türetilebileceğini vurgulamaktadır.

### C. Mimari Dokümantasyon Yöntemi (Views and Beyond)
Mimarinin farklı paydaşlara (Geliştirici, Yönetici, Test Mühendisi) hitap edebilmesi için **"Views and Beyond (V&B)"** yaklaşımı kullanılmıştır. Bu kapsamda mimari; Ayrıştırma, Katmanlı ve Dağıtım görünümleriyle belgelenmiştir.

Makale kapsamında V&B yaklaşımında tanımlı 17 farklı mimari görünüm, 50 pratisyene (yazılım mimarı, geliştirici, DevOps mühendisi vb.) sunulmuştur. Sonuç olarak referans mimari şu üç stil ile dokümante edilmiştir:
* Katmanlı Görünüm (Layered View)
* Ayrıştırma Görünümü (Decomposition View)
* Dağıtım ve SOA Görünümü (Deployment & SOA View)

---

## 4. Önerilen Referans Mimarinin Detaylı Analizi

Önerilen mimari, sistemi yatay katmanlara ve dikey modüllere ayırarak yönetilebilirliği artırmayı hedefler.

### 4.1. İletişim ve Giriş Katmanı (Communication)
* **API Gateway:** İstemciler ile backend servisleri arasındaki tek giriş noktasıdır. Yönlendirme (Routing), kimlik doğrulama ve protokol dönüşümü (HTTPS -> HTTP) yapar.
* **İletişim Protokolleri:** Servisler arası iletişimde senkron işlemler için **REST/gRPC**, asenkron işlemler için **Message Broker (RabbitMQ/Kafka)** yapıları tanımlanmıştır.

### 4.2. Veri Yönetimi ve Tutarlılık (Data Management)
Monolitik yapıdaki "Ortak Veritabanı" (Shared Database) anti-pattern'i yerine, **Database-per-Service** (Her servise özel veritabanı) deseni benimsenmiştir.

* **Sorun:** Dağıtık veritabanlarında "JOIN" işlemleri yapılamaz ve veri tutarlılığı zordur.
* **Çözüm:** Veri tutarlılığı için **SAGA Pattern** (telafi edici işlemler zinciri) ve veri geçmişi takibi için **Event Sourcing** önerilmiştir.

### 4.3. Test Stratejileri (Testing)
Makale, mikroservislerin test edilebilirliği için özel bir katman tanımlamıştır. Dağıtık sistemlerde sadece birim testler (Unit Test) yeterli değildir. Önerilen test yöntemleri şunlardır:
* **Consumer-Driven Contract Testing:** Servisler arası API sözleşmelerinin doğrulanması.
* **End-to-End Testing:** Tüm akışın uçtan uca test edilmesi.
* **Integration Testing:** Servislerin birbirleriyle entegrasyonunun testi.

### 4.4. Dayanıklılık ve Gözlemlenebilirlik (Resilience & Observability)
* **Circuit Breaker:** Hatalı bir servise giden trafiği keserek hatanın tüm sisteme yayılmasını (cascading failure) engeller.
* **Sidecar Pattern:** Loglama, izleme ve güvenlik gibi "cross-cutting concerns" (kesişen ilgiler), ana uygulama koduna dokunmadan yan bir konteyner (sidecar) olarak çalıştırılır.
* **Observability:** Sistemin sağlığını izlemek için **Distributed Tracing** (Trace ID ile takip), **Log Aggregation** (Merkezi Loglama) ve **Health Check** modülleri zorunlu kılınmıştır.

---

## 5. Dağıtım Mimarisi (Deployment View)

Makalede sunulan "Deployment View" (Şekil 5), bileşenlerin fiziksel kaynaklara nasıl dağıtılacağını modellemektedir.

* **Bağımsız Dağıtılabilirlik (Independent Deployability):** Dağıtık mimarideki her servis (Authentication, Logging, Business Services) birbirinden bağımsız "Deployable Unit" olarak modellenmiştir.
* **Konteynerizasyon:** Servislerin Docker gibi konteyner teknolojileriyle paketlenmesi ve bulut ortamlarına (AWS/Azure/Google Cloud) taşınabilir olması esas alınmıştır.
* **Orkestrasyon:** Konteynerlerin yönetimi, ölçeklenmesi ve sağlığı için Kubernetes benzeri **"Service Orchestrator"** ve uygulama sunucularında çalışan **"Agents"** bileşenleri mimaride yer alır.

Bu görünüm aynı zamanda **Hizmet Odaklı Mimari (SOA)** ile de yakından ilişkilidir. Her bileşen hem bir hizmet sağlayıcı (provider) hem de bir hizmet tüketici (consumer) rolü üstlenebilmekte ve aradaki ilişkiler açık şekilde tanımlanmış API sözleşmeleri üzerinden kurulmaktadır.

---

## 6. Vaka Çalışmaları ve Doğrulama (Case Studies)

Önerilen mimarinin geçerliliği, iki farklı endüstriyel proje üzerinde test edilmiş ve radar grafikleriyle analiz edilmiştir.

### Vaka 1: Ulaşım Yönetim Sistemi (Transportation Management)
COVID-19 sonrası artan lojistik taleplerini karşılamak için geliştirilmiştir.
* **Ekip ve Platform:** 11 kişilik ekip tarafından **AWS** üzerinde geliştirilmiştir.
* **Kritik Mimari Karar:** Bu projede AWS maliyetlerini düşürmek amacıyla **API Gateway** bileşeni kullanılmamış, bunun yerine **Load Balancer** doğrudan giriş noktası olarak yapılandırılmıştır. Kimlik doğrulama işlemi merkezi ağ geçidi yerine her mikroservisin kendi içinde yapılmıştır.

### Vaka 2: Uzaktan Ekip Yönetimi (Remote Team Management)
Pandemi sürecinde uzaktan çalışan ekiplerin performans takibi için geliştirilmiştir.
* **Ekip ve Platform:** 9 kişilik ekip tarafından **Google Cloud Platform (GCP)** üzerinde geliştirilmiştir.
* **Mimari Farklılık:** Birinci vakanın aksine, bu projede **API Gateway** aktif olarak kullanılmış ve yük dengeleme buradan sağlanmıştır. Ayrıca performans gereksinimleri nedeniyle sisteme **Caching (Önbellekleme)** modülü dahil edilmiştir.

---

## 7. Tartışma ve Değerlendirme (Discussion & Evaluation)

Makalede önerilen mimarinin başarısı, geliştirici ekiplerle yapılan mülakatlar ve 5'li Likert ölçeği kullanılarak nicel verilerle analiz edilmiştir (Şekil 13 ve 14).

### 7.1. Etkinlik ve Pratiklik
* **Yüksek Memnuniyet:** Her iki vaka çalışmasında da mimarinin kalitesi ve yeterliliği **4 ve üzeri (5 üzerinden)** puan almıştır.
* **Öğrenme Eğrisi:** Geliştirici ekipler, önerilen yaklaşımın ve özellik modelinin, karmaşık mikroservis kavramlarını (Service Discovery, Circuit Breaker vb.) netleştirdiğini ve **öğrenme eğrisini düşürdüğünü** belirtmiştir.

### 7.2. Geçerlilik Tehditleri
Çalışmanın bilimsel güvenilirliği için "Yapı (Construct)", "İç (Internal)" ve "Dış (External)" geçerlilik tehditleri analiz edilmiştir.
* **Önlem (Triangulation):** Araştırmacı önyargısını önlemek için mülakatlarda birden fazla araştırmacı bulunmuş ve soruların yanlış anlaşılmasını önlemek için detaylı açıklamalar yapılmıştır.

---

## 8. Literatür Karşılaştırması (Related Work)

Makale, mevcut çalışmalarla kıyaslandığında şu farkları ortaya koymaktadır:
* **Yu et al. Çalışması:** Genel yapı taşlarını sunsa da, seçim yapma konusunda sistematik bir rehber ve "Feature Model" sunmamaktadır.
* **Vendor Mimarileri:** Google ve AWS'nin kendi referans mimarileri teknoloji bağımlıdır; bu çalışma ise platformdan bağımsız (Platform-Agnostic) bir yapı sunar.

---

## 9. Sonuç (Conclusion)

Bu çalışma, monolitik yapıdan mikroservislere geçişte yaşanan belirsizliği gidermek için **Alan Odaklı (Domain-Driven)** bir referans mimari sunmuştur. Vaka analizleri, önerilen mimarinin hem AWS hem de GCP üzerinde başarıyla uygulanabildiğini ve ekiplere hız kazandırdığını kanıtlamıştır.

---

## 10. Nesneye Dayalı Programlama (OOP) ile İlişkiler

Bu makale, OOP prensiplerinin dağıtık sistemlere nasıl uyarlandığını göstermektedir:

* **Modülerlik ve Encapsulation (Kapsülleme):** Servislerin "Business Capability" bazında ayrıştırılması, OOP'deki Encapsulation ilkesinin mimari seviyedeki karşılığıdır. Veriler servis içinde saklanır ve sadece API ile dışarı açılır.
* **Interface Segregation (Arayüz Ayrımı):** Servisler birbirlerinin veritabanı şemasına (implementation detail) erişmez, sadece tanımlı Interface (API) üzerinden haberleşir. Bu da "Gevşek Bağlılık" (Loose Coupling) sağlar.
* **Proxy Pattern:** API Gateway ve Sidecar desenleri, OOP'deki Proxy tasarım deseninin dağıtık mimarideki uygulamasıdır. İstemci ile asıl servis arasına girerek güvenliği ve yönlendirmeyi yönetirler.
* **Observer Pattern:** Asenkron iletişimde kullanılan "Event Sourcing" ve "Message Broker" yapıları, OOP'deki Observer (Gözlemci) desenine dayanır. Bir servis olay yayınlar, diğerleri buna abone olur (Pub/Sub).

---

### 📝 Sonraki Adımlar
- [x] Raporun son okuması ve yazım hataları kontrolü.
- [ ] Şekil eklenecek untuma!!!!!
- [ ] GitHub'a PDF formatında nihai raporun yüklenmesi.