# API9:2023 Gestión Inadecuada de Inventario

| Agentes de amenaza/Vectores de ataque | Debilidad de seguridad | Impactos |
| - | - | - |
| Específico de API: Explotabilidad **Fácil** | Prevalencia **Generalizada** : Detectabilidad **Media** | Técnico **Moderado** : Específico del negocio |
| Los agentes de amenaza suelen obtener acceso no autorizado a través de versiones antiguas de API o endpoints que permanecen en ejecución sin parches y con requisitos de seguridad más débiles. En algunos casos existen exploits disponibles. Alternativamente, pueden obtener acceso a datos sensibles a través de un tercero con el cual no hay razón para compartirlos. | La documentación desactualizada dificulta encontrar y/o corregir vulnerabilidades. La falta de inventario de activos y estrategias de retiro conlleva a la ejecución de sistemas sin parches, lo que resulta en filtración de datos sensibles. Es común encontrar hosts de API innecesariamente expuestos debido a conceptos modernos como microservicios, que facilitan el despliegue de aplicaciones de forma independiente (p. ej., computación en la nube, K8S). Sencillas búsquedas en Google (*Google Dorking*), enumeración de DNS o el uso de motores de búsqueda especializados para varios tipos de servidores (cámaras web, enrutadores, servidores, etc.) conectados a Internet serán suficientes para descubrir objetivos. | Los atacantes pueden obtener acceso a datos sensibles o incluso tomar el control del servidor. A veces, diferentes versiones/implementaciones de la API están conectadas a la misma base de datos con datos reales. Los agentes de amenaza pueden explotar endpoints obsoletos disponibles en versiones antiguas de la API para acceder a funciones administrativas o aprovechar vulnerabilidades conocidas. |

## ¿Es vulnerable la API?

La naturaleza dispersa y conectada de las APIs y las aplicaciones modernas trae nuevos desafíos. Es importante que las organizaciones no solo tengan un buen entendimiento y visibilidad de sus propias APIs y endpoints, sino también de cómo las APIs almacenan o comparten datos con terceros externos.  

Ejecutar múltiples versiones de una API requiere recursos adicionales de gestión por parte del proveedor y amplía la superficie de ataque.  

Una API tiene un "<ins>punto ciego de documentación</ins>" si:

* El propósito de un host de API no está claro y no hay respuestas explícitas a las siguientes preguntas:  
  * ¿En qué entorno se está ejecutando la API (p. ej., producción, staging, pruebas, desarrollo)?  
  * ¿Quién debería tener acceso de red a la API (p. ej., público, interno, socios)?  
  * ¿Qué versión de API se está ejecutando?  
* No existe documentación o la documentación existente no está actualizada.  
* No hay un plan de retiro para cada versión de la API.  
* El inventario de hosts está ausente o desactualizado.  

La visibilidad e inventario de los flujos de datos sensibles juegan un papel importante como parte de un plan de respuesta a incidentes, en caso de que ocurra una brecha en el lado de un tercero.  

Una API tiene un "<ins>punto ciego de flujo de datos</ins>" si:

* Existe un "flujo de datos sensible" donde la API comparte datos sensibles con un tercero y:  
  * No existe una justificación comercial ni aprobación del flujo.  
  * No existe inventario ni visibilidad del flujo.  
  * No existe visibilidad profunda del tipo de datos sensibles compartidos.  

## Ejemplos de Escenarios de Ataque

### Escenario #1

Una red social implementó un mecanismo de limitación de tasa (*rate limiting*) que bloquea a los atacantes para que no usen fuerza bruta al adivinar tokens de restablecimiento de contraseña. Este mecanismo no se implementó como parte del código de la API, sino en un componente separado entre el cliente y la API oficial (`api.socialnetwork.owasp.org`).  

Un investigador encontró un host beta de la API (`beta.api.socialnetwork.owasp.org`) que ejecutaba la misma API, incluido el mecanismo de restablecimiento de contraseñas, pero el mecanismo de limitación de tasa no estaba presente. El investigador pudo restablecer la contraseña de cualquier usuario utilizando fuerza bruta simple para adivinar el token de 6 dígitos.  

### Escenario #2

Una red social permite a desarrolladores de aplicaciones independientes integrarse con ella. Como parte de este proceso, se solicita el consentimiento del usuario final para que la red social pueda compartir su información personal con la aplicación independiente.  

El flujo de datos entre la red social y las aplicaciones independientes no es lo suficientemente restrictivo ni monitoreado, lo que permite a las aplicaciones acceder no solo a la información del usuario, sino también a la información privada de todos sus amigos.  

Una firma de consultoría construye una aplicación maliciosa y logra obtener el consentimiento de 270,000 usuarios. Debido a la falla, la firma obtiene acceso a la información privada de 50,000,000 de usuarios. Posteriormente, la firma vende la información para fines maliciosos.  

## Cómo Prevenir

* Inventariar todos los <ins>hosts de API</ins> y documentar aspectos importantes de cada uno de ellos, enfocándose en el entorno de la API (p. ej., producción, staging, pruebas, desarrollo), quién debería tener acceso de red al host (p. ej., público, interno, socios) y la versión de API.  
* Inventariar <ins>servicios integrados</ins> y documentar aspectos importantes como su rol en el sistema, qué datos se intercambian (flujo de datos) y su sensibilidad.  
* Documentar todos los aspectos de la API como autenticación, errores, redirecciones, limitación de tasa, política de *Cross-Origin Resource Sharing (CORS)* y endpoints, incluyendo sus parámetros, solicitudes y respuestas.  
* Generar documentación automáticamente adoptando estándares abiertos. Incluir la construcción de documentación en el pipeline de CI/CD.  
* Poner la documentación de la API a disposición solo de quienes estén autorizados para usar la API.  
* Usar medidas de protección externas como soluciones específicas de seguridad de API para todas las versiones expuestas de tus APIs, no solo para la versión de producción actual.  
* Evitar usar datos de producción en implementaciones de API que no sean de producción. Si esto es inevitable, estos endpoints deben recibir el mismo tratamiento de seguridad que los de producción.  
* Cuando versiones más nuevas de las APIs incluyan mejoras de seguridad, realizar un análisis de riesgos para informar las acciones de mitigación necesarias para las versiones anteriores. Por ejemplo, si es posible aplicar mejoras retroactivamente sin romper la compatibilidad de la API o si es necesario retirar rápidamente la versión anterior y obligar a todos los clientes a migrar a la última versión.  

## Referencias

### Externas

* [CWE-1059: Incomplete Documentation][1]  

[1]: https://cwe.mitre.org/data/definitions/1059.html  
