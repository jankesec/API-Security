# API4:2023 Consumo de Recursos sin Restricciones

| Agentes de amenaza/Vectores de ataque | Debilidad de seguridad | Impactos |
| - | - | - |
| Específico de API: Explotabilidad **Media** | Prevalencia **Generalizada** : Detectabilidad **Fácil** | Técnico **Grave** : Específico del negocio |
| La explotación requiere solicitudes simples a la API. Pueden realizarse múltiples solicitudes concurrentes desde una sola computadora local o utilizando recursos de computación en la nube. La mayoría de las herramientas automatizadas disponibles están diseñadas para causar DoS mediante altas cargas de tráfico, afectando la tasa de servicio de las APIs. | Es común encontrar APIs que no limitan las interacciones de los clientes ni el consumo de recursos. Solicitudes API manipuladas, como aquellas con parámetros que controlan la cantidad de recursos devueltos y realizando análisis de estado/tiempo/longitud de la respuesta, deberían permitir identificar el problema. Lo mismo aplica para operaciones en lote. Aunque los agentes de amenaza no tienen visibilidad directa sobre el impacto en costos, este puede inferirse con base en el modelo de negocio/precios de los proveedores de servicios (p. ej., proveedor de nube). | La explotación puede llevar a DoS debido al agotamiento de recursos, pero también a un incremento de costos operativos, como mayor demanda de CPU, aumento en necesidades de almacenamiento en la nube, etc. |

## ¿Es vulnerable la API?

Satisfacer solicitudes de API requiere recursos como ancho de banda de red, CPU, memoria y almacenamiento. A veces, los recursos necesarios son provistos por proveedores de servicios mediante integraciones API y se pagan por solicitud, como envío de emails/SMS/llamadas telefónicas, validación biométrica, etc.

Una API es vulnerable si al menos uno de los siguientes límites falta o está configurado de manera inapropiada (p. ej., muy bajo/alto):

* Tiempos de espera de ejecución (timeouts)
* Memoria máxima asignable
* Número máximo de descriptores de archivo
* Número máximo de procesos
* Tamaño máximo de archivo para subir
* Número de operaciones a realizar en una sola solicitud de cliente API (p. ej., batching en GraphQL)
* Número de registros por página a devolver en una sola solicitud-respuesta
* Límite de gasto de proveedores de servicios externos

## Ejemplos de Escenarios de Ataque

### Escenario #1

Una red social implementó un flujo de “olvidé mi contraseña” utilizando verificación por SMS, permitiendo que el usuario reciba un token de un solo uso por SMS para restablecer su contraseña.

Cuando el usuario hace clic en “olvidé mi contraseña”, el navegador envía esta llamada al backend:

```
POST /initiate_forgot_password

{
  "step": 1,
  "user_number": "6501113434"
}
```

Luego, en segundo plano, el backend llama a una API de un tercero que entrega el SMS:

```
POST /sms/send_reset_pass_code

Host: willyo.net

{
  "phone_number": "6501113434"
}
```

El proveedor externo, Willyo, cobra 0.05 USD por este tipo de llamada.

Un atacante escribe un script que envía la primera llamada decenas de miles de veces. El backend, a su vez, solicita a Willyo el envío de decenas de miles de mensajes de texto, provocando que la empresa pierda miles de dólares en cuestión de minutos.

### Escenario #2

Un endpoint GraphQL permite al usuario subir una foto de perfil.

```
POST /graphql

{
  "query": "mutation {
    uploadPic(name: "pic1", base64_pic: "R0FOIEFOR0xJVA…") {
      url
    }
  }"
}
```

Una vez completada la subida, la API genera múltiples miniaturas de diferentes tamaños basadas en la imagen cargada. Esta operación gráfica consume mucha memoria del servidor.

La API implementa un rate limiting tradicional: un usuario no puede acceder demasiadas veces al endpoint GraphQL en un corto periodo. La API también valida el tamaño de la imagen antes de generar miniaturas para evitar procesar imágenes demasiado grandes.

Un atacante puede evadir esos mecanismos aprovechando la naturaleza flexible de GraphQL:

```
POST /graphql

[
  {"query": "mutation {uploadPic(name: "pic1", base64_pic: "R0FOIEFOR0xJVA…") {url}}"},
  {"query": "mutation {uploadPic(name: "pic2", base64_pic: "R0FOIEFOR0xJVA…") {url}}"},
  ...
  {"query": "mutation {uploadPic(name: "pic999", base64_pic: "R0FOIEFOR0xJVA…") {url}}"},
]
```

Como la API no limita la cantidad de veces que puede intentarse la operación `uploadPic`, la llamada produce agotamiento de memoria del servidor y Denegación de Servicio.

### Escenario #3

Un proveedor de servicios permite a sus clientes descargar archivos arbitrariamente grandes usando su API. Estos archivos se almacenan en un almacenamiento de objetos en la nube y no cambian con frecuencia. El proveedor depende de un servicio de caché para mejorar la tasa de servicio y reducir el consumo de ancho de banda. El servicio de caché solo guarda archivos de hasta 15 GB.

Cuando uno de los archivos se actualiza, su tamaño aumenta a 18 GB. Todos los clientes comienzan de inmediato a descargar la nueva versión. Como no había alertas de consumo ni un límite de costo máximo para el servicio en la nube, la factura mensual pasa de un promedio de 13 USD a 8,000 USD.

## Cómo Prevenir

* Usa una solución que facilite limitar [memoria][1], [CPU][2], [número de reinicios][3], [descriptores de archivos y procesos][4], como contenedores o código serverless (p. ej., Lambdas).
* Define y aplica un tamaño máximo de datos en todos los parámetros y payloads de entrada, como longitud máxima para cadenas, número máximo de elementos en arreglos y tamaño máximo de archivo (sin importar si se almacena localmente o en la nube).
* Implementa un límite de frecuencia sobre cómo y con qué frecuencia un cliente puede interactuar con la API dentro de un periodo definido (rate limiting).
* Ajusta el rate limiting según las necesidades del negocio. Algunos endpoints pueden requerir políticas más estrictas.
* Limita/restringe cuántas veces o con qué frecuencia un cliente/usuario puede ejecutar una misma operación (p. ej., validar un OTP o solicitar recuperación de contraseña sin visitar la URL de un solo uso).
* Agrega validación del lado del servidor para parámetros de query string y cuerpo de la solicitud, en particular los que controlan el número de registros devueltos en la respuesta.
* Configura límites de gasto para todos los proveedores/integraciones de servicio. Cuando no sea posible establecer límites de gasto, configura alertas de facturación.

## Referencias

### OWASP

* [“Availability” - Web Service Security Cheat Sheet][5]
* [“DoS Prevention” - GraphQL Cheat Sheet][6]
* [“Mitigating Batching Attacks” - GraphQL Cheat Sheet][7]

### Externas

* [CWE-770: Asignación de Recursos sin Límites o Regulación][8]
* [CWE-400: Consumo de Recursos sin Control][9]
* [CWE-799: Control Inadecuado de Frecuencia de Interacción][10]
* “Rate Limiting (Throttling)” - [Security Strategies for Microservices-based Application Systems][11], NIST

[1]: https://docs.docker.com/config/containers/resource_constraints/#memory
[2]: https://docs.docker.com/config/containers/resource_constraints/#cpu
[3]: https://docs.docker.com/engine/reference/commandline/run/#restart
[4]: https://docs.docker.com/engine/reference/commandline/run/#ulimit
[5]: https://cheatsheetseries.owasp.org/cheatsheets/Web_Service_Security_Cheat_Sheet.html#availability
[6]: https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html#dos-prevention
[7]: https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html#mitigating-batching-attacks
[8]: https://cwe.mitre.org/data/definitions/770.html
[9]: https://cwe.mitre.org/data/definitions/400.html
[10]: https://cwe.mitre.org/data/definitions/799.html
[11]: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-204.pdf
