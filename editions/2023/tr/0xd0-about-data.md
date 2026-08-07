# Metodoloji ve Veriler

## Genel Bakış

Bu liste güncellemesi için OWASP API Security ekibi, başarılı ve yaygın
şekilde benimsenen 2019 listesinde kullanılan yöntemi kullandı; buna
ek olarak üç aylık bir [herkese açık veri çağrısı][1] da yapıldı. Ne yazık
ki bu çağrı sonucunda, en yaygın API güvenliği sorunları hakkında anlamlı
bir istatistiksel analiz yapılmasına yetecek veri elde edilemedi.

Bununla birlikte, doğrudan geri bildirim ve içgörü sağlayabilen daha
olgun bir API güvenliği sektörüyle, güncelleme süreci önceki yöntemle
aynı şekilde ilerledi.

Sonuç olarak, önümüzdeki üç veya dört yıl boyunca geçerliliğini koruyacak ve
modern API'lere özgü sorunlara daha fazla odaklanacak, geleceğe dönük güçlü bir
farkındalık dokümanı ortaya koyduğumuza inanıyoruz. Bu projenin amacı diğer en
kritik 10 listelerinin yerini almak değil, sektörün farkında olması ve
titizlikle ele alması gerektiğine inandığımız mevcut ve yaklaşan en
önemli API güvenliği risklerini ele almaktır.

## Metodoloji

İlk aşamada, API güvenliği olaylarına ilişkin herkese açık veriler
toplandı, incelendi ve kategorize edildi. Bu veriler, bug bounty
platformlarından ve herkese açık raporlardan toplandı. Yalnızca
2019-2022 yılları arasında bildirilen sorunlar dikkate alındı. Bu
veriler, ekibe önceki en kritik 10 listesinin hangi yönde gelişmesi
gerektiği konusunda fikir vermek ve katkı yoluyla elde edilen verilerdeki olası
yanlılığı azaltmaya yardımcı olmak için kullanıldı.

1 Eylül - 30 Kasım 2022 tarihleri arasında halka açık bir [Veri
Çağrısı][1] yürütüldü. Bununla eş zamanlı olarak proje ekibi,
2019'dan bu yana neyin değiştiğine dair tartışmaya başladı. Bu
tartışma, ilk listenin etkisini, topluluktan alınan geri bildirimleri
ve API güvenliğindeki yeni eğilimleri içeriyordu.

Proje ekibi, ilgili API güvenlik tehditleri konusunda uzmanlarla
toplantılar düzenleyerek, mağdurların bu tehditlerden nasıl
etkilendiği ve bu tehditlerin etkilerinin nasıl azaltılabileceği konusunda
içgörü elde etti.

Bu çaba, ekibin en kritik on API güvenliği riski olduğuna inandığı
konuların ilk taslağıyla sonuçlandı. Risk analizini gerçekleştirmek
için [OWASP Risk Değerlendirme Metodolojisi][2] kullanıldı. Yaygınlık
derecelendirmeleri, proje ekibi üyelerinin alandaki deneyimlerine
dayanarak aralarında vardıkları fikir birliğiyle belirlendi. Bu
konulardaki değerlendirmeler için lütfen [API Güvenliği Riskleri][3]
bölümüne bakın.

İlk taslak, ardından API güvenliği alanında ilgili deneyime sahip
güvenlik uzmanlarıyla incelenmek üzere paylaşıldı. Yorumları
incelendi, tartışıldı ve uygun olduğunda belgeye dâhil edildi.
Ortaya çıkan belge, [açık tartışma][5] için bir [Sürüm Adayı olarak
yayımlandı][4]. Çeşitli [topluluk katkıları][6] son belgeye dâhil
edildi.

Katkıda bulunanların listesi [Teşekkürler][7] bölümünde mevcuttur.

## API'ye Özgü Riskler

Liste, API'lere daha özgü olan güvenlik risklerini ele alacak şekilde
oluşturulmuştur.

Bu, API tabanlı uygulamalarda diğer genel uygulama güvenliği
risklerinin bulunmadığı anlamına gelmez. Örneğin, "Vulnerable and
Outdated Components" (Zafiyetli ve Güncel Olmayan Bileşenler) veya
"Injection" (Enjeksiyon) gibi riskleri listeye dâhil etmedik, ancak
bunları API tabanlı uygulamalarda bulabilirsiniz. Bu riskler geneldir;
API'lerde farklı davranmazlar ve istismar edilme şekilleri de farklı
değildir.

Amacımız, API'lerde özel dikkat gerektiren güvenlik riskleri
konusundaki farkındalığı artırmaktır.

[1]: https://owasp.org/www-project-api-security/announcements/cfd/2022/
[2]: https://www.owasp.org/index.php/OWASP_Risk_Rating_Methodology
[3]: ./0x10-api-security-risks.md
[4]: https://owasp.org/www-project-api-security/announcements/2023/02/api-top10-2023rc
[5]: https://github.com/OWASP/API-Security/issues?q=is%3Aissue+label%3A2023RC
[6]: https://github.com/OWASP/API-Security/pulls?q=is%3Apr+label%3A2023RC
[7]: ./0xd1-acknowledgments.md
