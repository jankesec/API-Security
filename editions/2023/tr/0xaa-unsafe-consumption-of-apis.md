# API10:2023 API'lerin Güvensiz Kullanımı

| Tehdit aktörleri / Saldırı vektörleri | Güvenlik Zayıflığı | Etkiler |
| - | - | - |
| API'ye Özgü · İstismar edilebilirlik: **Kolay** | Yaygınlık: **Yaygın** · Tespit edilebilirlik: **Orta** | Teknik etki: **Ciddi** · İş etkisi: **Kuruluşa özgü** |
| Bu sorunu istismar etmek, saldırganların hedef API'nin entegre olduğu diğer API'leri/servisleri tespit etmesini ve potansiyel olarak ele geçirmesini gerektirir. Genellikle bu bilgi herkese açık değildir veya entegre API/servis kolayca istismar edilemez. | Geliştiriciler, harici veya üçüncü taraf API'lerle etkileşime giren uç noktalara güvenme ve bunları doğrulamama eğilimindedir; aktarım güvenliği, kimlik doğrulama/yetkilendirme ve girdi doğrulama ile temizleme gibi konularda daha zayıf güvenlik gereksinimlerine dayanırlar. Saldırganların, hedef API'nin entegre olduğu servisleri (veri kaynaklarını) tespit etmesi ve sonunda bunları ele geçirmesi gerekir. | Etki, hedef API'nin çekilen verilerle ne yaptığına göre değişir. Başarılı bir istismar; hassas bilgilerin yetkisiz aktörlere ifşa edilmesine, çeşitli enjeksiyon türlerine veya hizmet reddine yol açabilir. |

## API Bu Zafiyete Açık mı?

Geliştiriciler, üçüncü taraf API'lerden alınan verilere kullanıcı
girdisinden daha fazla güvenme eğilimindedir. Bu durum özellikle
tanınmış şirketler tarafından sunulan API'ler için geçerlidir. Bu
nedenle geliştiriciler, örneğin girdi doğrulama ve temizleme konusunda
daha zayıf güvenlik standartları benimseme eğilimindedir.

API aşağıdaki durumlarda zafiyete açık olabilir:

* Diğer API'lerle şifrelenmemiş bir kanal üzerinden etkileşime
  giriyorsa;
* Diğer API'lerden toplanan verileri işlemeden veya alt akış
  bileşenlerine iletmeden önce uygun şekilde doğrulamıyor ve
  temizlemiyorsa;
* Yönlendirmeleri körü körüne takip ediyorsa;
* Üçüncü taraf servis yanıtlarını işlemek için kullanılabilecek
  kaynak sayısını sınırlamıyorsa;
* Üçüncü taraf servislerle etkileşimler için zaman aşımı
  uygulamıyorsa;

## Örnek Saldırı Senaryoları

### Senaryo 1

Bir API, kullanıcının sağladığı iş adreslerini zenginleştirmek için
üçüncü taraf bir servise güvenir. Son kullanıcı tarafından API'ye bir
adres sağlandığında, bu adres üçüncü taraf servise gönderilir ve
döndürülen veri yerel, SQL destekli bir veritabanında saklanır.

Kötü niyetli kişiler, üçüncü taraf servisi kullanarak kendileri
tarafından oluşturulan bir işletmeyle ilişkili bir SQLi verisi
saklarlar. Ardından, zafiyetli API'yi kendi "kötü amaçlı işletmelerini"
üçüncü taraf servisten çekmesini sağlayacak özel bir girdi vererek
hedef alırlar. SQLi verisi sonunda veritabanı tarafından çalıştırılır ve
veriler saldırganın kontrol ettiği bir sunucuya sızdırılır.

### Senaryo 2

Bir API, hassas kullanıcı tıbbi bilgilerini güvenli bir şekilde
saklamak için üçüncü taraf bir servis sağlayıcısıyla entegre olur.
Veri, aşağıdaki gibi bir HTTP isteği kullanılarak güvenli bir bağlantı
üzerinden gönderilir:

```
POST /user/store_phr_record
{
  "genome": "ACTAGTAG__TTGADDAAIICCTT…"
}
```

Kötü niyetli kişiler üçüncü taraf API'yi ele geçirmenin bir yolunu
bulur ve API, yukarıdaki gibi isteklere `308 Permanent Redirect` ile
yanıt vermeye başlar.

```
HTTP/1.1 308 Permanent Redirect
Location: https://attacker.com/
```

API, üçüncü taraf yönlendirmelerini körü körüne takip ettiğinden,
kullanıcının hassas verilerini içeren tamamen aynı isteği tekrarlayacak,
ancak bu sefer saldırganın sunucusuna gönderecektir.

### Senaryo 3

Bir saldırgan, `'; drop db;--` adında bir Git repository'si hazırlayabilir.

Saldırıya uğrayan bir uygulama bu kötü amaçlı repository ile entegre edildiğinde,
repository adının güvenli bir girdi olduğuna
inanarak SQL sorgusu oluşturan bir uygulama üzerinde SQL enjeksiyon
verisi kullanılmış olur.

## Nasıl Önlenir?

* Servis sağlayıcılarını değerlendirirken, güvenlik duruşlarını da
  değerlendirin.
* Tüm API etkileşimlerinin güvenli bir iletişim kanalı (TLS) üzerinden
  gerçekleştiğinden emin olun.
* Entegre API'lerden alınan verileri kullanmadan önce her zaman
  doğrulayın ve uygun şekilde temizleyin.
* Entegre API'lerin sizi yönlendirebileceği iyi bilinen konumların bir
  izin listesini tutun: yönlendirmeleri körü körüne takip etmeyin.

## Kaynaklar

### OWASP

* [Web Servisi Güvenliği Hızlı Başvuru Rehberi][1]
* [Injection Flaws][2]
* [Girdi Doğrulama Hızlı Başvuru Rehberi][3]
* [Enjeksiyon Önleme Hızlı Başvuru Rehberi][4]
* [Aktarım Katmanı Koruması Hızlı Başvuru Rehberi][5]
* [Doğrulanmamış Yönlendirmeler ve İletmeler Hızlı Başvuru Rehberi][6]

### Harici Kaynaklar

* [CWE-20: Hatalı Girdi Doğrulama][7]
* [CWE-200: Hassas Bilgilerin Yetkisiz Bir Aktöre Açıklanması][8]
* [CWE-319: Hassas Bilgilerin Düz Metin Olarak İletilmesi][9]

[1]: https://cheatsheetseries.owasp.org/cheatsheets/Web_Service_Security_Cheat_Sheet.html
[2]: https://www.owasp.org/index.php/Injection_Flaws
[3]: https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html
[4]: https://cheatsheetseries.owasp.org/cheatsheets/Injection_Prevention_Cheat_Sheet.html
[5]: https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html
[6]: https://cheatsheetseries.owasp.org/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.html
[7]: https://cwe.mitre.org/data/definitions/20.html
[8]: https://cwe.mitre.org/data/definitions/200.html
[9]: https://cwe.mitre.org/data/definitions/319.html
