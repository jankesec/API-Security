# Riesgos de Seguridad en APIs

Se utilizó la [Metodología de Evaluación de Riesgos de OWASP][1] para realizar el análisis de riesgos.

La siguiente tabla resume la terminología asociada con la puntuación de riesgo.

| Agentes de Amenaza | Explotabilidad | Prevalencia de la Debilidad | Detectabilidad de la Debilidad | Impacto Técnico | Impactos en el Negocio |
| :-: | :-: | :-: | :-: | :-: | :-: |
| Específico de APIs | Fácil: **3** | Generalizado **3** | Fácil **3** | Severo **3** | Específico del Negocio |
| Específico de APIs | Promedio: **2** | Común **2** | Promedio **2** | Moderado **2** | Específico del Negocio |
| Específico de APIs | Difícil: **1** | Difícil **1** | Difícil **1** | Menor **1** | Específico del Negocio |

**Nota**: Este enfoque no toma en cuenta la probabilidad de que un agente de amenaza ataque. Tampoco considera detalles técnicos específicos de tu aplicación. Cualquiera de estos factores podría afectar significativamente la probabilidad de que un atacante encuentre y explote una vulnerabilidad en particular. Esta clasificación tampoco evalúa el impacto real en tu negocio.  

Cada organización debe decidir cuánto riesgo de seguridad en aplicaciones y APIs está dispuesta a aceptar, según su cultura, industria y entorno regulatorio. El propósito del **OWASP API Security Top 10** no es realizar este análisis de riesgos por ti. Dado que esta edición no está basada en datos cuantitativos, la prevalencia de las debilidades se determina mediante el consenso de los miembros del equipo.

## Referencias

### OWASP

* [Metodología de Evaluación de Riesgos de OWASP][1]
* [Artículo sobre Modelado de Amenazas/Riesgos][2]

### Externas

* [ISO 31000: Gestión de Riesgos][3]
* [ISO 27001: Sistema de Gestión de Seguridad de la Información (SGSI)][4]
* [Marco de Ciberseguridad del NIST (EE.UU.)][5]
* [Mitigaciones Estratégicas del ASD (Australia)][6]
* [NIST CVSS 3.0][7]
* [Herramienta de Modelado de Amenazas de Microsoft][8]

[1]: https://owasp.org/www-project-risk-assessment-framework/
[2]: https://owasp.org/www-community/Threat_Modeling
[3]: https://www.iso.org/iso-31000-risk-management.html
[4]: https://www.iso.org/isoiec-27001-information-security.html
[5]: https://www.nist.gov/cyberframework
[6]: https://www.asd.gov.au/infosec/mitigationstrategies.htm
[7]: https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator
[8]: https://www.microsoft.com/en-us/download/details.aspx?id=49168

