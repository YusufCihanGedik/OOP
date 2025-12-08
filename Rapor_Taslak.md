# Akademik Makale İnceleme Raporu: Microservice Reference Architecture

**Öğrenci:** Yusuf Cihan GEDİK  
**Öğrenci No:** Y245060014  
**Ders:** Nesneye Dayalı Programlama  
**Durum:** Taslak Aşamasında (WIP) - %85 Tamamlandı

---

## 1. Makale Künyesi
* **Makale Adı:** Microservice reference architecture design: A multi-case study
* **Yazarlar:** Mehmet Söylemez, Bedir Tekinerdogan, Ayça Kolukısa Tarhan
* **Yayın Yeri:** Software: Practice and Experience (Wiley)
* **Yıl:** 2024
* **DOI:** 10.1002/spe.3241
* **İndeks:** SCI-Expanded
* **İnceleme Tarihi:** 06.12.2024

---

## 2. Problem Tanımı ve Motivasyon
Yazılım endüstrisinde sistemler karmaşıklaştıkça, geleneksel **Monolitik (Monolithic)** mimarilerin sürdürülebilirliği zorlaşmaktadır. Makalede detaylandırıldığı üzere, monolitik yapılarda tüm işlevlerin tek bir kod tabanında (single codebase) toplanması ve bileşenlerin **"Sıkı Bağlı" (Tightly Coupled)** olması şu kritik sorunlara yol açmaktadır:

* **Derleme ve Dağıtım Sorunları:** Küçük bir kod değişikliğinde bile tüm uygulamanın yeniden derlenmesi gerekmekte, bu da "Sürekli Dağıtım" (Continuous Delivery) süreçlerini aksatmaktadır.
* **Ölçeklenebilirlik (Scalability) Kısıtları:** Sistemin sadece belirli bir modülü (örn: Ödeme Servisi) yoğun yük altında kalsa bile, monolitik yapıda tüm sunucunun kaynakları artırılmak zorunda kalınır, bu da verimsizliğe yol açar.
* **Teknoloji Bağımlılığı (Vendor Lock-in):** Tüm sistemin tek bir programlama dili veya framework'e mahkum olması, ekibin yeni teknolojileri denemesini engeller.

**Çözüm Arayışı:** Mikroservis Mimarisi (MSA), sistemleri **gevşek bağlı (loosely coupled)** ve **yüksek bütünlüklü (highly cohesive)** servislere bölerek bu sorunları çözer. Ancak, servis sınırlarının belirlenmesi ve dağıtık veri yönetimi gibi zorluklar nedeniyle endüstride kabul görmüş bir **Referans Mimari** eksikliği bulunmaktadır.

---

## 3. Metodoloji: Hibrit ve Alan Odaklı Yaklaşım
Makale yazarları, referans mimariyi oluşturmak için **Alan Mühendisliği (Domain Engineering)** yöntemlerini kullanmış ve süreci üç aşamada gerçekleştirmiştir:

### A. Endüstriyel Satıcı Analizi (Vendor Analysis)
Yazarlar, sadece teorik literatürle yetinmemiş, **AWS, Google Cloud ve Microsoft Azure** gibi dev bulut sağlayıcılarının sunduğu mimari çözümleri karşılaştırmalı olarak analiz etmiştir.
* **Bulgular:** İsimlendirmeler farklı olsa da temel yapı taşlarının ortak olduğu saptanmıştır. Örneğin, "Container Orchestration" için her üç sağlayıcı da Kubernetes tabanlı çözümler (EKS, GKE, AKS) sunmaktadır.
* **Ortak Bileşenler:** API Gateway, Load Balancing, Service Discovery ve Distributed Tracing tüm sağlayıcılarda standart olarak bulunmaktadır.

### B. Özellik Modellemesi (Feature Modeling)
MSA'nın bileşenleri bir "Özellik Ağacı" (Feature Diagram) üzerinde modellenmiştir. Özellikler şu şekilde sınıflandırılmıştır:
* **Zorunlu (Mandatory):** Service Discovery, API Gateway, Load Balancing.
* **Opsiyonel (Optional):** Circuit Breaker (Hata Toleransı), Distributed Tracing (İzlenebilirlik).
* **Alternatifli (XOR):** Veri tutarlılığı için "SAGA" veya "Two-Phase Commit (2PC)" seçeneklerinden biri seçilmelidir.

---

## 4. Önerilen Referans Mimarinin Detaylı Analizi
Önerilen mimari, sistemi "Decomposition View" (Ayrıştırma Görünümü) ve "Layered View" (Katmanlı Görünüm) olmak üzere farklı açılardan ele almaktadır.

### 4.1. İletişim ve Giriş Katmanı (Communication)
* **API Gateway:** İstemciler ile backend servisleri arasındaki tek giriş noktasıdır. Yönlendirme (Routing), kimlik doğrulama ve protokol dönüşümü (HTTPS -> HTTP) yapar.
* **İletişim Protokolleri:** Servisler arası iletişimde senkron işlemler için **REST/gRPC**, asenkron işlemler için **Message Broker (RabbitMQ/Kafka)** yapıları tanımlanmıştır.

### 4.2. Veri Yönetimi ve Tutarlılık (Data Management)
Monolitik yapıdaki "Ortak Veritabanı" (Shared Database) anti-pattern'i yerine, **Database-per-Service** (Her servise özel veritabanı) deseni benimsenmiştir.
* **Sorun:** Dağıtık veritabanlarında "JOIN" işlemleri yapılamaz ve veri tutarlılığı zordur.
* **Çözüm:** Veri tutarlılığı için **SAGA Pattern** (telafi edici işlemler zinciri) ve veri geçmişi takibi için **Event Sourcing** önerilmiştir.

### 4.3. Dayanıklılık ve Gözlemlenebilirlik (Resilience & Observability)
* **Circuit Breaker:** Hatalı bir servise giden trafiği keserek hatanın tüm sisteme yayılmasını (cascading failure) engeller.
* **Sidecar Pattern:** Loglama, izleme ve güvenlik gibi "cross-cutting concerns" (kesişen ilgiler), ana uygulama koduna dokunmadan yan bir konteyner (sidecar) olarak çalıştırılır.
* **Observability:** Sistemin sağlığını izlemek için **Distributed Tracing** (Trace ID ile takip), **Log Aggregation** (Merkezi Loglama) ve **Health Check** modülleri zorunlu kılınmıştır.

---

## 5. Dağıtım Mimarisi (Deployment View)
Makalede sunulan "Deployment View" (Şekil 5), bileşenlerin fiziksel kaynaklara nasıl dağıtılacağını modellemektedir.

* **Bağımsız Dağıtılabilirlik (Independent Deployability):** Dağıtık mimarideki her servis (Authentication, Logging, Business Services) birbirinden bağımsız "Deployable Unit" olarak modellenmiştir.
* **Konteynerizasyon:** Servislerin Docker gibi konteyner teknolojileriyle paketlenmesi ve bulut ortamlarına (AWS/Azure/Google Cloud) taşınabilir olması esas alınmıştır.
* **Orkestrasyon:** Konteynerlerin yönetimi, ölçeklenmesi ve sağlığı için Kubernetes benzeri **"Service Orchestrator"** ve uygulama sunucularında çalışan **"Agents"** bileşenleri mimaride yer alır.

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

### 7.1. Etkinlik ve Pratiklik (Effectiveness & Practicality)
* **Yüksek Memnuniyet:** Her iki vaka çalışmasında da mimarinin kalitesi ve yeterliliği **4 ve üzeri (5 üzerinden)** puan almıştır.
* **Öğrenme Eğrisi:** Geliştirici ekipler, önerilen yaklaşımın ve özellik modelinin, karmaşık mikroservis kavramlarını (Service Discovery, Circuit Breaker vb.) netleştirdiğini ve **öğrenme eğrisini düşürdüğünü** belirtmiştir.

### 7.2. Geçerlilik Tehditleri (Threats to Validity)
Çalışmanın bilimsel güvenilirliği için "Yapı (Construct)", "İç (Internal)" ve "Dış (External)" geçerlilik tehditleri analiz edilmiştir.
* **Önlem (Triangulation):** Araştırmacı önyargısını önlemek için mülakatlarda birden fazla araştırmacı bulunmuş ve soruların yanlış anlaşılmasını önlemek için detaylı açıklamalar yapılmıştır.

---

## 8. Literatür Karşılaştırması (Related Work)
Makale, mevcut çalışmalarla kıyaslandığında şu farkları ortaya koymaktadır:
* **Yu et al. Çalışması:** Genel yapı taşlarını sunsa da, seçim yapma konusunda sistematik bir rehber ve "Feature Model" sunmamaktadır.
* **Vendor Mimarileri:** Google ve AWS'nin kendi referans mimarileri teknoloji bağımlıdır; bu çalışma ise platformdan bağımsız (Platform-Agnostic) bir yapı sunar.

---

## 9. Sonuç (Conclusion)
Bu çalışma, monolitik yapıdan mikroservislere geçişte yaşanan belirsizliği gidermek için **Alan Odaklı (Domain-Driven)** bir referans mimari sunmuştur. Vaka analizleri, önerilen mimarinin hem **AWS** hem de **GCP** üzerinde başarıyla uygulanabildiğini ve ekiplere hız kazandırdığını kanıtlamıştır.

---

## 10. Nesneye Dayalı Programlama (OOP) ile İlişkiler
* **Modülerlik ve Encapsulation:** Servislerin "Business Capability" bazında ayrıştırılması, OOP'deki Encapsulation ilkesinin mimari seviyedeki karşılığıdır.
* **Interface Segregation:** Servisler birbirlerinin veritabanı şemasına erişmez, sadece tanımlı Interface (API) üzerinden haberleşir.
* **Single Responsibility Principle (SRP):** Her mikroservis tek bir iş alanına odaklanır ve o alanın gerektirdiği teknolojiyi kullanmakta özgürdür (Polyglot Programming).

---

## 📝 Sonraki Adımlar
- [ ] Raporun son okuması ve yazım hataları kontrolü.
- [ ] GitHub'a PDF formatında nihai raporun yüklenmesi. 