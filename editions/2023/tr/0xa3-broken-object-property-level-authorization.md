# API3:2023 Nesne Özelliği Düzeyinde Yetkilendirme Eksikliği

| Tehdit aktörleri / Saldırı vektörleri | Güvenlik Zayıflığı | Etkiler |
| - | - | - |
| API'ye Özgü · İstismar edilebilirlik: **Kolay** | Yaygınlık: **Yaygın** · Tespit edilebilirlik: **Kolay** | Teknik etki: **Orta** · İş etkisi: **Kuruluşa özgü** |
| API'ler, bir nesnenin tüm özelliklerini döndüren uç noktaları dışarıya açma eğilimindedir. Bu durum özellikle REST API'ler için geçerlidir. GraphQL gibi diğer protokollerde, hangi özelliklerin döndürüleceğini belirtmek için özel olarak hazırlanmış istekler gerekebilir. Manipüle edilebilecek bu ek özellikleri tespit etmek daha fazla çaba gerektirir, ancak bu görevde yardımcı olacak birkaç otomatik araç mevcuttur. | Döndürülen nesne temsillerinde hassas bilgileri tespit etmek için API yanıtlarını incelemek yeterlidir. Ek (gizli) özellikleri tespit etmek için genellikle fuzzing kullanılır. Bunların değiştirilip değiştirilemeyeceği, bir API isteği hazırlayıp yanıtı analiz etmekle anlaşılır. Hedef özellik API yanıtında döndürülmüyorsa yan etki analizi gerekebilir. | Özel/hassas nesne özelliklerine yetkisiz erişim; verilerin ifşa edilmesine, veri kaybına veya veri bozulmasına yol açabilir. Belirli koşullarda, nesne özelliklerine yetkisiz erişim; yetki yükseltmeye veya hesabın kısmen/tamamen ele geçirilmesine yol açabilir. |

## API Bu Zafiyete Açık mı?

Bir kullanıcının API uç noktası aracılığıyla bir nesneye erişmesine izin
verirken, kullanıcının erişmeye çalıştığı belirli nesne özelliklerine
erişim yetkisi olduğunu doğrulamak önemlidir.

Bir API uç noktası aşağıdaki durumlarda zafiyete açıktır:

* API uç noktası, hassas kabul edilen ve kullanıcı tarafından okunmaması
  gereken nesne özelliklerini açığa çıkarıyorsa. (önceki adı: "[Excessive
  Data Exposure][1]" — Gereğinden Fazla Verinin Açığa Çıkarılması)
* API uç noktası, kullanıcının erişememesi gereken hassas bir nesne
  özelliğinin değerini değiştirmesine, eklemesine veya silmesine izin
  veriyorsa (önceki adı: "[Mass Assignment][2]" — Kontrolsüz Toplu Atama)

## Örnek Saldırı Senaryoları

### Senaryo 1

Bir arkadaşlık uygulaması, kullanıcıların diğer kullanıcıları uygunsuz
davranış nedeniyle şikayet etmesine izin verir. Bu akışın bir parçası
olarak kullanıcı "şikayet et" düğmesine tıklar ve aşağıdaki API çağrısı
tetiklenir:

```
POST /graphql
{
  "operationName":"reportUser",
  "variables":{
    "userId": 313,
    "reason":["offensive behavior"]
  },
  "query":"mutation reportUser($userId: ID!, $reason: String!) {
    reportUser(userId: $userId, reason: $reason) {
      status
      message
      reportedUser {
        id
        fullName
        recentLocation
      }
    }
  }"
}
```

API uç noktası zafiyetlidir çünkü kimliği doğrulanmış kullanıcının,
başka kullanıcılar tarafından erişilmemesi gereken "fullName" (tam ad) ve
"recentLocation" (son konum) gibi hassas (şikayet edilen) kullanıcı nesnesi
özelliklerine erişmesine izin verir.

### Senaryo 2

Bir tür kullanıcının ("ev sahipleri") dairesini başka bir tür kullanıcıya
("misafirler") kiralamasına olanak tanıyan çevrimiçi bir pazar yeri
platformu, misafiri konaklama ücreti için tahsilat yapmadan önce ev
sahibinin misafirin yaptığı rezervasyonu onaylamasını gerektirir.

Bu akışın bir parçası olarak, ev sahibi tarafından `POST
/api/host/approve_booking` adresine aşağıdaki meşru istek gövdesiyle bir
API çağrısı gönderilir:

```
{
  "approved": true,
  "comment": "Check-in is after 3pm"
}
```

Ev sahibi, meşru isteği tekrar gönderir ve aşağıdaki kötü amaçlı veriyi
ekler:

```
{
  "approved": true,
  "comment": "Check-in is after 3pm",
  "total_stay_price": "$1,000,000"
}
```

API uç noktası zafiyetlidir çünkü ev sahibinin `total_stay_price`
(toplam konaklama ücreti) adlı dahili nesne özelliğine erişim yetkisi
olup olmadığı doğrulanmaz ve misafirden olması gerekenden çok daha fazla
ücret tahsil edilir.

### Senaryo 3

Kısa videolara dayanan bir sosyal ağ, kısıtlayıcı içerik filtreleme ve
sansür uygular. Yüklenen bir video engellense bile kullanıcı, aşağıdaki
API isteğini kullanarak videonun açıklamasını değiştirebilir:

```
PUT /api/video/update_video

{
  "description": "a funny video about cats"
}
```

Sinirlenen bir kullanıcı, meşru isteği tekrar gönderebilir ve aşağıdaki
kötü amaçlı veriyi ekleyebilir:

```
{
  "description": "a funny video about cats",
  "blocked": false
}
```

API uç noktası zafiyetlidir çünkü kullanıcının `blocked` (engellendi)
adlı dahili nesne özelliğine erişim yetkisi olup olmadığı doğrulanmaz ve
kullanıcı değeri `true`'dan `false`'a değiştirerek kendi engellenmiş
içeriğinin kilidini açabilir.

## Nasıl Önlenir?

* Bir nesneyi bir API uç noktası aracılığıyla açığa çıkarırken, her
  zaman kullanıcının açığa çıkardığınız nesne özelliklerine erişim
  yetkisi olduğundan emin olun.
* `to_json()` ve `to_string()` gibi genel yöntemler kullanmaktan kaçının.
  Bunun yerine, döndürmek istediğiniz belirli nesne özelliklerini tek
  tek seçin.
* Mümkünse, istemci girdisini otomatik olarak kod değişkenlerine, dahili
  nesnelere veya nesne özelliklerine bağlayan fonksiyonları kullanmaktan
  kaçının ("Mass Assignment" / Kontrolsüz Toplu Atama).
* Yalnızca istemci tarafından güncellenmesi gereken nesne özelliklerinde
  değişikliğe izin verin.
* Ek bir güvenlik katmanı olarak şemaya dayalı bir yanıt doğrulama
  mekanizması uygulayın. Bu mekanizmanın bir parçası olarak, tüm API
  yöntemleri tarafından döndürülen verileri tanımlayın ve uygulayın.
* Uç nokta için iş/işlevsel gereksinimlere göre döndürülen veri
  yapılarını asgari düzeyde tutun.

## Kaynaklar

### OWASP

* [API3:2019 Excessive Data Exposure - OWASP API Security Top 10 2019][1]
* [API6:2019 - Mass Assignment - OWASP API Security Top 10 2019][2]
* [Kontrolsüz Toplu Atama Hızlı Başvuru Rehberi][3]

### Harici Kaynaklar

* [CWE-213: Uyumsuz Politikalar Nedeniyle Hassas Bilgilerin Açığa Çıkması][4]
* [CWE-915: Dinamik Olarak Belirlenen Nesne Özelliklerinin Yetersiz Denetlenen Değişikliği][5]

[1]: https://owasp.org/API-Security/editions/2019/en/0xa3-excessive-data-exposure/
[2]: https://owasp.org/API-Security/editions/2019/en/0xa6-mass-assignment/
[3]: https://cheatsheetseries.owasp.org/cheatsheets/Mass_Assignment_Cheat_Sheet.html
[4]: https://cwe.mitre.org/data/definitions/213.html
[5]: https://cwe.mitre.org/data/definitions/915.html
