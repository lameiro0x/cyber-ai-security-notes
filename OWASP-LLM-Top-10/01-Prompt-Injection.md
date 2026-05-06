---
tags:
  - ai-security
  - owasp-llm
  - prompt-injection
aliases:
  - Prompt Injection
  - LLM01 Prompt Injection
  - LLM01:2025 Prompt Injection
---

# LLM01:2025 Prompt Injection

## Resumen ejecutivo

**Prompt Injection** ocurre cuando una entrada directa del usuario o una fuente externa consigue alterar el comportamiento esperado del LLM. Es el riesgo más representativo de las aplicaciones LLM porque explota una debilidad estructural: el modelo procesa instrucciones, datos y contexto en el mismo espacio lingüístico.

El impacto depende de la arquitectura. En un chatbot simple puede causar respuestas fuera de política. En un agente con herramientas puede provocar consultas no autorizadas, exfiltración, acciones indebidas o manipulación de decisiones.

## Descripción técnica

Prompt injection aparece cuando el sistema no separa de forma robusta:

- Instrucciones del sistema.
- Mensajes del usuario.
- Contexto recuperado desde [[RAG Security]].
- Contenido externo como webs, emails, tickets, PDFs o repositorios.
- Salidas intermedias de tools o agentes.

Tipos principales:

- **Direct prompt injection**: el usuario introduce instrucciones adversarias directamente en el chat o API.
- **Indirect prompt injection**: las instrucciones están en contenido externo que el LLM procesa, por ejemplo un documento indexado en RAG.
- **Cross-context injection**: una instrucción pasa de un canal a otro, por ejemplo de un email a un agente que tiene acceso a calendario.
- **Multimodal injection**: la instrucción está escondida o representada en imagen, audio, OCR o metadatos.

Componentes afectados:

- Orquestador de prompts.
- RAG retriever y ranker.
- [[Vector Databases]].
- Agentes y tools.
- Output parser.
- Sistemas posteriores: email, CRM, repositorios, APIs internas.

Diferencia frente a vulnerabilidades clásicas:

- En SQLi, el problema está en mezclar datos y código dentro de una query.
- En prompt injection, el problema está en mezclar datos e instrucciones dentro del contexto del modelo.
- La defensa no puede depender solo de escaping o listas de palabras, porque el ataque es semántico, contextual y no siempre literal.

Por qué puede pasar desapercibido:

- Puede parecer una respuesta normal del modelo.
- Puede activarse solo cuando un documento concreto es recuperado.
- Puede no generar error técnico.
- Puede ser no determinista y aparecer solo en algunas ejecuciones.
- Puede esconderse en contenido que el usuario no ve.

## Escenario realista

Una empresa usa un asistente interno con RAG sobre documentación de proyectos y acceso a herramientas de ticketing. El asistente puede resumir tickets, buscar documentación y proponer cambios de prioridad.

Un atacante con acceso a crear tickets introduce instrucciones encubiertas en un ticket de baja prioridad. Cuando un manager pregunta al asistente por incidencias críticas, el sistema recupera ese ticket y el modelo trata las instrucciones incrustadas como si fueran parte del flujo de control.

## Ejemplo de ataque

Ejemplo conceptual y seguro:

1. El atacante coloca contenido no confiable en una fuente que el sistema indexa.
2. Un usuario legítimo hace una pregunta normal.
3. El retriever incluye el documento manipulado en el contexto.
4. El modelo prioriza la instrucción maliciosa frente a la política del sistema.
5. El agente intenta revelar información, alterar una recomendación o invocar una tool no prevista.

Qué intenta conseguir el atacante:

- Cambiar la respuesta del sistema.
- Acceder a información de otros documentos.
- Forzar una tool call.
- Inducir a error a un usuario con autoridad.
- Preparar una cadena hacia [[Excessive Agency]] o [[Sensitive Information Disclosure]].

Controles que fallan:

- Falta de separación entre contexto no confiable e instrucciones.
- RAG sin validación de documentos.
- Tools disponibles sin autorización externa.
- Ausencia de detección de instrucciones en contenido recuperado.
- Falta de revisión humana para acciones sensibles.

Impacto:

- Decisiones incorrectas.
- Filtración de contexto interno.
- Acciones no autorizadas.
- Pérdida de confianza en el asistente.

## Impacto

| Dimensión | Impacto |
|---|---|
| Confidencialidad | Exposición de datos internos, prompts, documentos o resultados de tools. |
| Integridad | Manipulación de respuestas, recomendaciones, prioridades o acciones. |
| Disponibilidad | Bucles, tool calls repetidas o degradación por prompts adversarios. |
| Privacidad | Revelación de PII recuperada por RAG o tools. |
| Cumplimiento | Incumplimiento de políticas de tratamiento de datos, secretos o registros. |
| Reputación | Capturas de respuestas inseguras o abuso público del chatbot. |
| Coste operativo | Investigación, reindexado, ajuste de prompts, incident response y retesting. |

## Indicadores de riesgo

- El sistema mezcla contenido recuperado con instrucciones sin delimitación.
- El modelo decide permisos o acciones críticas por lenguaje natural.
- No hay allowlist de herramientas según rol y tarea.
- El RAG indexa documentos sin revisión.
- Las respuestas no incluyen trazabilidad de fuentes.
- No se registran documentos recuperados ni tool calls.
- Los prompts de sistema se tratan como control suficiente.
- No existen evals adversarias ni pruebas de regresión.

## Controles defensivos

- **Diseño seguro**: separar instrucciones, datos del usuario y contexto recuperado con estructura explícita.
- **Validación de entradas**: clasificar prompts y documentos antes de procesarlos.
- **Validación de salidas**: comprobar formato, política, citas y acciones propuestas.
- **Least privilege**: limitar tools por rol, tarea y contexto.
- **Human-in-the-loop**: exigir aprobación para acciones que envíen, borren, modifiquen o consulten datos sensibles.
- **Logging y monitorización**: registrar prompt, contexto recuperado, puntuaciones, respuesta, tool calls y decisiones.
- **Rate limiting**: limitar intentos repetidos, prompts largos y abuso de tokens.
- **Evaluaciones automáticas**: datasets de prompt injection directo, indirecto, multi-turn y RAG.
- **Red teaming**: probar manipulación contextual, recuperación de documentos contaminados y tool abuse.
- **RAG específico**: sanitizar documentos, detectar instrucciones incrustadas, aplicar permisos por documento y mostrar fuentes.

## Checklist de auditoría

- [ ] ¿El sistema separa claramente instrucciones del sistema, datos del usuario y contexto recuperado?
- [ ] ¿Se trata el contexto RAG como no confiable?
- [ ] ¿Existen delimitadores o estructura que identifique fuentes no confiables?
- [ ] ¿Hay validación antes de indexar documentos?
- [ ] ¿El retriever respeta permisos del usuario?
- [ ] ¿Las tools tienen permisos mínimos y autorización externa?
- [ ] ¿Las acciones sensibles requieren confirmación humana?
- [ ] ¿Se registran documentos recuperados, prompt final y tool calls?
- [ ] ¿Hay detección de patrones de inyección directa e indirecta?
- [ ] ¿Existen evals de regresión para prompt injection?
- [ ] ¿El sistema puede abstenerse o pedir confirmación ante conflicto de instrucciones?

## Preguntas de entrevista

- ¿Por qué el prompt injection no se soluciona solo filtrando palabras?
- ¿Qué diferencia hay entre prompt injection directo e indirecto?
- ¿Por qué RAG puede aumentar la superficie de prompt injection?
- ¿Cómo mitigarías este riesgo en un sistema RAG empresarial?
- ¿Por qué el system prompt no debe ser tratado como un boundary de seguridad?
- ¿Qué logs necesitas para investigar un prompt injection?
- ¿Cómo relacionas prompt injection con excessive agency?

## Relación con Purple Team

**Red Team**:

- Simula inyección directa, indirecta y multi-turn usando datos sintéticos.
- Coloca instrucciones conceptuales en documentos controlados.
- Prueba si el modelo altera su política o invoca herramientas fuera de scope.

**Blue Team**:

- Detecta patrones de instrucciones adversarias.
- Correlaciona prompts, retrieval, tool calls y respuesta final.
- Crea alertas para documentos recuperados con contenido sospechoso.

**Purple Team**:

- Convierte cada bypass en un test case.
- Ajusta controles y evals.
- Mide bloqueo, detección, falsos positivos y severidad residual.

## Mapeo con controles

- **OWASP ASVS**: validación y sanitización de entradas, encoding de salidas, control de acceso, logging, manejo de errores y seguridad de APIs. Confirmar IDs exactos contra ASVS v5.0.0 si se requiere trazabilidad formal.
- **NIST AI RMF**: MAP para identificar contexto y fuentes de riesgo; MEASURE para evaluar robustez; MANAGE para controles, monitorización y respuesta; GOVERN para políticas de uso.
- **MITRE ATLAS**: `AML.T0051` LLM Prompt Injection; `AML.T0051.000` Direct; `AML.T0051.001` Indirect; `AML.T0054` LLM Jailbreak cuando el objetivo es saltarse políticas de seguridad.
- **Principios generales**: mínimo privilegio, defensa en profundidad, zero trust, separación de responsabilidades, secure by design.

## Notas para Obsidian

Enlaces:

- [[OWASP LLM Top 10]]
- [[Prompt Injection]]
- [[RAG Security]]
- [[AI Red Teaming]]
- [[LLM Agents]]
- [[Vector Databases]]
- [[AI Security Controls]]

## Resumen final

- Idea clave: cualquier texto procesado por el LLM puede comportarse como instrucción.
- Riesgo principal: que datos no confiables controlen respuestas o acciones.
- Defensa más importante: separar datos e instrucciones, limitar tools y validar acciones fuera del LLM.
- Para entrevista: prompt injection es un problema de arquitectura y límites de confianza, no solo de prompt engineering.

## Fuentes base

- [OWASP LLM01:2025 Prompt Injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)
- [MITRE ATLAS data](https://github.com/mitre-atlas/atlas-data)
