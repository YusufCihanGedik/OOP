# Akademik Makale İnceleme Raporu: Microservice Reference Architecture

**Öğrenci:** Yusuf Cihan GEDİK  
**Öğrenci No:** Y245060014  
**Ders:** Nesneye Dayalı Programlama  
**Durum:** Taslak Aşamasında (WIP) 

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
* **Derleme ve Dağıtım Sorunları:** Küçük bir kod değişikliğinde bile tüm uygulamanın yeniden derlenmesi ve test edilmesi gerekliliği.
* **Ölçeklenebilirlik (Scalability) Kısıtları:** Sistemin sadece belirli bir modülü yoğun yük altında kalsa bile, tüm sunucunun (gereksiz kaynak kullanımıyla) ölçeklendirilmek zorunda kalınması.
* **Teknoloji Bağımlılığı:** Tüm sistemin tek bir dil veya framework'e mahkum olması, yeni teknolojilerin denenmesini engellemesi.

---

## 3. Metodoloji: Hibrit ve Alan Odaklı Yaklaşım
Makale yazarları, referans mimariyi oluşturmak için **Alan Mühendisliği (Domain Engineering)** yöntemlerini kullanmıştır. Süreç üç ana aşamadan oluşur:

### A. Endüstriyel Satıcı Analizi (Vendor Analysis)
Sadece teorik bilgiyle yetinilmemiş, **AWS, Google Cloud ve Microsoft Azure** gibi dev sağlayıcıların sunduğu mimari çözümler karşılaştırmalı olarak analiz edilmiştir.
* *Sonuç:* İsimlendirmeler farklı olsa da (örn: AWS SQS vs Azure Service Bus), temel yapı taşlarının (Messaging, API Gateway, Orchestration) ortak olduğu saptanmıştır.

### B. Özellik Modellemesi (Feature Modeling)
MSA'nın bileşenleri bir "Özellik Ağacı" (Feature Diagram) üzerinde modellenmiştir. Özellikler şu şekilde sınıflandırılmıştır:
* **Zorunlu (Mandatory):** Service Discovery, API Gateway.
* **Opsiyonel (Optional):** Circuit Breaker, Distributed Tracing.

---

## 4. Önerilen Referans Mimarinin Detaylı Analizi
Önerilen mimari, sistemi yatay katmanlara ve dikey modüllere ayırarak yönetilebilirliği artırmayı hedefler.

### 4.1. İletişim ve Giriş Katmanı (Communication)
* **API Gateway:** İstemciler ile servisler arasındaki tek giriş noktasıdır. Yönlendirme, kimlik doğrulama ve protokol dönüşümü yapar.
* **Senkron vs Asenkron:** Servisler arası iletişimde REST/gRPC (Senkron) veya Message Broker (Asenkron) yapıları tanımlanmıştır.

### 4.2. Veri Yönetimi ve Tutarlılık (Data Management)
Monolitik yapıdaki "Ortak Veritabanı" yerine, **Database-per-Service** (Her servise özel veritabanı) deseni benimsenmiştir. Veri tutarlılığı için **SAGA Pattern** (telafi edici işlemler) önerilmiştir.

### 4.3. Dayanıklılık ve Gözlemlenebilirlik
* **Circuit Breaker:** Hatalı bir servise giden trafiği keserek sistemin geri kalanını korur.
* **Observability:** Sistemin sağlığını izlemek için **Distributed Tracing** ve **Log Aggregation** modülleri zorunlu kılınmıştır.

---

## 5. Dağıtım Mimarisi (Deployment View)
Makalede sunulan "Deployment View" (Şekil 5), bileşenlerin fiziksel kaynaklara nasıl dağıtılacağını modellemektedir. Bu görünüm, "Allocation Style" ve "Component & Connector Style" yaklaşımlarını birleştirir.

* **Bağımsız Dağıtılabilirlik (Independent Deployability):** Dağıtık mimarideki her servis (Authentication, Logging, Business Services) birbirinden bağımsız "Deployable Unit" olarak modellenmiştir.
* **Konteynerizasyon:** Servislerin Docker gibi konteyner teknolojileriyle paketlenmesi ve bulut ortamlarına (AWS/Azure/Google Cloud) taşınabilir olması esas alınmıştır.
* **Orkestrasyon:** Konteynerlerin yönetimi, ölçeklenmesi ve sağlığı için Kubernetes benzeri **"Service Orchestrator"** ve uygulama sunucularında çalışan **"Agents"** bileşenleri mimaride yer alır.

---

## 6. Vaka Çalışmaları ve Doğrulama (Case Studies)
Önerilen mimarinin geçerliliği, iki farklı endüstriyel proje üzerinde test edilmiş ve radar grafikleriyle analiz edilmiştir.

### Vaka 1: Ulaşım Yönetim Sistemi (Transportation Management)
COVID-19 sonrası artan lojistik taleplerini karşılamak için geliştirilmiştir.
* **Ekip:** 11 kişilik ekip (2 SRE, 1 Mimar, 6 Geliştirici vb.).
* **Platform:** Amazon Web Services (AWS) üzerinde koşturulmuştur.
* **Kritik Mimari Karar:** Bu projede maliyet (AWS costs) nedeniyle **API Gateway özel bir bileşen olarak kullanılmamış**, bunun yerine **Load Balancer** doğrudan giriş noktası olarak kullanılmıştır. Kimlik doğrulama işlemi her mikroservisin kendi sorumluluğuna bırakılmıştır.

### Vaka 2: Uzaktan Ekip Yönetimi (Remote Team Management)
Pandemi sürecinde uzaktan çalışan ekiplerin performans takibi için geliştirilmiştir.
* **Ekip:** 9 kişilik ekip.
* **Platform:** Google Cloud Platform (GCP) tercih edilmiştir.
* **Mimari Farklılık:** Birinci vakanın aksine, bu projede **API Gateway** aktif olarak kullanılmış ve yük dengeleme (load balancing) özelliği buradan sağlanmıştır. Ayrıca performans gereksinimleri nedeniyle sisteme **Caching (Önbellekleme)** modülü dahil edilmiştir.

---

## 7. Nesneye Dayalı Programlama (OOP) ile İlişkiler
* **Modülerlik ve Encapsulation:** Servislerin "Business Capability" bazında ayrıştırılması, OOP'deki Encapsulation ilkesinin mimari karşılığıdır.
* **Interface Segregation:** Servisler birbirlerinin veritabanına erişmez, sadece tanımlı Interface (API) üzerinden haberleşir.
* **Single Responsibility Principle (SRP):** Her mikroservis tek bir iş alanına odaklanır ve o alanın gerektirdiği teknolojiyi kullanmakta özgürdür (Polyglot Programming).

---

## 📝 Sonraki Adımlar (To-Do List)
- [ ] Makalenin "Discussion" bölümündeki anket sonuçları incelenecek.
- [ ] Sonuç (Conclusion) bölümü özetlenecek.
- [ ] Kaynakça formatı düzenlenecek.
- [ ] Kişisel yorumlar eklenecek.