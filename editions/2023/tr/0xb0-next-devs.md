# Geliştiricileri Neler Bekliyor?

Güvenli uygulamalar oluşturmak ve bunların güvenliğini sürdürmek ya da mevcut
uygulamaları düzeltme görevi zor olabilir. API'ler için de durum farklı
değildir.

Eğitim ve farkındalığın, güvenli yazılım yazmanın temel unsurları
olduğuna inanıyoruz. Bu hedefi gerçekleştirmek için gereken her şey,
**tekrarlanabilir güvenlik süreçlerinin ve standart güvenlik
kontrollerinin oluşturulmasına ve kullanılmasına** bağlıdır.

OWASP, güvenliği ele almanıza yardımcı olacak çok sayıda ücretsiz ve açık
kaynak sunar. Mevcut projelerin kapsamlı bir listesi için
lütfen [OWASP Projeler sayfasını][1] ziyaret edin.

| | |
|-|-|
| **Eğitim** | [Application Security Wayfinder][2], Yazılım Geliştirme Yaşam Döngüsü'nün (SDLC) her aşamasında kullanılabilecek projeler hakkında genel bir fikir verir. Uygulamalı eğitim için [OWASP **crAPI** - **C**ompletely **R**idiculous **API**][3] veya [OWASP Juice Shop][4] ile başlayabilirsiniz; her ikisi de kasıtlı olarak zafiyetli API'ler içerir. [OWASP Vulnerable Web Applications Directory Project][5], kasıtlı olarak zafiyetli uygulamaların derlenmiş bir listesini sunar; burada başka zafiyetli API'ler de bulabilirsiniz. Ayrıca [OWASP AppSec Conference][6] eğitim oturumlarına katılabilir veya [yerel topluluğunuza katılabilirsiniz][7]. |
| **Güvenlik Gereksinimleri** | Güvenlik, en başından itibaren her projenin bir parçası olmalıdır. Gereksinimleri tanımlarken, o proje için "güvenli"nin ne anlama geldiğini tanımlamak önemlidir. OWASP, güvenlik gereksinimlerini belirlemek için bir rehber olarak [OWASP Application Security Verification Standard (ASVS)][8] kullanmanızı önerir. Dış kaynak kullanıyorsanız, yerel yasa ve düzenlemelere göre uyarlanması gereken [OWASP Secure Software Contract Annex][9]'i göz önünde bulundurun. |
| **Güvenlik Mimarisi** | Güvenlik, tüm proje aşamalarında bir öncelik olarak kalmalıdır. [OWASP Cheat Sheet Series][10], mimari aşamada güvenliği tasarlama konusunda rehberlik için iyi bir başlangıç noktasıdır. Diğerlerinin yanı sıra, [REST Security Cheat Sheet][11], [REST Assessment Cheat Sheet][12] ve [GraphQL Cheat Sheet][13]'i orada bulacaksınız. |
| **Standart Güvenlik Kontrolleri** | Standart güvenlik kontrollerini benimsemek, kendi mantığınızı yazarken güvenlik zayıflıkları oluşturma riskini azaltır. Birçok modern çerçeve artık etkili yerleşik standart kontrollerle geliyor olsa da, [OWASP Proactive Controls][14] projenize hangi güvenlik kontrollerini dâhil etmeniz gerektiği konusunda size iyi bir genel bakış sunar. OWASP ayrıca doğrulama kontrolleri gibi değerli bulabileceğiniz bazı kütüphaneler ve araçlar sağlar. |
| **Güvenli Yazılım Geliştirme Yaşam Döngüsü** | API'ler oluşturma süreçlerinizi geliştirmek için [OWASP Software Assurance Maturity Model (SAMM)][15] kullanabilirsiniz. Farklı API geliştirme aşamalarında size yardımcı olacak, örneğin [OWASP Code Review Guide][16] gibi başka birçok OWASP projesi de mevcuttur. |

[1]: https://owasp.org/projects/
[2]: https://owasp.org/projects/#owasp-projects-the-sdlc-and-the-security-wayfinder
[3]: https://owasp.org/www-project-crapi/
[4]: https://owasp.org/www-project-juice-shop/
[5]: https://owasp.org/www-project-vulnerable-web-applications-directory/
[6]: https://owasp.org/events/
[7]: https://owasp.org/chapters/
[8]: https://owasp.org/www-project-application-security-verification-standard/
[9]: https://owasp.org/www-community/OWASP_Secure_Software_Contract_Annex
[10]: https://cheatsheetseries.owasp.org/
[11]: https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html
[12]: https://cheatsheetseries.owasp.org/cheatsheets/REST_Assessment_Cheat_Sheet.html
[13]: https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html
[14]: https://owasp.org/www-project-proactive-controls/
[15]: https://owasp.org/www-project-samm/
[16]: https://owasp.org/www-project-code-review-guide/
