# API3:2023 Autorización Rota a Nivel de Propiedades del Objeto

| Agentes de amenaza/Vectores de ataque | Debilidad de seguridad | Impactos |
| - | - | - |
| Específico de API: Explotabilidad **Fácil** | Prevalencia **Común** : Detectabilidad **Fácil** | Técnico **Moderado** : Específico del negocio |
| Las APIs tienden a exponer endpoints que devuelven todas las propiedades de un objeto. Esto es particularmente válido para REST APIs. Para otros protocolos como GraphQL, puede requerirse crear solicitudes específicas que indiquen qué propiedades deben devolverse. Identificar estas propiedades adicionales que pueden manipularse requiere más esfuerzo, aunque existen algunas herramientas automatizadas que ayudan en esta tarea. | Inspeccionar las respuestas de la API es suficiente para identificar información sensible en las representaciones de los objetos devueltos. Generalmente se usa fuzzing para identificar propiedades adicionales (ocultas). Ver si pueden modificarse depende de crear una solicitud API adecuada y analizar la respuesta. Puede ser necesario un análisis de efectos colaterales si la propiedad objetivo no se devuelve en la respuesta de la API. | El acceso no autorizado a propiedades privadas/sensibles de objetos puede resultar en divulgación, pérdida o corrupción de datos. Bajo ciertas circunstancias, el acceso no autorizado puede llevar a una escalada de privilegios o a la toma parcial/completa de cuentas. |

## ¿Es vulnerable la API?

Cuando se permite que un usuario acceda a un objeto mediante un endpoint de API, es importante validar que el usuario tenga acceso a las propiedades específicas del objeto a las que intenta acceder.

Un endpoint de API es vulnerable si:

* Expone propiedades de un objeto consideradas sensibles y que el usuario no debería poder leer. (anteriormente llamado: “[Exposición Excesiva de Datos][1]”)  
* Permite a un usuario cambiar, agregar o eliminar el valor de una propiedad sensible de un objeto a la que no debería tener acceso (anteriormente llamado: “[Asignación Masiva][2]”).  

## Ejemplos de Escenarios de Ataque

### Escenario #1

Una aplicación de citas permite a un usuario reportar a otros por comportamiento inapropiado. Como parte de este flujo, el usuario hace clic en un botón de “reportar” y se dispara la siguiente llamada API:

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

El endpoint es vulnerable porque permite al usuario autenticado acceder a propiedades sensibles del objeto del usuario reportado, como `fullName` y `recentLocation`, que no deberían estar disponibles para otros usuarios.

### Escenario #2

Una plataforma de marketplace en línea, donde un tipo de usuario (“anfitrión”) renta su apartamento a otro tipo de usuario (“invitado”), requiere que el anfitrión apruebe la reserva antes de cobrar al invitado.

Como parte de este flujo, el anfitrión envía la siguiente solicitud legítima a  
`POST /api/host/approve_booking`:

```
{
  "approved": true,
  "comment": "Check-in is after 3pm"
}
```

El anfitrión repite la solicitud legítima pero añade un payload malicioso:

```
{
  "approved": true,
  "comment": "Check-in is after 3pm",
  "total_stay_price": "$1,000,000"
}
```

El endpoint es vulnerable porque no valida que el anfitrión tenga acceso a la propiedad interna `total_stay_price`, causando que el invitado sea cobrado más de lo acordado.

### Escenario #3

Una red social de videos cortos aplica filtros de contenido restrictivos. Incluso si un video es bloqueado, el usuario puede cambiar la descripción mediante esta solicitud:

```
PUT /api/video/update_video

{
  "description": "a funny video about cats"
}
```

Un usuario frustrado repite la solicitud legítima y añade un payload malicioso:

```
{
  "description": "a funny video about cats",
  "blocked": false
}
```

El endpoint es vulnerable porque no valida si el usuario debería tener acceso a la propiedad interna `blocked`, lo que permite modificar su valor de `true` a `false` y desbloquear contenido prohibido.

## Cómo Prevenir

* Al exponer un objeto mediante un endpoint de API, siempre asegúrate de que el usuario tenga acceso únicamente a las propiedades que se exponen.  
* Evita usar métodos genéricos como `to_json()` y `to_string()`. En su lugar, selecciona explícitamente las propiedades que deseas devolver.  
* Si es posible, evita funciones que automáticamente vinculan la entrada del cliente a variables de código, objetos internos o propiedades de objetos (“Asignación Masiva”).  
* Permite cambios únicamente en las propiedades que deberían ser actualizadas por el cliente.  
* Implementa un mecanismo de validación de respuestas basado en esquemas como capa adicional de seguridad. Dentro de este mecanismo, define y aplica los datos que devuelven todos los métodos de la API.  
* Mantén las estructuras de datos devueltas en su mínima expresión, de acuerdo con los requisitos funcionales/empresariales del endpoint.  

## Referencias

### OWASP

* [API3:2019 Exposición Excesiva de Datos - OWASP API Security Top 10 2019][1]  
* [API6:2019 - Asignación Masiva - OWASP API Security Top 10 2019][2]  
* [Mass Assignment Cheat Sheet][3]  

### Externas

* [CWE-213: Exposición de Información Sensible por Políticas Incompatibles][4]  
* [CWE-915: Modificación Inadecuadamente Controlada de Atributos de Objetos Determinados Dinámicamente][5]  

[1]: https://owasp.org/API-Security/editions/2019/en/0xa3-excessive-data-exposure/  
[2]: https://owasp.org/API-Security/editions/2019/en/0xa6-mass-assignment/  
[3]: https://cheatsheetseries.owasp.org/cheatsheets/Mass_Assignment_Cheat_Sheet.html  
[4]: https://cwe.mitre.org/data/definitions/213.html  
[5]: https://cwe.mitre.org/data/definitions/915.html  
