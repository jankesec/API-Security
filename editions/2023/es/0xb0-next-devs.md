# ¿Qué Sigue Para los Desarrolladores?

La tarea de crear y mantener aplicaciones seguras, o de corregir aplicaciones existentes, puede ser difícil. Con las APIs no es diferente.  

Creemos que la educación y la concienciación son factores clave para escribir software seguro. Todo lo demás necesario para lograr el objetivo depende de **establecer y utilizar procesos de seguridad repetibles y controles de seguridad estándar**.  

OWASP proporciona numerosos recursos gratuitos y de código abierto para ayudarte a abordar la seguridad. Por favor visita la [página de Proyectos OWASP][1] para obtener una lista completa de los proyectos disponibles.  

| | |
|-|-|
| **Educación** | El [Application Security Wayfinder][2] debería darte una buena idea sobre qué proyectos están disponibles para cada etapa/fase del Ciclo de Vida del Desarrollo de Software (SDLC). Para aprendizaje/práctica práctica puedes comenzar con [OWASP **crAPI** - **C**ompletely **R**idiculous **API**][3] o [OWASP Juice Shop][4]: ambos tienen APIs intencionalmente vulnerables. El [OWASP Vulnerable Web Applications Directory Project][5] proporciona una lista seleccionada de aplicaciones intencionalmente vulnerables: allí encontrarás varias otras APIs vulnerables. También puedes asistir a las sesiones de capacitación en la [Conferencia OWASP AppSec][6], o [unirte a tu capítulo local][7]. |
| **Requisitos de Seguridad** | La seguridad debe ser parte de cada proyecto desde el inicio. Al definir requisitos, es importante definir qué significa "seguro" para ese proyecto. OWASP recomienda usar el [OWASP Application Security Verification Standard (ASVS)][8] como guía para establecer los requisitos de seguridad. Si subcontratas, considera el [OWASP Secure Software Contract Annex][9], que debe adaptarse de acuerdo con la legislación y regulaciones locales. |
| **Arquitectura de Seguridad** | La seguridad debe seguir siendo una preocupación durante todas las etapas del proyecto. La [Serie de Cheat Sheets OWASP][10] es un buen punto de partida para orientación sobre cómo diseñar la seguridad durante la fase de arquitectura. Entre muchos otros, encontrarás el [REST Security Cheat Sheet][11] y el [REST Assessment Cheat Sheet][12], así como el [GraphQL Cheat Sheet][13]. |
| **Controles de Seguridad Estándar** | Adoptar controles de seguridad estándar reduce el riesgo de introducir debilidades de seguridad al escribir tu propia lógica. Aunque muchos frameworks modernos ahora vienen con controles estándar efectivos integrados, [OWASP Proactive Controls][14] ofrece una buena visión general de qué controles de seguridad deberías incluir en tu proyecto. OWASP también proporciona algunas bibliotecas y herramientas que pueden ser valiosas, como controles de validación. |
| **Ciclo de Vida de Desarrollo de Software Seguro** | Puedes usar el [OWASP Software Assurance Maturity Model (SAMM)][15] para mejorar tus procesos de construcción de APIs. Varios otros proyectos OWASP están disponibles para ayudarte durante las diferentes fases de desarrollo de APIs, por ejemplo, la [Guía de Revisión de Código OWASP][16]. |

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
