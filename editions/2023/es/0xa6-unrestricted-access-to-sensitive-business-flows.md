# API6:2023 Acceso Sin Restricciones a Flujos de Negocio Sensibles

| Agentes de amenaza/Vectores de ataque | Debilidad de seguridad | Impactos |
| - | - | - |
| Específico de API: Explotabilidad **Fácil** | Prevalencia **Generalizada** : Detectabilidad **Media** | Técnico **Moderado** : Específico del negocio |
| La explotación usualmente implica comprender el modelo de negocio respaldado por la API, identificar flujos de negocio sensibles y automatizar el acceso a estos flujos, causando daño al negocio. | La falta de una visión holística de la API para dar soporte completo a los requerimientos del negocio tiende a contribuir a la prevalencia de este problema. Los atacantes identifican manualmente qué recursos (p. ej., endpoints) están involucrados en el flujo objetivo y cómo trabajan entre sí. Si ya existen mecanismos de mitigación, los atacantes buscan una forma de evadirlos. | En general, no se espera un impacto técnico significativo. Sin embargo, la explotación puede dañar al negocio de diferentes maneras, por ejemplo: impedir que usuarios legítimos compren un producto o generar inflación en la economía interna de un juego. |

## ¿Es vulnerable la API?

Al crear un endpoint de API, es importante comprender qué flujo de negocio expone.  
Algunos flujos de negocio son más sensibles que otros, en el sentido de que un acceso excesivo a ellos puede dañar al negocio.

Ejemplos comunes de flujos de negocio sensibles y riesgos de acceso excesivo asociados:

* Flujo de compra de un producto: un atacante puede comprar todo el stock de un artículo de alta demanda de una sola vez y revenderlo a un precio mayor (*scalping*).  
* Flujo de creación de comentarios/publicaciones: un atacante puede inundar el sistema con spam.  
* Flujo de reservas: un atacante puede reservar todos los horarios disponibles e impedir que otros usuarios utilicen el sistema.  

El riesgo de acceso excesivo puede variar según la industria y el tipo de negocio.  
Por ejemplo, la creación de publicaciones mediante un script podría considerarse spam en una red social, pero ser alentada en otra.  

Un endpoint de API es vulnerable si expone un flujo de negocio sensible sin restringir adecuadamente el acceso a él.  

## Ejemplos de Escenarios de Ataque

### Escenario #1

Una empresa tecnológica anuncia que lanzará una nueva consola de videojuegos en **Thanksgiving**. El producto tiene una demanda muy alta y el stock es limitado.  
Un atacante escribe código para comprar automáticamente el nuevo producto y completar la transacción.  

El día del lanzamiento, el atacante ejecuta el código distribuido en diferentes direcciones IP y ubicaciones. La API no implementa la protección adecuada y le permite comprar la mayoría del stock antes que los usuarios legítimos.  

Posteriormente, el atacante revende el producto en otra plataforma a un precio mucho mayor.  

### Escenario #2

Una aerolínea ofrece compra de boletos en línea sin tarifa de cancelación.  
Un usuario malicioso reserva el 90% de los asientos de un vuelo deseado.  

Días antes del vuelo, el usuario cancela todos los boletos de una sola vez, obligando a la aerolínea a bajar los precios para llenar el avión.  

En ese momento, el usuario compra un solo boleto mucho más barato que el precio original.  

### Escenario #3

Una aplicación de transporte por plataforma ofrece un programa de referidos: los usuarios pueden invitar amigos y obtener crédito por cada registro. Dicho crédito puede usarse para reservar viajes.  

Un atacante explota este flujo escribiendo un script que automatiza el proceso de registro, de modo que cada cuenta falsa agrega crédito a su cartera.  

Después, el atacante disfruta viajes gratis o vende las cuentas con crédito acumulado por dinero.  

## Cómo Prevenir

La planificación de la mitigación debe hacerse en dos capas:

* **Negocio**: identificar los flujos de negocio que podrían dañar a la organización si son usados de manera excesiva.  
* **Ingeniería**: elegir los mecanismos de protección adecuados para mitigar el riesgo empresarial.  

Algunas medidas son simples, mientras que otras son más complejas de implementar. Entre los métodos comunes para frenar amenazas automatizadas se encuentran:

* **Huellas digitales de dispositivos**: denegar servicio a clientes inesperados (p. ej., navegadores sin interfaz gráfica) obliga a los atacantes a usar soluciones más sofisticadas y costosas.  
* **Detección humana**: usar captcha o soluciones biométricas más avanzadas (p. ej., patrones de tipeo).  
* **Patrones no humanos**: analizar el flujo de uso para detectar patrones sospechosos (p. ej., un usuario accede a “añadir al carrito” y “completar compra” en menos de un segundo).  
* **Bloqueo de proxies/Tor**: considerar el bloqueo de direcciones IP de nodos de salida de Tor y proxys conocidos.  

Además, se debe asegurar y limitar el acceso a APIs consumidas directamente por máquinas (como APIs de desarrolladores o B2B). Estas suelen ser objetivos fáciles porque a menudo no implementan todos los mecanismos de protección requeridos.  

## Referencias

### OWASP

* [OWASP Automated Threats to Web Applications][1]  
* [API10:2019 Insufficient Logging & Monitoring][2]  

[1]: https://owasp.org/www-project-automated-threats-to-web-applications/  
[2]: https://owasp.org/API-Security/editions/2019/en/0xaa-insufficient-logging-monitoring/  
