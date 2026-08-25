
# Notas de la Versión

Esta es la segunda edición del OWASP API Security Top 10, exactamente cuatro años después de su primera publicación. Mucho ha cambiado en el panorama de la seguridad en APIs. El tráfico de APIs ha aumentado rápidamente, algunos protocolos de API han ganado mucha más popularidad, han surgido numerosos proveedores y soluciones de seguridad para APIs y, por supuesto, los atacantes han desarrollado nuevas habilidades y técnicas para comprometer APIs. Era momento de actualizar la lista de los diez riesgos de seguridad más críticos en APIs.

Con una industria de seguridad en APIs más madura, por primera vez, hubo [una convocatoria pública para recopilar datos][1]. Desafortunadamente, no se recibió ninguna contribución de datos, pero basándonos en la experiencia del equipo del proyecto, la cuidadosa revisión de especialistas en seguridad de APIs y los comentarios de la comunidad sobre la versión candidata, construimos esta nueva lista. En la [sección de Metodología y Datos][2], encontrarás más detalles sobre cómo se elaboró esta versión. Para conocer más detalles sobre los riesgos de seguridad, consulta la [sección de Riesgos de Seguridad en APIs][3].

El OWASP API Security Top 10 2023 es un documento de concienciación con visión de futuro para una industria de ritmo acelerado. No reemplaza a otros Top 10. En esta edición:

* Hemos combinado la Exposición Excesiva de Datos y la Asignación Masiva, enfocándonos en la causa raíz común: fallas en la validación de autorización a nivel de propiedad de objeto.
* Hemos puesto mayor énfasis en el consumo de recursos, en lugar de solo centrarnos en la rapidez con la que se agotan.
* Hemos creado una nueva categoría, **"Acceso Ilimitado a Flujos de Negocio Sensibles"**, para abordar nuevas amenazas, incluidas muchas que pueden mitigarse mediante limitación de tasas (rate limiting).
* Agregamos **"Consumo Inseguro de APIs"** para abordar un nuevo enfoque observado en los ataques: los atacantes han comenzado a buscar servicios integrados de un objetivo para comprometerlos, en lugar de atacar directamente las APIs de su objetivo. Este es el momento adecuado para generar conciencia sobre este riesgo creciente.

Las APIs desempeñan un papel cada vez más importante en la arquitectura moderna de microservicios, aplicaciones de una sola página (SPA), aplicaciones móviles, IoT, entre otros. El **OWASP API Security Top 10** es un esfuerzo necesario para crear conciencia sobre los problemas modernos de seguridad en APIs.

Esta actualización solo fue posible gracias al gran esfuerzo de varios voluntarios, quienes están listados en la [sección de Reconocimientos][4].

¡Gracias!

[1]: https://owasp.org/www-project-api-security/announcements/cfd/2022/
[2]: ./0xd0-about-data.md
[3]: ./0x10-api-security-risks.md
[4]: ./0xd1-acknowledgments.md
