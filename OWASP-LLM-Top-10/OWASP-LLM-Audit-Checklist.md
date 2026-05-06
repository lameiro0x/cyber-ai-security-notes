# OWASP LLM Audit Checklist

> Checklist global para revisar aplicaciones basadas en LLMs, RAG, agentes y herramientas externas.

## Uso

Esta checklist sirve para auditoría, diseño seguro, pentesting controlado y preparación de ejercicios [[Purple Team]]. No sustituye a un threat model, pero ayuda a no olvidar superficies típicas de [[LLM Security]].

Marca cada punto como:

- `OK`: control presente y probado.
- `Gap`: control ausente o incompleto.
- `N/A`: no aplica al alcance.
- `Evidence`: referencia a log, captura, ticket, configuración o prueba.

## Arquitectura

- [ ] ¿Existe un diagrama actualizado del flujo LLM completo?
- [ ] ¿Se separan usuario, backend, orquestador, modelo, RAG, tools, APIs y logging?
- [ ] ¿Están definidos los límites de confianza entre datos internos, usuario y fuentes externas?
- [ ] ¿Se trata la salida del modelo como no confiable?
- [ ] ¿Hay threat modeling específico para [[Prompt Injection]], [[RAG Security]], agentes y datos sensibles?
- [ ] ¿Existe un modo seguro de degradación si el modelo o el proveedor fallan?
- [ ] ¿Hay separación entre entorno de desarrollo, pruebas y producción?

## Modelo

- [ ] ¿Está documentado el modelo usado, versión, proveedor, región y propósito?
- [ ] ¿Se conoce si los datos enviados al proveedor pueden usarse para entrenamiento?
- [ ] ¿Existe evaluación de seguridad antes de cambiar de modelo o versión?
- [ ] ¿Se registran cambios de parámetros críticos como temperature, max tokens, tools y system prompts?
- [ ] ¿Se han definido límites de uso aceptable y tareas fuera de alcance?
- [ ] ¿Existe fallback o bloqueo para baja confianza?

## Prompting

- [ ] ¿El system prompt evita incluir secretos, credenciales o información sensible?
- [ ] ¿Se separan claramente instrucciones del sistema, datos del usuario y contexto recuperado?
- [ ] ¿Se usan delimitadores y estructura consistente para contexto no confiable?
- [ ] ¿El prompt declara límites de rol, scope y formato de salida?
- [ ] ¿Hay pruebas de regresión contra cambios de prompt?
- [ ] ¿Se evalúa prompt injection directo e indirecto?
- [ ] ¿No se confía en el prompt como único control de autorización?

## RAG

- [ ] ¿Las fuentes de conocimiento están inventariadas y clasificadas?
- [ ] ¿Cada documento tiene owner, sensibilidad, origen y fecha?
- [ ] ¿Existe validación antes de indexar documentos?
- [ ] ¿Se detecta contenido oculto, obfuscado o instrucciones incrustadas?
- [ ] ¿El retrieval respeta permisos del usuario?
- [ ] ¿Se registran documentos recuperados, puntuaciones y filtros aplicados?
- [ ] ¿Se validan citas y fuentes antes de mostrarlas como evidencia?
- [ ] ¿Existe proceso para retirar documentos contaminados?

## Embeddings

- [ ] ¿El modelo de embeddings está documentado y versionado?
- [ ] ¿Se evalúa si los embeddings pueden revelar información sensible?
- [ ] ¿Se evita insertar secretos o PII innecesaria en el índice?
- [ ] ¿Hay estrategia de reindexado tras cambios de permisos o clasificación?
- [ ] ¿Se controla la calidad de chunking, metadatos y normalización?
- [ ] ¿Se prueba recuperación adversaria y queries de enumeración?

## Vector database

- [ ] ¿La base vectorial aplica controles de acceso por tenant, usuario o clasificación?
- [ ] ¿Hay particiones lógicas o físicas para datos sensibles?
- [ ] ¿Se evita mezclar datasets con permisos incompatibles?
- [ ] ¿Se auditan consultas, filtros, top-k y resultados devueltos?
- [ ] ¿Se cifran datos y metadatos sensibles en reposo y tránsito?
- [ ] ¿Existe backup, borrado seguro y rotación de índices?
- [ ] ¿Hay monitorización de consultas anómalas o scraping semántico?

## Agentes

- [ ] ¿El agente tiene objetivos estrechos y medibles?
- [ ] ¿Se limita el número de pasos, iteraciones y acciones encadenadas?
- [ ] ¿El agente puede explicar qué herramienta quiere usar antes de invocarla?
- [ ] ¿Hay límites de autonomía para acciones sensibles?
- [ ] ¿Se registra el razonamiento operativo relevante sin exponer secretos?
- [ ] ¿Existe kill switch o modo read-only?
- [ ] ¿Se prueba comportamiento con contexto no confiable?

## Herramientas externas

- [ ] ¿Existe inventario de tools, plugins, MCP servers o extensiones?
- [ ] ¿Cada tool tiene owner, descripción, permisos y riesgo?
- [ ] ¿Las tools evitan funciones genéricas como ejecutar comandos o URLs arbitrarias si no son necesarias?
- [ ] ¿Los inputs y outputs de tools tienen schema validation?
- [ ] ¿Las tools aplican autorización propia en backend?
- [ ] ¿Las credenciales de tools están fuera del prompt y gestionadas por secret manager?
- [ ] ¿Hay allowlists de acciones, dominios, tablas o rutas?

## APIs

- [ ] ¿Las APIs llamadas por el LLM aplican autenticación y autorización independientes?
- [ ] ¿Se usan scopes mínimos y tokens separados por función?
- [ ] ¿Hay protección contra abuso de tasa, tamaño y coste?
- [ ] ¿Las respuestas de APIs se filtran antes de entrar al contexto?
- [ ] ¿Los errores no revelan secretos ni detalles internos?
- [ ] ¿Se registran llamadas API iniciadas por agentes?

## Autorización

- [ ] ¿Las decisiones de acceso se toman fuera del LLM?
- [ ] ¿El agente actúa en contexto del usuario cuando corresponde?
- [ ] ¿Se evita usar cuentas genéricas privilegiadas?
- [ ] ¿Las acciones críticas requieren confirmación humana?
- [ ] ¿Se separan permisos de lectura, escritura, borrado y envío externo?
- [ ] ¿Hay revisiones periódicas de permisos concedidos a agentes y tools?

## Logging

- [ ] ¿Se registran prompts, respuestas, modelo, versión y configuración?
- [ ] ¿Se registran fuentes RAG recuperadas y tool calls?
- [ ] ¿Se protegen logs con datos sensibles mediante masking o tokenización?
- [ ] ¿Los logs permiten reconstruir una acción sin exponer secretos innecesarios?
- [ ] ¿Existe correlación por usuario, sesión, request ID y trace ID?
- [ ] ¿Se retienen logs el tiempo requerido por riesgo y normativa?

## Monitorización

- [ ] ¿Hay alertas por intentos de prompt injection?
- [ ] ¿Hay alertas por acceso anómalo a documentos sensibles?
- [ ] ¿Hay detección de tool calls inusuales, repetidos o fuera de horario?
- [ ] ¿Hay métricas de alucinación, groundedness y abstención?
- [ ] ¿Se revisan drift, cambios de comportamiento y degradación de calidad?
- [ ] ¿Existe playbook de respuesta para incidentes LLM?

## Rate limiting

- [ ] ¿Hay límites por usuario, IP, tenant, API key y organización?
- [ ] ¿Se limitan tokens de entrada y salida?
- [ ] ¿Se limitan pasos de agente, tool calls y tamaño de documentos?
- [ ] ¿Se controla coste por sesión y coste diario?
- [ ] ¿Hay throttling y colas con backpressure?
- [ ] ¿Existe protección frente a Denial of Wallet?

## Privacidad

- [ ] ¿Se minimizan datos personales enviados al modelo?
- [ ] ¿Se aplica masking, redaction o pseudonimización cuando procede?
- [ ] ¿Hay base legal y finalidad documentada para procesar PII?
- [ ] ¿Se informa al usuario sobre el uso de IA y límites de fiabilidad?
- [ ] ¿Se puede borrar o excluir información de índices y logs?
- [ ] ¿Se revisan transferencias internacionales y términos del proveedor?

## Gestión de datos sensibles

- [ ] ¿Los secretos nunca aparecen en prompts, documentos RAG o ejemplos?
- [ ] ¿Hay clasificación de datos antes de indexar?
- [ ] ¿Se detecta PII, credenciales, tokens, claves y datos regulados?
- [ ] ¿Se bloquea exfiltración por output, URL, tool call o renderizado?
- [ ] ¿Hay rotación de credenciales si se detecta filtración?
- [ ] ¿Existe proceso de data incident para LLMs?

## Evaluación continua

- [ ] ¿Hay dataset de evaluación con casos normales, adversarios y edge cases?
- [ ] ¿Se ejecutan evals antes de cambios de modelo, prompt, RAG o tools?
- [ ] ¿Se mide tasa de bloqueo, falsos positivos, groundedness y calidad?
- [ ] ¿Se guardan resultados históricos para detectar regresiones?
- [ ] ¿Se incluyen pruebas específicas por cada riesgo OWASP?
- [ ] ¿Hay criterios de salida para producción?

## Red teaming

- [ ] ¿Existe un plan de [[AI Red Teaming]] con alcance y reglas claras?
- [ ] ¿Se prueban inyección directa, indirecta, RAG poisoning y tool abuse?
- [ ] ¿Los ataques son conceptuales, controlados y sin impacto real no autorizado?
- [ ] ¿Se documenta evidencia, impacto y control fallido?
- [ ] ¿Se convierten hallazgos en casos de regresión?
- [ ] ¿Se retestean mitigaciones?

## Respuesta ante incidentes

- [ ] ¿Existe clasificación de incidentes LLM?
- [ ] ¿Hay responsables de seguridad, AI engineering, legal/GRC y negocio?
- [ ] ¿Se puede desactivar una tool o un índice RAG rápidamente?
- [ ] ¿Se puede revocar memoria, prompts persistentes o documentos contaminados?
- [ ] ¿Se puede rotar credenciales expuestas?
- [ ] ¿Hay comunicación interna y externa preparada?
- [ ] ¿Se hace post-mortem con mejoras de controles?

## Resultado esperado de auditoría

Una revisión profesional debe terminar con:

- Riesgos priorizados por impacto y probabilidad.
- Evidencia reproducible y segura.
- Controles existentes y controles fallidos.
- Recomendaciones técnicas concretas.
- Severidad residual tras mitigación.
- Casos de prueba para regresión.
- Plan de retest.

## Enlaces relacionados

- [[OWASP LLM Top 10]]
- [[AI Security Controls]]
- [[RAG Security]]
- [[LLM Agents]]
- [[Vector Databases]]
- [[AI Red Teaming]]
- [[Purple Team]]
