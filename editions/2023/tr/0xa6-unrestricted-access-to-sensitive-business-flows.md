# API6:2023 Hassas İş Akışlarına Sınırsız Erişim

| Tehdit aktörleri / Saldırı vektörleri | Güvenlik Zayıflığı | Etkiler |
| - | - | - |
| API'ye Özgü · İstismar edilebilirlik: **Kolay** | Yaygınlık: **Çok yaygın** · Tespit edilebilirlik: **Orta** | Teknik etki: **Orta** · İş etkisi: **Kuruluşa özgü** |
| İstismar genellikle API'nin desteklediği iş modelini anlamayı, hassas iş akışlarını bulmayı ve bu akışlara erişimi otomatikleştirerek kuruluşa zarar vermeyi içerir. | İş gereksinimlerini tam olarak desteklemek için API'yi bütünsel biçimde değerlendirmemek, bu sorunun yaygınlaşmasına katkıda bulunur. Saldırganlar, hedef iş akışında hangi kaynakların (örn. uç noktaların) yer aldığını ve bunların birlikte nasıl çalıştığını manuel olarak tespit eder. Koruma mekanizmaları mevcutsa saldırganların bunları aşmanın bir yolunu bulması gerekir. | Genel olarak teknik bir etki beklenmez. İstismar kuruluşa farklı şekillerde zarar verebilir; örneğin meşru kullanıcıların bir ürünü satın almasını engelleyebilir veya bir oyunun iç ekonomisinde enflasyona yol açabilir. |

## API Bu Zafiyete Açık mı?

Bir API uç noktası oluştururken, hangi iş akışını açığa çıkardığını
anlamak önemlidir. Bazı iş akışları, aşırı erişimin işletmeye zarar
verebilmesi anlamında diğerlerinden daha hassastır.

Hassas iş akışlarına ve bunlarla ilişkili aşırı erişim risklerine
yaygın örnekler:

* Ürün satın alma akışı - bir saldırgan, yüksek talep gören bir ürünün
  tüm stoğunu tek seferde satın alıp daha yüksek fiyata yeniden satabilir
  (scalping)
* Yorum/gönderi oluşturma akışı - bir saldırgan sistemi spam ile
  doldurabilir
* Rezervasyon yapma - bir saldırgan tüm uygun zaman dilimlerini
  rezerve ederek diğer kullanıcıların sistemi kullanmasını engelleyebilir

Aşırı erişim riski, sektörlere ve kuruluşlara göre değişebilir. Örneğin,
bir betik tarafından gönderi oluşturulması bir sosyal ağ tarafından
spam riski olarak görülebilirken, başka bir sosyal ağ tarafından teşvik
edilebilir.

Bir API uç noktası, hassas bir iş akışına erişimi uygun şekilde
kısıtlamadan açığa çıkarıyorsa zafiyete açıktır.

## Örnek Saldırı Senaryoları

### Senaryo 1

Bir teknoloji şirketi, Şükran Günü'nde yeni bir oyun konsolu
çıkaracağını duyurur. Ürüne çok yüksek talep vardır ve stok sınırlıdır.
Bir saldırgan, yeni ürünü otomatik olarak satın alıp işlemi tamamlayan
bir kod yazar.

Çıkış gününde saldırgan, farklı IP adreslerine ve konumlara dağıtılmış
şekilde kodu çalıştırır. API uygun korumayı uygulamadığı için, saldırgan
diğer meşru kullanıcılardan önce stoğun büyük kısmını satın alabilir.

Daha sonra saldırgan, ürünü başka bir platformda çok daha yüksek bir
fiyata satar.

### Senaryo 2

Bir havayolu şirketi, iptal ücreti almadan çevrimiçi bilet satın alma
imkanı sunar. Kötü niyetli bir kullanıcı, istediği bir uçuşun
koltuklarının %90'ını rezerve eder.

Uçuştan birkaç gün önce kötü niyetli kullanıcı tüm biletleri aynı anda
iptal eder; bu da havayolunu uçuşu doldurmak için bilet fiyatlarını
indirime zorlar.

Bu noktada kullanıcı, orijinalinden çok daha ucuz olan tek bir bilet
satın alır.

### Senaryo 3

Bir yolculuk paylaşım uygulaması bir referans programı sunar -
kullanıcılar arkadaşlarını davet edebilir ve uygulamaya katılan her
arkadaş için kredi kazanabilir. Bu kredi daha sonra yolculuk
rezervasyonu için nakit gibi kullanılabilir.

Bir saldırgan, kayıt sürecini otomatikleştiren bir betik yazarak bu
akışı istismar eder; her yeni kullanıcı saldırganın cüzdanına kredi
ekler.

Saldırgan daha sonra ücretsiz yolculukların keyfini çıkarabilir veya
aşırı kredisi olan hesapları nakit karşılığında satabilir.

## Nasıl Önlenir?

Riski azaltmaya yönelik planlama iki katmanda yapılmalıdır:

* İş - aşırı kullanıldığında kuruluşa zarar verebilecek iş
  akışlarını tespit edin.
* Mühendislik - iş riskini azaltmak için doğru koruma
  mekanizmalarını seçin.

    Koruma mekanizmalarından bazıları daha basit, bazıları ise
    uygulanması daha zordur. Otomatik tehditleri yavaşlatmak için şu
    yöntemler kullanılır:

    * Cihaz parmak izi çıkarma: beklenmedik istemci cihazlarına (örn.
      headless tarayıcılar) hizmet reddetmek, tehdit aktörlerinin daha
      gelişmiş çözümler kullanmasına neden olur ve bu da onlar için
      daha maliyetli hâle gelir
    * İnsan tespiti: captcha veya daha gelişmiş biyometrik çözümler
      (örn. yazma kalıpları) kullanmak
    * İnsan dışı kalıplar: insan dışı kalıpları tespit etmek için
      kullanıcı akışını analiz edin (örn. kullanıcının "sepete ekle" ve
      "satın almayı tamamla" fonksiyonlarına bir saniyeden kısa sürede
      erişmesi)
    * Tor çıkış düğümlerinin ve bilinen proxy'lerin IP adreslerini
      engellemeyi değerlendirin

    Doğrudan makineler tarafından kullanılan API'lere (geliştirici ve
    B2B API'leri gibi) erişimi güvenli hâle getirin ve sınırlayın. Bu
    tür API'ler genellikle gerekli tüm koruma mekanizmalarını
    uygulamadıkları için saldırganlar için kolay bir hedef olma
    eğilimindedir.

## Kaynaklar

### OWASP

* [OWASP Automated Threats to Web Applications][1]
* [API10:2019 Insufficient Logging & Monitoring][2]

[1]: https://owasp.org/www-project-automated-threats-to-web-applications/
[2]: https://owasp.org/API-Security/editions/2019/en/0xaa-insufficient-logging-monitoring/
