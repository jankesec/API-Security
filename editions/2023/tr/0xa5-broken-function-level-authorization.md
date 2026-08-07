# API5:2023 Fonksiyon Düzeyinde Yetkilendirme Eksikliği

| Tehdit aktörleri / Saldırı vektörleri | Güvenlik Zayıflığı | Etkiler |
| - | - | - |
| API'ye Özgü · İstismar edilebilirlik: **Kolay** | Yaygınlık: **Yaygın** · Tespit edilebilirlik: **Kolay** | Teknik etki: **Ciddi** · İş etkisi: **Kuruluşa özgü** |
| İstismar için saldırganın, anonim kullanıcı veya ayrıcalıksız normal kullanıcı olarak erişim yetkisi olmaması gereken bir API uç noktasına meşru API çağrıları göndermesi yeterlidir. Açığa çıkmış uç noktalar kolayca istismar edilir. | Bir fonksiyon veya kaynak için yetkilendirme kontrolleri genellikle yapılandırma veya kod düzeyinde yönetilir. Modern uygulamalar birçok rol, grup ve karmaşık kullanıcı hiyerarşisi (örn. alt kullanıcılar veya birden fazla role sahip kullanıcılar) içerebildiğinden, uygun kontrollerin uygulanması kafa karıştırıcı bir görev olabilir. API'ler daha yapılandırılmış olduğundan ve farklı fonksiyonlara erişim daha öngörülebilir olduğundan, bu tür açıkları API'lerde tespit etmek daha kolaydır. | Bu tür açıklar, saldırganların yetkisiz işlevlere erişmesine olanak tanır. Yönetimsel fonksiyonlar bu tür saldırılar için başlıca hedeftir ve verilerin ifşa edilmesine, veri kaybına veya veri bozulmasına yol açabilir. Nihayetinde hizmet kesintisine de yol açabilir. |

## API Bu Zafiyete Açık mı?

Fonksiyon düzeyinde yetkilendirme eksikliği sorunlarını bulmanın en iyi
yolu, uygulamadaki kullanıcı hiyerarşisini, farklı rolleri veya grupları
göz önünde bulundurarak yetkilendirme mekanizmasının derinlemesine
analizini yapmak ve şu soruları sormaktır:

* Normal bir kullanıcı yönetimsel uç noktalara erişebiliyor mu?
* Bir kullanıcı, yalnızca HTTP yöntemini değiştirerek (örn. `GET`'ten
  `DELETE`'e) erişim yetkisi olmaması gereken hassas işlemleri (örn.
  oluşturma, değiştirme veya silme) gerçekleştirebiliyor mu?
* X grubundaki bir kullanıcı, yalnızca uç nokta URL'sini ve
  parametrelerini tahmin ederek (örn. `/api/v1/users/export_all`)
  yalnızca Y grubundaki kullanıcılara açık olması gereken bir
  fonksiyona erişebiliyor mu?

Bir API uç noktasının yalnızca URL yoluna bakarak normal mi yoksa
yönetimsel mi olduğunu varsaymayın.

Geliştiriciler yönetimsel uç noktaların çoğunu `/api/admins` gibi
belirli bir göreli yol altında açığa çıkarmayı tercih etse de, bu
yönetimsel uç noktaların `/api/users` gibi normal uç noktalarla
birlikte başka göreli yollar altında bulunması da oldukça yaygındır.

## Örnek Saldırı Senaryoları

### Senaryo 1

Yalnızca davet edilen kullanıcıların katılabildiği bir uygulamanın
kayıt süreci sırasında, mobil uygulama `GET
/api/invites/{invite_guid}` şeklinde bir API çağrısı tetikler. Yanıt,
davetin ayrıntılarını, kullanıcının rolünü ve kullanıcının e-postasını
içeren bir JSON içerir.

Bir saldırgan, isteği kopyalar ve HTTP yöntemini ve uç noktayı `POST
/api/invites/new` olarak değiştirir. Bu uç noktaya yalnızca yöneticiler
tarafından yönetici konsolu üzerinden erişilmelidir. Uç nokta,
fonksiyon düzeyinde yetkilendirme kontrolleri uygulamaz.

Saldırgan bu açığı istismar eder ve yönetici ayrıcalıklarına sahip yeni
bir davet gönderir:

```
POST /api/invites/new

{
  "email": "attacker@somehost.com",
  "role":"admin"
}
```

Daha sonra saldırgan, kendisine bir yönetici hesabı oluşturmak ve
sisteme tam erişim sağlamak için bu kötü amaçlı olarak hazırlanmış
daveti kullanır.

### Senaryo 2

Bir API, yalnızca yöneticilere açık olması gereken bir uç nokta içerir
- `GET /api/admin/v1/users/all`. Bu uç nokta, uygulamanın tüm
kullanıcılarının bilgilerini döndürür ve fonksiyon düzeyinde
yetkilendirme kontrolleri uygulamaz. API yapısını öğrenen bir saldırgan,
bilinçli bir tahminde bulunarak bu uç noktaya erişmeyi başarır ve bu da
uygulama kullanıcılarının hassas bilgilerinin ifşa olmasına yol açar.

## Nasıl Önlenir?

Uygulamanız, tüm iş fonksiyonlarınızdan çağrılan tutarlı ve analiz
edilmesi kolay bir yetkilendirme modülüne sahip olmalıdır. Bu tür bir
koruma çoğunlukla, uygulama kodunun dışındaki bir veya daha fazla
bileşen tarafından sağlanır.

* Uygulama mekanizması(ları), varsayılan olarak tüm erişimi reddetmeli
  ve her fonksiyona erişim için belirli rollere açık izin verilmesini
  gerektirmelidir.
* Uygulamanın iş mantığını ve grup hiyerarşisini göz önünde
  bulundurarak API uç noktalarınızı fonksiyon düzeyinde yetkilendirme
  açıkları açısından gözden geçirin.
* Tüm yönetimsel denetleyicilerinizin, kullanıcının grubuna/rolüne
  dayalı yetkilendirme kontrolleri uygulayan soyut bir yönetimsel
  denetleyiciden türediğinden emin olun.
* Normal bir denetleyici içindeki yönetimsel fonksiyonların,
  kullanıcının grubuna ve rolüne dayalı yetkilendirme kontrolleri
  uyguladığından emin olun.

## Kaynaklar

### OWASP

* [Forced Browsing][1]
* "A7: Missing Function Level Access Control", [OWASP Top 10 2013][2]
* [Access Control][3]

### Harici Kaynaklar

* [CWE-285: Hatalı Yetkilendirme][4]

[1]: https://owasp.org/www-community/attacks/Forced_browsing
[2]: https://github.com/OWASP/Top10/raw/master/2013/OWASP%20Top%2010%20-%202013.pdf
[3]: https://owasp.org/www-community/Access_Control
[4]: https://cwe.mitre.org/data/definitions/285.html
