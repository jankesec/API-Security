# API7:2023 Server-Side Request Forgery (SSRF)

| Agentes de amenaza/Vectores de ataque | Debilidad de seguridad | Impactos |
| - | - | - |
| Específico de API: Explotabilidad **Fácil** | Prevalencia **Común** : Detectabilidad **Fácil** | Técnico **Moderado** : Específico del negocio |
| La explotación requiere que el atacante encuentre un endpoint de la API que acceda a un URI proporcionado por el cliente. En general, un SSRF básico (cuando la respuesta se devuelve al atacante) es más fácil de explotar que un SSRF ciego, en el cual el atacante no tiene retroalimentación sobre si el ataque fue exitoso o no. | Los conceptos modernos en el desarrollo de aplicaciones fomentan que los desarrolladores accedan a URIs proporcionados por el cliente. La falta de validación o una validación incorrecta de dichos URIs es un problema común. Para detectar este problema, se requiere análisis de solicitudes y respuestas de la API. Cuando la respuesta no se devuelve (SSRF ciego), detectar la vulnerabilidad requiere más esfuerzo y creatividad. | Una explotación exitosa puede conducir a enumeración de servicios internos (p. ej., escaneo de puertos), divulgación de información, eludir firewalls u otros mecanismos de seguridad. En algunos casos, puede provocar DoS o que el servidor sea usado como proxy para ocultar actividades maliciosas. |

## ¿Es vulnerable la API?

Las fallas de **Server-Side Request Forgery (SSRF)** ocurren cuando una API obtiene un recurso remoto sin validar la URL proporcionada por el usuario. Esto permite que un atacante obligue a la aplicación a enviar una solicitud manipulada a un destino inesperado, incluso si está protegido por un firewall o una VPN.  

Los conceptos modernos de desarrollo hacen que SSRF sea más común y más peligroso:  

* **Más común**: características como *webhooks*, descarga de archivos desde URLs, SSO personalizado y vistas previas de URLs fomentan el acceso a recursos externos basados en entradas de usuario.  
* **Más peligroso**: tecnologías modernas como proveedores de nube, Kubernetes y Docker exponen canales de administración y control vía HTTP en rutas predecibles y conocidas. Estos canales son un objetivo fácil para ataques SSRF.  

Además, limitar el tráfico saliente de las aplicaciones es cada vez más difícil debido a la naturaleza conectada de los sistemas modernos.  

El riesgo de SSRF no siempre puede eliminarse por completo. Al elegir mecanismos de protección, es importante considerar los riesgos y necesidades del negocio.  

## Ejemplos de Escenarios de Ataque

### Escenario #1

Una red social permite a los usuarios subir fotos de perfil. El usuario puede elegir subir el archivo desde su computadora o proporcionar la URL de la imagen. En el segundo caso, se dispara la siguiente llamada:  

```
POST /api/profile/upload_picture

{
  "picture_url": "http://example.com/profile_pic.jpg"
}
```

Un atacante puede enviar una URL maliciosa y usar el endpoint de la API para iniciar un escaneo de puertos en la red interna:  

```
{
  "picture_url": "localhost:8080"
}
```

En función del tiempo de respuesta, el atacante puede deducir si el puerto está abierto o no.  

### Escenario #2

Un producto de seguridad genera eventos al detectar anomalías en la red. Algunos equipos prefieren revisar esos eventos en un sistema de monitoreo más general, como un SIEM. Para esto, el producto ofrece integración mediante *webhooks*.  

Durante la creación de un nuevo webhook, se envía una mutación GraphQL con la URL del API del SIEM:  

```
POST /graphql

[
  {
    "variables": {},
    "query": "mutation {
      createNotificationChannel(input: {
        channelName: \"ch_piney\",
        notificationChannelConfig: {
          customWebhookChannelConfigs: [
            {
              url: \"http://www.siem-system.com/create_new_event\",
              send_test_req: true
            }
          ]
        }
      }){
        channelId
      }
    }"
  }
]
```

El backend de la API envía una solicitud de prueba al webhook proporcionado y muestra la respuesta al usuario.  

Un atacante puede abusar de este flujo y apuntar a un recurso sensible, como el servicio interno de metadatos en la nube que expone credenciales:  

```
POST /graphql

[
  {
    "variables": {},
    "query": "mutation {
      createNotificationChannel(input: {
        channelName: \"ch_piney\",
        notificationChannelConfig: {
          customWebhookChannelConfigs: [
            {
              url: \"http://169.254.169.254/latest/meta-data/iam/security-credentials/ec2-default-ssm\",
              send_test_req: true
            }
          ]
        }
      }) {
        channelId
      }
    }
  }
]
```

Dado que la aplicación muestra la respuesta de la solicitud de prueba, el atacante puede visualizar las credenciales del entorno en la nube.  

## Cómo Prevenir

* Aísla el mecanismo de obtención de recursos en tu red: normalmente estas funciones deben recuperar recursos remotos, no internos.  
* Siempre que sea posible, usa listas de permitidos (*allow lists*) de:  
  * Orígenes remotos desde donde se espera descargar recursos (p. ej., Google Drive, Gravatar, etc.)  
  * Esquemas de URL y puertos aceptados  
  * Tipos de contenido permitidos para una funcionalidad específica  
* Deshabilita redirecciones HTTP.  
* Usa un analizador de URLs probado y mantenido para evitar problemas derivados de inconsistencias de parseo.  
* Valida y sanitiza todos los datos proporcionados por el cliente.  
* No devuelvas respuestas en bruto al cliente.  

## Referencias

### OWASP

* [Server Side Request Forgery][1]  
* [Server-Side Request Forgery Prevention Cheat Sheet][2]  

### Externas

* [CWE-918: Server-Side Request Forgery (SSRF)][3]  
* [URL confusion vulnerabilities in the wild: Exploring parser inconsistencies, Snyk][4]  

[1]: https://owasp.org/www-community/attacks/Server_Side_Request_Forgery  
[2]: https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html  
[3]: https://cwe.mitre.org/data/definitions/918.html  
[4]: https://snyk.io/blog/url-confusion-vulnerabilities/  
