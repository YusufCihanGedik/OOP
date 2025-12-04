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
* **İnceleme Tarihi:** 05.12.2024

---

## 2. Problem Tanımı ve Motivasyon
Yazılım endüstrisinde sistemler karmaşıklaştıkça, geleneksel **Monolitik (Monolithic)** mimarilerin sürdürülebilirliği zorlaşmaktadır. Makalede vurgulandığı üzere, monolitik yapılarda tüm işlevlerin tek bir kod tabanında (single codebase) toplanması ve bileşenlerin **"Sıkı Bağlı" (Tightly Coupled)** olması şu sorunlara yol açmaktadır:
* Küçük bir değişiklikte tüm sistemin yeniden derlenmesi gerekliliği.
* Ölçeklenebilirlik (scalability) sorunları; tek bir modül için tüm sunucuyu büyütme zorunluluğu.
* Hata toleransının düşük olması; bir modüldeki hatanın tüm sistemi çökertmesi.

Mikroservis Mimarisi (MSA) bu sorunlara çözüm getirse de, literatürde ve endüstride standart bir uygulama rehberinin (Reference Architecture) eksikliği, geliştiricilerin şu konularda zorlanmasına neden olmaktadır:
1.  **Servis Sınırlarının Belirlenmesi (Service Boundaries):** Domain'in nasıl parçalanacağı.
2.  **Veri Tutarlılığı (Data Consistency):** Dağıtık veritabanlarında tutarlılığın sağlanması (SAGA vb.).
3.  **Orkestrasyon ve İzlenebilirlik (Orchestration & Distributed Tracing).**

**Makalenin Amacı:** Hem akademik literatürü hem de endüstriyel pratikleri birleştirerek, uygulanabilir ve doğrulanmış bir **Mikroservis Referans Mimarisi** sunmaktır.

---

## 3. Metodoloji ve Yaklaşım (Methodology)
Makale yazarları, referans mimariyi oluşturmak için **Hibrit ve Alan Odaklı (Domain-Driven)** bir yaklaşım izlemiştir. Süreç şu aşamalardan oluşmaktadır:

### A. Endüstriyel Satıcı Analizi (Vendor Analysis)
Sadece teorik bilgiyle yetinilmemiş, bulut bilişim devlerinin sunduğu mimari çözümler incelenmiştir:
* **Amazon Web Services (AWS)**
* **Google Cloud Platform (GCP)**
* **Microsoft Azure**
* *Bulgu:* Her ne kadar terminolojiler farklı olsa da (örn: AWS Lambda vs Azure Functions), sunulan temel bileşenlerin (API Gateway, Service Discovery, Load Balancer) ortak olduğu tespit edilmiştir.

### B. Alan Modellemesi (Feature Modeling)
Mikroservis mimarisinin özellikleri **"Zorunlu" (Mandatory)** ve **"Opsiyonel" (Optional)** olarak sınıflandırılmıştır.
* *Örnek:* "Service Discovery" zorunlu bir özellik iken, "Circuit Breaker" deseni opsiyonel bir dayanıklılık (resilience) modülü olarak modellenmiştir.

### C. Doğrulama: Vaka Çalışmaları (Multi-Case Study)
Önerilen mimari sadece kağıt üzerinde bırakılmamış, iki farklı gerçek dünya senaryosunda test edilmiştir:
1.  **Ulaşım Yönetim Sistemi (Transportation Management System):** Lojistik süreçlerinin yönetimi.
2.  **Uzaktan Ekip Yönetimi (Remote Team Management):** Dağıtık ekiplerin performans takibi.

---

## 4. OOP ve Tasarım Desenleri ile İlk İlişkiler
*(Ön inceleme notlarıdır, detaylandırılacak)*
Makalede geçen kavramlar, OOP prensipleriyle doğrudan örtüşmektedir:
* **Decomposition (Ayrıştırma):** "Single Responsibility Principle" (Tek Sorumluluk Prensibi) ilkesinin mimari seviyedeki uygulamasıdır.
* **Encapsulation (Kapsülleme):** Her mikroservis kendi veritabanına sahiptir (Database per Service), dışarıdan doğrudan erişim engellenmiştir.
* **Interfaces (Arayüzler):** Servisler birbirlerinin iç yapısını bilmez, sadece API kontratları (Interface) üzerinden haberleşir.

---

## 📝 Sonraki Adımlar (To-Do List)
- [ ] Vaka çalışmalarındaki "Decomposition View" ve "Deployment View" diyagramları incelenecek.
- [ ] Makalede geçen **SAGA** ve **Circuit Breaker** desenlerinin kod tarafındaki karşılıkları araştırılacak.
- [ ] "Loose Coupling" kavramının önerilen mimaride nasıl sağlandığı detaylandırılacak.