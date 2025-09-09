# API2:2023 Autenticación Rota

| Agentes de amenaza/Vectores de ataque | Debilidad de seguridad | Impactos |
| - | - | - |
| Específico de API: Explotabilidad **Fácil** | Prevalencia **Común** : Detectabilidad **Fácil** | Técnico **Grave** : Específico del negocio |
| El mecanismo de autenticación es un objetivo fácil para los atacantes ya que está expuesto a todos. Aunque se pueden requerir habilidades técnicas más avanzadas para explotar algunos problemas de autenticación, generalmente existen herramientas disponibles para hacerlo. | Conceptos erróneos de ingenieros de software y seguridad respecto a los límites de autenticación y la complejidad inherente de su implementación hacen que los problemas de autenticación sean comunes. Las metodologías para detectar autenticación rota están disponibles y son fáciles de crear. | Los atacantes pueden obtener control total de las cuentas de otros usuarios en el sistema, leer sus datos personales y realizar acciones sensibles en su nombre. Es poco probable que los sistemas puedan distinguir las acciones de los atacantes de las de un usuario legítimo. |

## ¿Es vulnerable la API?

Los endpoints y flujos de autenticación son activos que deben protegerse.  
Además, los mecanismos de "Olvidé mi contraseña / restablecer contraseña" deben tratarse de la misma manera que los mecanismos de autenticación.

Una API es vulnerable si:

* Permite **credential stuffing**, donde el atacante usa fuerza bruta con una lista de nombres de usuario y contraseñas válidos.  
* Permite que los atacantes realicen un ataque de fuerza bruta sobre la misma cuenta de usuario sin presentar captcha o mecanismos de bloqueo de cuenta.  
* Permite contraseñas débiles.  
* Envía detalles sensibles de autenticación, como tokens y contraseñas, en la **URL**.  
* Permite a los usuarios cambiar su dirección de correo electrónico, contraseña actual u otras operaciones sensibles sin solicitar confirmación de la contraseña.  
* No valida la autenticidad de los tokens.  
* Acepta tokens JWT sin firmar o débilmente firmados (`{"alg":"none"}`).  
* No valida la fecha de expiración del JWT.  
* Utiliza contraseñas en texto plano, no cifradas o débilmente hasheadas.  
* Usa claves de cifrado débiles.  

Además, un microservicio es vulnerable si:

* Otros microservicios pueden acceder a él sin autenticación.  
* Usa tokens débiles o predecibles para imponer autenticación.  

## Ejemplos de Escenarios de Ataque

### Escenario #1

Para realizar la autenticación de usuario, el cliente debe emitir una solicitud a la API con las credenciales, como en el siguiente ejemplo:

```
POST /graphql
{
  "query":"mutation {
    login (username:\"<usuario>\",password:\"<contraseña>\") {
      token
    }
   }"
}
```

Si las credenciales son válidas, se devuelve un **token de autenticación** que debe ser proporcionado en las solicitudes posteriores para identificar al usuario. Los intentos de inicio de sesión están sujetos a una limitación de tasa restrictiva: solo se permiten tres solicitudes por minuto.  

Para forzar un inicio de sesión en la cuenta de la víctima, los atacantes aprovechan la capacidad de **batching** de consultas GraphQL para evadir la limitación de tasa, acelerando el ataque:  

```
POST /graphql
[
  {"query":"mutation{login(username:\"victim\",password:\"password\"){token}}"},
  {"query":"mutation{login(username:\"victim\",password:\"123456\"){token}}"},
  {"query":"mutation{login(username:\"victim\",password:\"qwerty\"){token}}"},
  ...
  {"query":"mutation{login(username:\"victim\",password:\"123\"){token}}"},
]
```

### Escenario #2

Para actualizar la dirección de correo electrónico asociada con la cuenta de un usuario, los clientes deben emitir una solicitud como la siguiente:  

```
PUT /account
Authorization: Bearer <token>

{ "email": "<nueva_direccion_email>" }
```

Debido a que la API no requiere que los usuarios confirmen su identidad proporcionando su contraseña actual, atacantes que logren robar el token de autenticación podrían tomar control de la cuenta de la víctima iniciando un flujo de restablecimiento de contraseña después de actualizar la dirección de correo electrónico de la cuenta.

## Cómo Prevenir

* Asegúrate de conocer todos los posibles flujos de autenticación de la API (móvil/web/enlaces profundos que implementen autenticación de un clic/etc.). Pregunta a tus ingenieros qué flujos podrían haberse pasado por alto.  
* Estudia tus mecanismos de autenticación. Asegúrate de entender qué son y cómo se utilizan. OAuth no es autenticación, y las API keys tampoco lo son.  
* No reinventes la rueda en autenticación, generación de tokens o almacenamiento de contraseñas. Usa los estándares.  
* Los endpoints de recuperación de credenciales/“olvidé mi contraseña” deben tratarse como endpoints de inicio de sesión en cuanto a protección contra fuerza bruta, limitación de tasa y mecanismos de bloqueo.  
* Requiere re-autenticación para operaciones sensibles (por ejemplo: cambiar el correo electrónico del propietario de la cuenta o el número de teléfono de 2FA).  
* Usa la [Guía de Autenticación de OWASP][1].  
* Implementa, cuando sea posible, autenticación multifactor.  
* Implementa mecanismos anti-fuerza bruta para mitigar ataques de credential stuffing, diccionario y fuerza bruta en tus endpoints de autenticación. Estos mecanismos deben ser más estrictos que la limitación de tasa regular en tus APIs.  
* Implementa [bloqueo de cuenta][2]/mecanismos de captcha para prevenir ataques de fuerza bruta contra usuarios específicos. Implementa verificaciones de contraseñas débiles.  
* Las API keys no deben usarse para autenticación de usuarios. Deben usarse únicamente para autenticación de [clientes de API][3].  

## Referencias

### OWASP

* [Authentication Cheat Sheet][1]  
* [Key Management Cheat Sheet][4]  
* [Credential Stuffing][5]  

### Externas

* [CWE-204: Observable Response Discrepancy][6]  
* [CWE-307: Improper Restriction of Excessive Authentication Attempts][7]  

[1]: https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html  
[2]: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/03-Testing_for_Weak_Lock_Out_Mechanism(OTG-AUTHN-003)  
[3]: https://cloud.google.com/endpoints/docs/openapi/when-why-api-key  
[4]: https://cheatsheetseries.owasp.org/cheatsheets/Key_Management_Cheat_Sheet.html  
[5]: https://owasp.org/www-community/attacks/Credential_stuffing  
[6]: https://cwe.mitre.org/data/definitions/204.html  
[7]: https://cwe.mitre.org/data/definitions/307.html  
