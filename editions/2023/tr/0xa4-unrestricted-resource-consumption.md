# API4:2023 Sınırsız Kaynak Tüketimi

| Tehdit aktörleri / Saldırı vektörleri | Güvenlik Zayıflığı | Etkiler |
| - | - | - |
| API'ye Özgü · İstismar edilebilirlik: **Orta** | Yaygınlık: **Çok yaygın** · Tespit edilebilirlik: **Kolay** | Teknik etki: **Ciddi** · İş etkisi: **Kuruluşa özgü** |
| İstismar etmek basit API istekleri gerektirir. Tek bir yerel bilgisayardan veya bulut bilişim kaynakları kullanılarak birden fazla eşzamanlı istek gönderilebilir. Mevcut otomatik araçların çoğu, yüksek trafik yüküyle DoS oluşturmak ve API'lerin hizmet oranını etkilemek üzere tasarlanmıştır. | İstemci etkileşimlerini veya kaynak tüketimini sınırlamayan API'lere sıkça rastlanır. Döndürülecek kaynak sayısını kontrol eden parametreler içeren özel API istekleri hazırlamak ve yanıt durumu/süresi/uzunluğu analizi yapmak, sorunun tespit edilmesini sağlar. Aynı durum toplu (batched) işlemler için de geçerlidir. Tehdit aktörleri maliyet etkisini doğrudan göremese de, bu durum servis sağlayıcıların (örn. bulut sağlayıcı) iş/fiyatlandırma modeline dayanarak çıkarılabilir. | İstismar, kaynakların tükenmesi nedeniyle DoS'a yol açabileceği gibi; artan CPU talebi, artan bulut depolama ihtiyacı gibi altyapıyla ilgili operasyonel maliyetlerin de artmasına yol açabilir. |

## API Bu Zafiyete Açık mı?

API isteklerinin karşılanması; ağ bant genişliği, CPU, bellek ve depolama
gibi kaynaklar gerektirir. Bazen gerekli kaynaklar, e-posta/SMS/telefon
araması gönderme, biyometrik doğrulama vb. gibi API entegrasyonları
aracılığıyla servis sağlayıcılar tarafından sunulur ve istek başına
ücretlendirilir.

Aşağıdaki sınırlamalardan en az biri eksikse veya uygunsuz şekilde
ayarlanmışsa (örn. çok düşük/yüksek) bir API zafiyete açıktır:

* Çalıştırma zaman aşımları
* Ayrılabilecek maksimum bellek
* Maksimum dosya tanımlayıcısı (file descriptor) sayısı
* Maksimum işlem (process) sayısı
* Maksimum yükleme dosyası boyutu
* Tek bir API istemci isteğinde gerçekleştirilecek işlem sayısı (örn.
  GraphQL toplu işlemesi)
* Tek bir istek-yanıtta döndürülecek sayfa başına kayıt sayısı
* Üçüncü taraf servis sağlayıcıların harcama limiti

## Örnek Saldırı Senaryoları

### Senaryo 1

Bir sosyal ağ, kullanıcının şifresini sıfırlamak için SMS ile tek
kullanımlık bir belirteç almasını sağlayan SMS doğrulamalı bir "şifremi
unuttum" akışı uygulamıştır.

Kullanıcı "şifremi unuttum" seçeneğine tıkladığında, kullanıcının
tarayıcısından arka uç API'ye bir API çağrısı gönderilir:

```
POST /initiate_forgot_password

{
  "step": 1,
  "user_number": "6501113434"
}
```

Ardından, perde arkasında, arka uçtan SMS gönderimini üstlenen üçüncü
taraf bir API'ye bir API çağrısı gönderilir:

```
POST /sms/send_reset_pass_code

Host: willyo.net

{
  "phone_number": "6501113434"
}
```

Üçüncü taraf sağlayıcı Willyo, bu tür bir çağrı için 0,05 $ ücret
alır.

Bir saldırgan, ilk API çağrısını on binlerce kez gönderen bir betik
yazar. Arka uç bu isteği takip eder ve Willyo'dan on binlerce metin
mesajı göndermesini ister; bu da şirketin dakikalar içinde binlerce
dolar kaybetmesine yol açar.

### Senaryo 2

Bir GraphQL API uç noktası, kullanıcının profil resmi yüklemesine izin
verir.

```
POST /graphql

{
  "query": "mutation {
    uploadPic(name: \"pic1\", base64_pic: \"R0FOIEFOR0xJVA…\") {
      url
    }
  }"
}
```

Yükleme tamamlandığında, API yüklenen resme dayanarak farklı
boyutlarda birden fazla küçük resim (thumbnail) oluşturur. Bu grafiksel
işlem, sunucudan çok fazla bellek tüketir.

API, geleneksel bir istek sınırlandırma koruması uygular; bir kullanıcı kısa
bir süre içinde GraphQL uç noktasına çok sayıda erişemez. API ayrıca,
çok büyük resimlerin işlenmesini önlemek için küçük resim oluşturmadan
önce yüklenen resmin boyutunu kontrol eder.

Bir saldırgan, GraphQL'in esnek yapısından yararlanarak bu mekanizmaları
kolayca aşabilir:

```
POST /graphql

[
  {"query": "mutation {uploadPic(name: \"pic1\", base64_pic: \"R0FOIEFOR0xJVA…\") {url}}"},
  {"query": "mutation {uploadPic(name: \"pic2\", base64_pic: \"R0FOIEFOR0xJVA…\") {url}}"},
  ...
  {"query": "mutation {uploadPic(name: \"pic999\", base64_pic: \"R0FOIEFOR0xJVA…\") {url}}"},
}
```

API, `uploadPic` işleminin kaç kez denenebileceğini sınırlamadığı için,
bu çağrı sunucu belleğinin tükenmesine ve Hizmet Reddine (DoS) yol
açacaktır.

### Senaryo 3

Bir servis sağlayıcı, istemcilerin API'sini kullanarak keyfi boyutta
büyük dosyalar indirmesine izin verir. Bu dosyalar bulut nesne
depolamada saklanır ve çok sık değişmezler. Servis sağlayıcı, daha iyi
bir hizmet oranı sağlamak ve bant genişliği tüketimini düşük tutmak için
bir önbellek servisine güvenir. Önbellek servisi yalnızca 15 GB'a kadar
olan dosyaları önbelleğe alır.

Dosyalardan biri güncellendiğinde boyutu 18 GB'a çıkar. Tüm servis
istemcileri hemen yeni sürümü çekmeye başlar. Tüketim maliyeti
uyarıları veya bulut servisi için maksimum maliyet sınırı
olmadığından, bir sonraki aylık fatura ortalama 13 ABD dolarından 8.000
ABD dolarına çıkar.

## Nasıl Önlenir?

* Container'lar/Sunucusuz kod (örn. Lambda'lar) gibi [belleği][1],
  [CPU'yu][2], [yeniden başlatma sayısını][3], [dosya tanımlayıcılarını
  ve işlemleri][4] kolayca sınırlamayı sağlayan bir çözüm kullanın.
* Tüm gelen parametreler ve istek gövdeleri üzerinde maksimum veri
  boyutunu tanımlayın ve uygulayın; örneğin dizeler için maksimum
  uzunluk, dizilerdeki maksimum eleman sayısı ve maksimum yükleme dosya
  boyutu (yerel olarak veya bulut depolamada saklanmasından bağımsız
  olarak).
* Bir istemcinin API ile belirli bir zaman diliminde ne sıklıkla
  etkileşime girebileceğini sınırlandırın (istek sınırlandırma).
* İstek sınırlandırma, iş ihtiyaçlarına göre hassas biçimde ayarlanmalıdır. Bazı
  API uç noktaları daha katı politikalar gerektirebilir.
* Tek bir API istemcisinin/kullanıcısının tek bir işlemi kaç kez veya
  ne sıklıkla gerçekleştirebileceğini sınırlayın/kısıtlayın (örn. bir
  OTP doğrulama veya tek kullanımlık URL'yi ziyaret etmeden şifre
  kurtarma isteği).
* Sorgu dizesi ve istek gövdesi parametreleri için, özellikle yanıtta
  döndürülecek kayıt sayısını kontrol eden parametre için uygun
  sunucu tarafı doğrulaması ekleyin.
* Tüm servis sağlayıcılar/API entegrasyonları için harcama limitleri
  yapılandırın. Harcama limiti belirlemek mümkün değilse, bunun yerine
  faturalandırma uyarıları yapılandırılmalıdır.

## Kaynaklar

### OWASP

* ["Kullanılabilirlik" - Web Servisi Güvenliği Hızlı Başvuru Rehberi][5]
* ["DoS Önleme" - GraphQL Hızlı Başvuru Rehberi][6]
* ["Toplu İşlem Saldırılarını Azaltma" - GraphQL Hızlı Başvuru Rehberi][7]

### Harici Kaynaklar

* [CWE-770: Kaynakların Sınır veya Kısıtlama Olmadan Tahsis Edilmesi][8]
* [CWE-400: Denetimsiz Kaynak Tüketimi][9]
* [CWE-799: Etkileşim Sıklığının Yetersiz Denetimi][10]
* "Rate Limiting (Throttling)" - [Security Strategies for Microservices-based
  Application Systems][11], NIST

[1]: https://docs.docker.com/config/containers/resource_constraints/#memory
[2]: https://docs.docker.com/config/containers/resource_constraints/#cpu
[3]: https://docs.docker.com/engine/reference/commandline/run/#restart
[4]: https://docs.docker.com/engine/reference/commandline/run/#ulimit
[5]: https://cheatsheetseries.owasp.org/cheatsheets/Web_Service_Security_Cheat_Sheet.html#availability
[6]: https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html#dos-prevention
[7]: https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html#mitigating-batching-attacks
[8]: https://cwe.mitre.org/data/definitions/770.html
[9]: https://cwe.mitre.org/data/definitions/400.html
[10]: https://cwe.mitre.org/data/definitions/799.html
[11]: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-204.pdf
