# Sürüm Notları

Bu, tam dört yılın ardından, OWASP API Security Top 10'un ikinci sürümüdür. Bu süreçte API ve API güvenliği alanında çok şey değişti. API trafiği hızla arttı, bazı API protokolleri çok daha yaygın hâle geldi, çok sayıda yeni API güvenliği sağlayıcısı ve çözümü ortaya çıktı. Elbette saldırganlar da API'leri ele geçirmek için yeni beceri ve teknikler geliştirdi. Bu nedenle en kritik 10 API güvenliği riskini içeren listenin güncellenme zamanı gelmişti.

API güvenlik sektörünün daha olgun bir seviyeye ulaşmasıyla, ilk kez [halka açık bir veri çağrısı][1] yapıldı. Ne yazık ki bu çağrı sonucunda herhangi bir veri paylaşılmadı. Buna rağmen proje ekibinin deneyimi, API güvenliği uzmanlarının ayrıntılı incelemeleri ve sürüm adayı hakkında topluluktan alınan geri bildirimler doğrultusunda yeni listeyi oluşturduk. Bu sürümün nasıl oluşturulduğuyla ilgili daha fazla bilgiye [Metodoloji ve Veriler bölümünden][2] ulaşabilirsiniz. Güvenlik riskleri hakkında daha fazla bilgi için [API Güvenlik Riskleri bölümüne][3] bakabilirsiniz.

OWASP API Security Top 10 2023, hızlı gelişen bir sektör için ileriye dönük hazırlanmış bir farkındalık dokümanıdır. Diğer Top 10 çalışmalarının yerini almaz. Bu sürümde:

- Excessive Data Exposure (Gereğinden Fazla Verinin Açığa Çıkarılması) ve Mass Assignment (Kontrolsüz Toplu Atama) kategorilerini, ortak temel neden olan nesne özelliği düzeyindeki yetkilendirme kontrollerinin yetersizliği etrafında birleştirdik.
- Kaynakların ne kadar hızlı tükendiğinden çok, kaynak tüketiminin kendisine daha fazla ağırlık verdik
- Çoğu rate limiting uygulanarak azaltılabilenler de dâhil olmak üzere yeni tehditleri ele almak amacıyla "Unrestricted Access to Sensitive Business Flows" (Hassas İş Akışlarına Sınırsız Erişim) adlı yeni bir kategori oluşturduk.
- Son dönemde gözlemlemeye başladığımız yeni bir saldırı yaklaşımını ele almak için "Unsafe Consumption of APIs" (API'lerin Güvensiz Kullanımı) kategorisini ekledik. Saldırganlar artık doğrudan hedefin API'lerine saldırmak yerine, hedef sistemle entegre çalışan servisleri bularak bu servisleri ele geçirmeye çalışıyor. Giderek büyüyen bu risk konusunda farkındalık oluşturmanın zamanı geldi.

API'ler; modern mikroservis mimarisinde, Tek Sayfa Uygulamalarında (SPA), mobil uygulamalarda, IoT'de vb. giderek daha önemli bir rol üstlenmektedir. OWASP API Security Top 10, güncel API güvenliği sorunları konusunda farkındalık oluşturmak amacıyla yürütülen önemli bir çalışmadır.

Bu güncelleme, [Teşekkürler][4] bölümünde listelenen çok sayıda gönüllünün büyük emeği sayesinde mümkün olmuştur.

Teşekkürler!

[1]: https://owasp.org/www-project-api-security/announcements/cfd/2022/
[2]: ./0xd0-about-data.md
[3]: ./0x10-api-security-risks.md
[4]: ./0xd1-acknowledgments.md
