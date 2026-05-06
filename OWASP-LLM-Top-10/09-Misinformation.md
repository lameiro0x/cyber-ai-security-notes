---
tags:
  - ai-security
  - owasp-llm
  - hallucinations
aliases:
  - Misinformation
  - LLM09 Misinformation
  - LLM09:2025 Misinformation
---

# LLM09:2025 Misinformation

## Resumen ejecutivo

**Misinformation** ocurre cuando un LLM produce información falsa, engañosa, incompleta o no verificada que parece creíble. Incluye alucinaciones, afirmaciones sin soporte, citas falsas, código inseguro, recomendaciones erróneas y exceso de confianza del usuario.

El riesgo aumenta cuando la respuesta se usa para decisiones legales, médicas, financieras, de seguridad, soporte a clientes o desarrollo de software.

## Descripción técnica

Cómo aparece:

- El modelo inventa hechos, fuentes o citas.
- El sistema RAG recupera documentos irrelevantes y el modelo rellena huecos.
- El asistente presenta incertidumbre como certeza.
- El modelo recomienda paquetes, funciones o configuraciones inexistentes.
- La UI no comunica límites de fiabilidad.
- Los usuarios integran respuestas sin revisión.

Componentes afectados:

- Modelo.
- Prompt y políticas de abstención.
- RAG y ranking.
- Evaluadores automáticos.
- UI/API.
- Procesos de revisión humana.

Diferencias frente a vulnerabilidades clásicas:

- No siempre hay atacante.
- Puede ser un fallo de calidad que se convierte en riesgo de seguridad.
- La explotación puede consistir en inducir dependencia excesiva o aprovechar hallucinated dependencies.
- El impacto se materializa en decisiones humanas o workflows posteriores.

Por qué puede pasar desapercibido:

- La respuesta suena profesional.
- Los usuarios confían en tono y fluidez.
- Las pruebas manuales no cubren incertidumbre.
- Las métricas de utilidad no miden veracidad.
- Las citas pueden parecer correctas visualmente.

## Escenario realista

Un asistente de soporte responde preguntas sobre políticas de devolución. En una situación no cubierta por la documentación, el modelo inventa una excepción favorable al cliente. El cliente usa la respuesta como evidencia y la empresa debe asumir coste o conflicto legal.

El sistema no fue atacado. Falló la falta de grounding, abstención y revisión para respuestas con impacto contractual.

## Ejemplo de ataque

Ejemplo conceptual y seguro:

1. El atacante identifica que el asistente responde con confianza en temas poco documentados.
2. Formula preguntas ambiguas o de borde.
3. El modelo genera una respuesta no soportada.
4. El atacante usa la respuesta como evidencia o para inducir una acción.

Qué intenta conseguir el atacante:

- Obtener una declaración favorable.
- Forzar soporte, reembolso o excepción.
- Inducir uso de código o paquetes inseguros.
- Reducir confianza en la organización.

Controles que fallan:

- Sin grounding obligatorio.
- Sin citas verificables.
- Sin abstención ante baja confianza.
- Sin revisión humana en high-stakes.
- Sin evaluación de factualidad.

Impacto:

- Decisiones incorrectas.
- Daño legal o financiero.
- Vulnerabilidades por código sugerido.
- Pérdida de confianza.

## Impacto

| Dimensión | Impacto |
|---|---|
| Confidencialidad | Misinformation puede inducir consultas o acciones que expongan datos. |
| Integridad | Decisiones basadas en información falsa o no verificada. |
| Disponibilidad | Corrección manual masiva, retirada de respuestas o bloqueo del asistente. |
| Privacidad | Consejos incorrectos sobre manejo de datos personales. |
| Cumplimiento | Respuestas no alineadas con obligaciones legales o regulatorias. |
| Reputación | Capturas de respuestas falsas, asesoramiento incorrecto o promesas no autorizadas. |
| Coste operativo | Reclamaciones, revisión humana, retraining, evals y cambios de producto. |

## Indicadores de riesgo

- El sistema responde aunque no tenga evidencia.
- No muestra fuentes ni nivel de confianza.
- No distingue hechos de recomendaciones.
- RAG no mide relevancia, groundedness ni respuesta soportada.
- No hay revisión humana para high-stakes.
- La UI incentiva copiar/pegar sin verificación.
- No hay evals de factualidad o hallucination.
- Se usa para código sin revisión de seguridad.

## Controles defensivos

- **Diseño seguro**: definir dominios permitidos y casos donde el sistema debe abstenerse.
- **Validación de entradas**: detectar preguntas fuera de scope o high-stakes.
- **Validación de salidas**: fact-checking, groundedness, citas verificables y policy checks.
- **Least privilege**: no permitir que respuestas no verificadas activen acciones automáticas.
- **Human-in-the-loop**: revisión en legal, salud, finanzas, seguridad, HR y decisiones irreversibles.
- **Logging y monitorización**: registrar baja confianza, ausencia de fuentes y correcciones humanas.
- **Rate limiting**: limitar probing de zonas grises o generación masiva de contenido.
- **Evaluaciones automáticas**: factualidad, RAG triad, hallucination, citation accuracy y secure code review.
- **Red teaming**: casos ambiguos, preguntas no cubiertas y dependencia excesiva.
- **Controles específicos**: respuestas con fuentes, abstención, disclaimers útiles, feedback loop y revisión de paquetes/código.

## Checklist de auditoría

- [ ] ¿El sistema puede decir "no lo sé" o escalar?
- [ ] ¿Las respuestas críticas requieren fuentes verificables?
- [ ] ¿Se evalúa groundedness?
- [ ] ¿Se mide factualidad y hallucination?
- [ ] ¿La UI comunica límites de fiabilidad?
- [ ] ¿Hay revisión humana en high-stakes?
- [ ] ¿Las recomendaciones de código pasan revisión?
- [ ] ¿Se bloquean afirmaciones sin fuente en dominios críticos?
- [ ] ¿Se registran correcciones de usuarios o revisores?
- [ ] ¿Hay proceso para corregir conocimiento erróneo?
- [ ] ¿Se prueban casos de borde y preguntas ambiguas?

## Preguntas de entrevista

- ¿Por qué misinformation es un riesgo de seguridad y no solo de calidad?
- ¿Qué relación hay entre hallucination y overreliance?
- ¿Cómo reducirías alucinaciones en un RAG empresarial?
- ¿Qué es groundedness?
- ¿Cómo diseñarías UI para reducir exceso de confianza?
- ¿Por qué el código generado por LLM puede introducir vulnerabilidades?

## Relación con Purple Team

**Red Team**:

- Busca preguntas ambiguas o no cubiertas.
- Prueba si el modelo inventa fuentes.
- Evalúa recomendaciones inseguras en código o procesos.

**Blue Team**:

- Monitoriza respuestas sin fuente, baja confianza y feedback negativo.
- Crea alertas por dominios high-stakes.
- Revisa patrones de alucinación recurrente.

**Purple Team**:

- Convierte alucinaciones en evals.
- Ajusta RAG, prompts de abstención y UI.
- Mide reducción de respuestas no soportadas.

## Mapeo con controles

- **OWASP ASVS**: aplicable cuando misinformation genera salida que afecta seguridad, APIs, datos o decisiones; especialmente logging, validación y control de cambios.
- **NIST AI RMF**: MEASURE para validez, fiabilidad y robustez; MAP para contexto de uso; MANAGE para mitigación de daños; GOVERN para accountability.
- **MITRE ATLAS**: `AML.T0062` aparece en mitigaciones de ATLAS relacionado con contenido alucinado; OWASP también relaciona misinformation con impactos sociales y de confianza.
- **Principios generales**: human oversight, defense in depth, secure by design, accountability, fail safe.

## Notas para Obsidian

Enlaces:

- [[OWASP LLM Top 10]]
- [[Misinformation]]
- [[Hallucinations]]
- [[RAG Security]]
- [[AI Red Teaming]]
- [[AI Security Controls]]

## Resumen final

- Idea clave: una respuesta falsa con tono convincente puede convertirse en riesgo real.
- Riesgo principal: decisiones o acciones basadas en información no verificada.
- Defensa más importante: grounding, abstención, fuentes verificables y revisión humana en high-stakes.
- Para entrevista: misinformation conecta AI Safety, seguridad, legal y producto.

## Fuentes base

- [OWASP LLM09:2025 Misinformation](https://genai.owasp.org/llmrisk/llm092025-misinformation/)
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
