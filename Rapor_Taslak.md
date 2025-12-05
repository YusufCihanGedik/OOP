# Akademik Makale İnceleme Raporu: Microservice Reference Architecture

**Öğrenci:** Yusuf Cihan GEDİK  
**Öğrenci No:** Y245060014  
**Ders:** Nesneye Dayalı Programlama  
**Durum:** Taslak Aşamasında (WIP) - %50 Tamamlandı

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

Mikroservis Mimarisi (MSA) bu sorunlara **"Gevşek Bağlılık" (Loose Coupling)** ve **"Yüksek Bütünlük" (High Cohesion)** ile çözüm getirse de, endüstride standart bir uygulama rehberinin eksikliği geliştiricileri; servis sınırlarının belirlenmesi, veri tutarlılığı ve dağıtık hata ayıklama konularında zorlamaktadır.

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
* **Alternatifli (XOR):** İletişim yöntemi olarak "Senkron" (REST) veya "Asenkron" (Messaging) seçimi.

---

## 4. Önerilen Referans Mimarinin Detaylı Analizi
*(Bu bölüm, makaledeki "Decomposition View" ve "Layered View" incelenerek oluşturulmuştur.)*

Önerilen mimari, sistemi yatay katmanlara ve dikey modüllere ayırarak yönetilebilirliği artırmayı hedefler. Kritik bileşenler şunlardır:

### 4.1. İletişim ve Giriş Katmanı (Communication)
* **API Gateway:** İstemciler ile servisler arasındaki tek giriş noktasıdır. Yönlendirme, kimlik doğrulama ve protokol dönüşümü (HTTPS -> HTTP) yapar.
* **Senkron vs Asenkron:** Servisler arası iletişimde REST/gRPC (Senkron) veya Message Broker (Asenkron) yapıları tanımlanmıştır.

### 4.2. Veri Yönetimi ve Tutarlılık (Data Management)
Monolitik yapıdaki "Ortak Veritabanı" yerine, **Database-per-Service** (Her servise özel veritabanı) deseni benimsenmiştir. Dağıtık veri tutarlılığı için ise şu desenler önerilmiştir:
* **SAGA Pattern:** Uzun süreli işlemleri yönetmek için telafi edici (compensating) transaction'lar kullanılır.
* **CQRS (Command Query Responsibility Segregation):** Okuma ve yazma işlemlerinin farklı modeller üzerinden yapılması.

### 4.3. Dayanıklılık ve Hata Toleransı (Resilience)
Dağıtık sistemlerde ağ hataları kaçınılmaz olduğu için şu koruma mekanizmaları mimariye eklenmiştir:
* **Circuit Breaker:** Hatalı bir servise giden trafiği keserek sistemin geri kalanını korur.
* **Bulkhead Pattern:** Sistem kaynaklarını izole ederek bir arızanın yayılmasını engeller.

### 4.4. Gözlemlenebilirlik (Observability)
Sistemin sağlığını izlemek için **Distributed Tracing** (Dağıtık İzleme), **Log Aggregation** (Log Toplama) ve **Health Check** modülleri zorunlu kılınmıştır.

---

## 5. Nesneye Dayalı Programlama (OOP) ile İlişkiler
Makaledeki mimari kararlar, OOP prensiplerinin sistem tasarımına yansımasıdır:

### 5.1. Modülerlik ve Encapsulation (Kapsülleme)
Makalede servislerin "Business Capability" (İş Yeteneği) bazında ayrıştırılması gerektiği belirtilmiştir. Bu, OOP'deki **Encapsulation** ilkesinin mimari karşılığıdır. Bir servis (sınıf), verisini (field) dış dünyadan saklar ve sadece API (metot) üzerinden erişim verir.

### 5.2. Interface Segregation (Arayüz Ayrımı)
Servisler birbirlerinin iç yapısını veya veritabanı şemasını bilmezler. Sadece tanımlı **Interface (API Contract)** üzerinden haberleşirler. Bu, istemcilerin servisin implementasyon detaylarına bağımlı olmamasını sağlar (Dependency Inversion).

### 5.3. Single Responsibility Principle (SRP)
Her mikroservisin tek bir iş alanına (Bounded Context) odaklanması, bir sınıfta olması gereken **Tek Sorumluluk Prensibi (SRP)** ile birebir örtüşmektedir.

---

## 📝 Sonraki Adımlar (To-Do List)
- [ ] Vaka çalışmalarının (Transportation System) sonuçları ve başarı metrikleri incelenecek.
- [ ] Makalenin "Deployment View" (Şekil 5) diyagramındaki konteyner yapıları analiz edilecek.
- [ ] Kendi yorumlarım ve eleştirilerim eklenecek.