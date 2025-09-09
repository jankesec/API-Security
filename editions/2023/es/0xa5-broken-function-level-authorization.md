# API5:2023 Autorización Rota a Nivel de Función

| Agentes de amenaza/Vectores de ataque | Debilidad de seguridad | Impactos |
| - | - | - |
| Específico de API: Explotabilidad **Fácil** | Prevalencia **Común** : Detectabilidad **Fácil** | Técnico **Grave** : Específico del negocio |
| La explotación requiere que el atacante envíe llamadas legítimas a un endpoint de la API al que no debería tener acceso como usuario anónimo o como usuario regular sin privilegios. Los endpoints expuestos se explotan fácilmente. | Las verificaciones de autorización para una función o recurso suelen gestionarse a nivel de configuración o código. Implementar comprobaciones adecuadas puede ser confuso ya que las aplicaciones modernas pueden contener muchos tipos de roles, grupos y jerarquías complejas de usuarios (p. ej., subusuarios o usuarios con más de un rol). Es más fácil descubrir estas fallas en APIs porque son más estructuradas y el acceso a diferentes funciones es más predecible. | Tales fallas permiten a los atacantes acceder a funcionalidades no autorizadas. Las funciones administrativas son objetivos clave para este tipo de ataque y pueden conducir a divulgación de datos, pérdida de datos o corrupción de datos. En última instancia, puede provocar interrupción del servicio. |

## ¿Es vulnerable la API?

La mejor manera de encontrar problemas de autorización rota a nivel de función es realizar un análisis profundo del mecanismo de autorización, considerando la jerarquía de usuarios, diferentes roles o grupos en la aplicación y planteando las siguientes preguntas:

* ¿Puede un usuario regular acceder a endpoints administrativos?  
* ¿Puede un usuario realizar acciones sensibles (p. ej., creación, modificación o eliminación) a las que no debería tener acceso simplemente cambiando el método HTTP (p. ej., de `GET` a `DELETE`)?  
* ¿Puede un usuario del grupo X acceder a una función que debería estar expuesta solo a usuarios del grupo Y, adivinando la URL del endpoint y sus parámetros (p. ej., `/api/v1/users/export_all`)?  

No asumas que un endpoint de API es regular o administrativo únicamente por la ruta en la URL.  

Aunque los desarrolladores pueden optar por exponer la mayoría de los endpoints administrativos bajo una ruta específica como `/api/admins`, es muy común encontrarlos bajo otras rutas junto con endpoints regulares, como `/api/users`.  

## Ejemplos de Escenarios de Ataque

### Escenario #1

Durante el proceso de registro de una aplicación que permite unirse solo a usuarios invitados, la app móvil ejecuta una llamada a:  
`GET /api/invites/{invite_guid}`.  

La respuesta contiene un JSON con detalles de la invitación, incluyendo el rol y el correo del usuario.  

Un atacante duplica la solicitud y manipula el método y el endpoint a:  
`POST /api/invites/new`.  

Este endpoint debería ser accesible únicamente a administradores desde la consola de administración. Sin embargo, no implementa verificaciones de autorización a nivel de función.  

El atacante explota la falla y envía una nueva invitación con privilegios de administrador:  

```
POST /api/invites/new

{
  "email": "attacker@somehost.com",
  "role": "admin"
}
```

Posteriormente, el atacante utiliza la invitación maliciosa para crearse una cuenta de administrador y obtener acceso completo al sistema.

### Escenario #2

Una API contiene un endpoint que debería estar expuesto solo a administradores:  
`GET /api/admin/v1/users/all`.  

Este endpoint devuelve los detalles de todos los usuarios de la aplicación y no implementa verificaciones de autorización a nivel de función.  

Un atacante que estudió la estructura de la API realiza una conjetura y logra acceder al endpoint, exponiendo información sensible de los usuarios de la aplicación.  

## Cómo Prevenir

Tu aplicación debe tener un módulo de autorización consistente y fácil de analizar que sea invocado desde todas las funciones de negocio. Con frecuencia, esta protección la proporciona uno o más componentes externos al código de la aplicación.

* Los mecanismos de aplicación deben denegar todo acceso por defecto, requiriendo concesiones explícitas de roles específicos para acceder a cada función.  
* Revisa tus endpoints de API contra fallas de autorización a nivel de función, teniendo en cuenta la lógica de negocio de la aplicación y la jerarquía de grupos.  
* Asegúrate de que todos tus controladores administrativos hereden de un controlador abstracto administrativo que implemente verificaciones de autorización basadas en el rol/grupo del usuario.  
* Asegúrate de que las funciones administrativas dentro de un controlador regular implementen verificaciones de autorización basadas en el rol/grupo del usuario.  

## Referencias

### OWASP

* [Forced Browsing][1]  
* "A7: Missing Function Level Access Control", [OWASP Top 10 2013][2]  
* [Access Control][3]  

### Externa

* [CWE-285: Improper Authorization][4]  

[1]: https://owasp.org/www-community/attacks/Forced_browsing  
[2]: https://github.com/OWASP/Top10/raw/master/2013/OWASP%20Top%2010%20-%202013.pdf  
[3]: https://owasp.org/www-community/Access_Control  
[4]: https://cwe.mitre.org/data/definitions/285.html  
