# Metodología y Datos

## Resumen

Para esta actualización de la lista, el equipo de OWASP API Security utilizó la misma metodología aplicada en la lista exitosa y ampliamente adoptada de 2019, con la adición de un [llamado público a contribución de datos][1] de 3 meses. Desafortunadamente, este llamado no resultó en datos que permitieran un análisis estadístico relevante de los problemas de seguridad de API más comunes.  

Sin embargo, con una industria de seguridad de APIs más madura, capaz de proporcionar retroalimentación e información directa, el proceso de actualización avanzó utilizando la misma metodología que antes.  

Con ello, creemos haber elaborado un buen documento de concienciación con visión de futuro para los próximos tres o cuatro años, más enfocado en problemas específicos de APIs modernas. El objetivo de este proyecto no es reemplazar otras listas top 10, sino cubrir los riesgos de seguridad en APIs existentes y emergentes que creemos que la industria debe conocer y atender con diligencia.  

## Metodología

En la primera fase, se recopilaron, revisaron y categorizaron datos públicamente disponibles sobre incidentes de seguridad en APIs. Dichos datos se obtuvieron de plataformas de *bug bounty* y de informes públicos. Solo se consideraron los problemas reportados entre 2019 y 2022. Estos datos se usaron para dar al equipo una idea de hacia dónde debía evolucionar la lista top 10 anterior, así como para ayudar a manejar posibles sesgos en los datos contribuidos.  

Un [llamado público a contribución de datos][1] se llevó a cabo entre el 1 de septiembre y el 30 de noviembre de 2022. En paralelo, el equipo del proyecto inició la discusión sobre lo que había cambiado desde 2019. La discusión incluyó el impacto de la primera lista, la retroalimentación recibida de la comunidad y las nuevas tendencias en seguridad de APIs.  

El equipo del proyecto promovió reuniones con especialistas en amenazas relevantes de seguridad en APIs para obtener información sobre cómo se ven afectadas las víctimas y cómo se pueden mitigar esas amenazas.  

Este esfuerzo resultó en un borrador inicial de lo que el equipo consideraba eran los diez riesgos de seguridad en APIs más críticos. Se utilizó la [Metodología de Evaluación de Riesgos OWASP][2] para realizar el análisis de riesgos. Las calificaciones de prevalencia se decidieron por consenso entre los miembros del equipo del proyecto, basándose en su experiencia en el campo. Para consideraciones sobre estos temas, consulta la sección de [Riesgos de Seguridad en APIs][3].  

El borrador inicial fue compartido para revisión con profesionales de seguridad con experiencia relevante en el campo de la seguridad en APIs. Sus comentarios fueron revisados, discutidos y, cuando correspondía, incluidos en el documento. El documento resultante se [publicó como Candidato a Publicación][4] para [discusión abierta][5]. Varias [contribuciones de la comunidad][6] se incluyeron en el documento final.  

La lista de contribuyentes está disponible en la sección de [Agradecimientos][7].  

## Riesgos Específicos de APIs

La lista está construida para abordar riesgos de seguridad que son más específicos de las APIs.  

Esto no implica que otros riesgos genéricos de seguridad de aplicaciones no existan en aplicaciones basadas en APIs. Por ejemplo, no incluimos riesgos como "Componentes Vulnerables y Desactualizados" o "Inyección", aunque puedan encontrarse en aplicaciones basadas en APIs. Estos riesgos son genéricos, no se comportan de manera diferente en las APIs ni su explotación es distinta.  

Nuestro objetivo es aumentar la concienciación sobre riesgos de seguridad que merecen especial atención en APIs.  

[1]: https://owasp.org/www-project-api-security/announcements/cfd/2022/  
[2]: https://www.owasp.org/index.php/OWASP_Risk_Rating_Methodology  
[3]: ./0x10-api-security-risks.md  
[4]: https://owasp.org/www-project-api-security/announcements/2023/02/api-top10-2023rc  
[5]: https://github.com/OWASP/API-Security/issues?q=is%3Aissue+label%3A2023RC  
[6]: https://github.com/OWASP/API-Security/pulls?q=is%3Apr+label%3A2023RC  
[7]: ./0xd1-acknowledgments.md  
