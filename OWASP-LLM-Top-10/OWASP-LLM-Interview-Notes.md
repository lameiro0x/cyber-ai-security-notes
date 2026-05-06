# OWASP LLM Interview Notes

> Notas para explicar OWASP LLM Top 10 con claridad profesional en entrevistas de prácticas, junior cybersecurity o roles orientados a AI Security.

## Explicación breve de cada riesgo

| Riesgo | Explicación breve |
|---|---|
| LLM01 Prompt Injection | El usuario o una fuente externa introduce instrucciones que alteran el comportamiento del modelo. |
| LLM02 Sensitive Information Disclosure | El sistema revela PII, secretos, datos internos, prompts o información propietaria. |
| LLM03 Supply Chain | Modelos, datasets, librerías, plugins o proveedores externos introducen vulnerabilidades. |
| LLM04 Data and Model Poisoning | Datos o modelos manipulados introducen sesgos, backdoors o respuestas incorrectas. |
| LLM05 Improper Output Handling | La salida del LLM se usa sin validación en navegadores, APIs, queries o acciones. |
| LLM06 Excessive Agency | El agente tiene demasiadas herramientas, permisos o autonomía para su función. |
| LLM07 System Prompt Leakage | El prompt de sistema o reglas internas se exponen, especialmente si contienen secretos. |
| LLM08 Vector and Embedding Weaknesses | La base vectorial o el retrieval filtran datos, mezclan permisos o recuperan contenido manipulado. |
| LLM09 Misinformation | El modelo produce información falsa, incompleta o alucinada que los usuarios pueden confiar. |
| LLM10 Unbounded Consumption | El sistema permite consumo excesivo de tokens, coste, cómputo o queries de extracción. |

## Preguntas típicas y respuestas modelo

### ¿Qué diferencia hay entre seguridad tradicional y seguridad LLM?

En AppSec tradicional protegemos entradas, sesiones, autorización, APIs y salidas. En seguridad LLM mantenemos todo eso, pero añadimos un componente no determinista que interpreta instrucciones en lenguaje natural, consume contexto externo y puede activar herramientas. Por eso el modelo debe tratarse como no confiable: hay que validar entradas, salidas y acciones fuera del LLM.

### ¿Por qué prompt injection no se soluciona solo filtrando palabras?

Porque el ataque no depende de una palabra concreta. Puede estar obfuscado, traducido, dividido, incrustado en documentos o expresado de forma semántica. La mitigación real combina separación de contexto, límites de herramientas, autorización fuera del modelo, validación de salida, detección y pruebas continuas.

### ¿Qué diferencia hay entre prompt injection directo e indirecto?

El directo lo introduce el usuario en la conversación. El indirecto llega desde datos que el sistema procesa, como una web, un email, un ticket o un documento recuperado por RAG. El indirecto suele ser más peligroso porque el usuario puede no verlo y el sistema puede tratarlo como información confiable.

### ¿Cómo mitigarías prompt injection en un RAG empresarial?

Separaría instrucciones y datos recuperados, clasificaría fuentes, validaría documentos antes de indexar, aplicaría retrieval con permisos del usuario, bloquearía tool calls cuando haya contexto no confiable, validaría salida y registraría qué documentos influyeron en cada respuesta.

### ¿Por qué no debemos poner secretos en el system prompt?

Porque el prompt de sistema no es un secreto robusto. Puede filtrarse por comportamiento del modelo, logs, errores, configuración o ingeniería inversa conversacional. Los secretos deben estar en secret managers y los permisos deben aplicarse en backend, no en instrucciones al modelo.

### ¿Qué es excessive agency?

Es dar al LLM demasiada capacidad de actuar: demasiadas herramientas, permisos amplios o autonomía sin confirmación. Un fallo del modelo, una alucinación o un prompt injection puede convertirse en una acción real como enviar correos, modificar documentos o llamar APIs.

### ¿Cómo auditarías un agente con herramientas?

Revisaría inventario de tools, permisos, identidad usada, acciones mutativas, validación de argumentos, logs, límites de iteración, aprobación humana y si las tools autorizan en backend. También probaría si contexto no confiable puede activar herramientas.

### ¿Qué es RAG poisoning?

Es insertar o manipular contenido en la base de conocimiento para que el sistema recupere información maliciosa, falsa o con instrucciones ocultas. El impacto puede ser misinformation, prompt injection indirecto, fuga de datos o manipulación de decisiones.

### ¿Qué controles defensivos son más importantes?

Mínimo privilegio, autorización fuera del LLM, separación de datos e instrucciones, validación de entrada y salida, logging de tool calls y retrieval, rate limiting, evaluación continua y human-in-the-loop para acciones críticas.

### ¿Cómo hablarías de MITRE ATLAS en una entrevista?

MITRE ATLAS me ayuda a convertir riesgos AI en tácticas y técnicas adversarias. OWASP me da los riesgos principales; ATLAS me ayuda a describir comportamientos de ataque, diseñar detecciones y estructurar ejercicios red/purple team.

## Frases profesionales útiles

- "No considero el prompt un boundary de seguridad; lo trato como una capa de orientación."
- "La autorización debe vivir en sistemas deterministas, no en el modelo."
- "Un LLM no solo responde texto; en una arquitectura agentic puede activar acciones reales."
- "En RAG, la seguridad depende tanto del retrieval y los permisos como del modelo."
- "La salida del modelo debe validarse como cualquier otra entrada no confiable."
- "El objetivo de purple team en AI no es hacer jailbreak por espectáculo, sino validar controles medibles."
- "Para producción, necesito trazabilidad: qué prompt, qué contexto, qué documentos, qué tool calls y qué salida."

## Comparación: AI Security ofensiva, defensiva y purple team

| Enfoque | Objetivo | Ejemplos |
|---|---|---|
| Ofensiva | Simular abuso y demostrar impacto | Prompt injection, RAG poisoning, tool abuse, leakage testing |
| Defensiva | Prevenir, detectar y responder | Guardrails, logging, rate limiting, access control, alertas |
| Purple Team | Unir ataque y defensa para mejorar controles | Test cases, detección, validación, retest, métricas |

## Cómo vender este conocimiento en una entrevista junior

Mensaje recomendado:

> "Vengo de una base de pentesting/red team y estoy orientando ese enfoque hacia AI Security. Me interesa especialmente cómo los LLMs cambian la superficie de ataque: prompt injection, RAG, agentes, herramientas y datos sensibles. Mi forma de trabajarlo es purple team: entiendo el abuso, diseño controles, creo test cases, reviso logs y valido que la mitigación funcione."

Puntos que puedes destacar:

- Conoces OWASP LLM Top 10 como marco base.
- Entiendes que el riesgo está en la aplicación completa, no solo en el modelo.
- Puedes hablar de RAG, agentes, permisos y output handling.
- Sabes conectar riesgos con controles defensivos.
- Tienes mentalidad de auditoría: evidencia, impacto, mitigación y retest.

## Mini respuestas de 30 segundos

### Prompt injection

Prompt injection ocurre cuando una entrada altera las instrucciones del modelo. Puede ser directa desde el usuario o indirecta desde documentos, emails o webs. No se mitiga solo filtrando palabras; hay que separar instrucciones y datos, limitar herramientas, validar salidas y aplicar autorización fuera del LLM.

### Sensitive information disclosure

Es la filtración de PII, secretos o datos internos por la respuesta del LLM, el RAG, logs o tools. La defensa pasa por minimización de datos, clasificación, DLP, permisos por usuario, redaction, logging seguro y no poner secretos en prompts.

### Excessive agency

Es dar al agente más herramientas, permisos o autonomía de la necesaria. Si el modelo se equivoca o recibe una inyección, puede ejecutar acciones reales. La mitigación es mínimo privilegio, tools específicas, autorización backend, límites de pasos y human-in-the-loop.

### Misinformation

El modelo puede generar respuestas falsas pero creíbles. En negocio, eso afecta decisiones, soporte, legal, salud o finanzas. Hay que usar grounding, citas verificables, abstención, revisión humana en high-stakes y evaluación continua.

## Preguntas para practicar

- ¿Cómo harías threat modeling de una aplicación RAG?
- ¿Qué logs necesitas para investigar un incidente LLM?
- ¿Qué controles pondrías antes de permitir tool calls?
- ¿Cómo distinguirías un fallo de modelo de un fallo de arquitectura?
- ¿Qué diferencia hay entre data poisoning y vector/embedding weaknesses?
- ¿Qué significa "treat the model as untrusted" en una arquitectura real?
- ¿Cómo medirías si una mitigación contra prompt injection funciona?
- ¿Qué harías si un chatbot filtra datos sensibles?

## Enlaces relacionados

- [[OWASP LLM Top 10]]
- [[OWASP-LLM-Audit-Checklist]]
- [[OWASP-LLM-Purple-Team-Playbook]]
- [[Prompt Injection]]
- [[RAG Security]]
- [[AI Red Teaming]]
- [[AI Security Controls]]
