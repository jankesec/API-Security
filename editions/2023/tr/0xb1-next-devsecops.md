# DevSecOps'u Neler Bekliyor?

Modern uygulama mimarilerindeki önemleri nedeniyle, güvenli API'ler
oluşturmak son derece önemlidir. Güvenlik ihmal edilemez ve tüm
geliştirme yaşam döngüsünün bir parçası olmalıdır. Yıllık tarama ve
sızma testleri artık yeterli değildir.

DevSecOps, tüm yazılım geliştirme yaşam döngüsü boyunca sürekli güvenlik
testlerini kolaylaştırarak geliştirme çalışmalarına dâhil olmalıdır. Hedefiniz,
geliştirme hızını etkilemeden geliştirme ardışık düzenini güvenlik
otomasyonuyla güçlendirmek olmalıdır.

Şüpheye düştüğünüzde bilgi sahibi olmaya devam edin ve [DevSecOps
Manifesto][1]'ya başvurun.

| | |
|-|-|
| **Tehdit Modelini Anlayın** | Test öncelikleri bir tehdit modelinden gelir. Eğer bir tehdit modeliniz yoksa, girdi olarak [OWASP Application Security Verification Standard (ASVS)][2] ve [OWASP Testing Guide][3] kullanmayı düşünün. Geliştirme ekibini sürece dâhil etmek, onların güvenlik konusunda daha bilinçli olmasına yardımcı olacaktır. |
| **SDLC'yi Anlayın** | Yazılım Geliştirme Yaşam Döngüsünü daha iyi anlamak için geliştirme ekibine katılın. Sürekli güvenlik testlerine katkınız; insanlar, süreçler ve araçlarla uyumlu olmalıdır. Herkes süreç konusunda hemfikir olmalı, böylece gereksiz sürtüşme veya direnç yaşanmamalıdır. |
| **Test Stratejileri** | Çalışmanızın geliştirme hızını etkilememesi gerektiğinden, güvenlik gereksinimlerini doğrulamak için en uygun (en basit, en hızlı ve en doğru) tekniği dikkatle seçmelisiniz. [OWASP Security Knowledge Framework][4] ve [OWASP Application Security Verification Standard][2], fonksiyonel ve fonksiyonel olmayan güvenlik gereksinimleri için değerli kaynaklardır. [DevSecOps topluluğu][7] tarafından sunulanlara benzer [projeler][5] ve [araçlar][6] için başka kaynaklar da vardır. |
| **Kapsam ve Doğruluğa Ulaşma** | Geliştiriciler ve operasyon ekipleri arasındaki köprüsünüz. Kapsama ulaşmak için yalnızca işlevselliğe değil, aynı zamanda orkestrasyona da odaklanmalısınız. Zamanınızı ve çabanızı optimize edebilmek için en baştan hem geliştirme hem de operasyon ekipleriyle yakın çalışın. Temel güvenliğin sürekli olarak doğrulandığı bir duruma ulaşmayı hedeflemelisiniz. |
| **Bulguları Açıkça İletin** | Az sürtüşmeyle veya hiç sürtüşme olmadan değer katın. Bulguları zamanında, geliştirme ekiplerinin kullandığı araçlar içinde teslim edin (PDF dosyaları içinde değil). Bulguları ele almak için geliştirme ekibine katılın. Zayıflığı ve nasıl kötüye kullanılabileceğini açıkça tanımlayarak, durumu gerçek kılmak için bir saldırı senaryosu da dâhil ederek onları eğitmek için bu fırsatı değerlendirin. |

[1]: https://www.devsecops.org/
[2]: https://owasp.org/www-project-application-security-verification-standard/
[3]: https://owasp.org/www-project-web-security-testing-guide/
[4]: https://owasp.org/www-project-security-knowledge-framework/
[5]: http://devsecops.github.io/
[6]: https://github.com/devsecops/awesome-devsecops
[7]: http://devsecops.org
