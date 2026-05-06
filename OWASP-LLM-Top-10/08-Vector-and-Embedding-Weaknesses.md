---
tags:
  - ai-security
  - owasp-llm
  - rag
  - vector-databases
aliases:
  - Vector and Embedding Weaknesses
  - LLM08 Vector and Embedding Weaknesses
  - LLM08:2025 Vector and Embedding Weaknesses
---

# LLM08:2025 Vector and Embedding Weaknesses

## Resumen ejecutivo

**Vector and Embedding Weaknesses** cubre fallos en cómo se generan, almacenan, consultan y protegen embeddings y bases vectoriales. Es especialmente relevante para [[RAG Security]], donde el modelo recibe conocimiento externo recuperado por similitud semántica.

El riesgo principal es que la base vectorial recupere contenido incorrecto, no autorizado, contaminado o sensible, y el LLM lo convierta en una respuesta confiable.

## Descripción técnica

Componentes afectados:

- Pipeline de ingesta y chunking.
- Modelo de embeddings.
- Metadatos y clasificación.
- [[Vector Databases]].
- Retriever, ranker y reranker.
- Filtros de autorización.
- Context builder.
- Logs de retrieval.

Riesgos típicos:

- Acceso no autorizado a embeddings o documentos.
- Mezcla de tenants o permisos.
- Recuperación de documentos sensibles por similitud.
- Embedding inversion o inferencia parcial de contenido.
- Poisoning de documentos indexados.
- Conflictos entre fuentes.
- Metadatos pobres que impiden aplicar controles.
- Reindexado incompleto tras cambios de permisos.

Diferencias frente a vulnerabilidades clásicas:

- En una base relacional, las consultas suelen ser exactas y autorizadas por filas/tablas.
- En vector search, el resultado depende de similitud, top-k, filtros, chunks y embeddings.
- Un documento no autorizado puede aparecer por relevancia semántica si los filtros no se aplican antes del contexto.

Por qué puede pasar desapercibido:

- El sistema "funciona" y responde con confianza.
- Las fugas pueden ocurrir como resumen, no como documento exacto.
- Los permisos se pierden durante chunking o reindexado.
- Los embeddings parecen datos derivados, pero pueden seguir siendo sensibles.

## Escenario realista

Una empresa crea un asistente RAG para documentación interna. La vector DB contiene documentos de RRHH, ingeniería y ventas. Los chunks incluyen metadatos de origen, pero no clasificación ni permisos. Un usuario de ventas pregunta por políticas internas y el sistema recupera fragmentos de RRHH con datos sensibles porque son semánticamente relevantes.

El fallo está en que la base vectorial se diseñó para relevancia, no para seguridad.

## Ejemplo de ataque

Ejemplo conceptual y seguro:

1. El atacante formula consultas amplias para explorar temas indexados.
2. Observa qué tipos de documentos aparecen citados o resumidos.
3. Ajusta preguntas para acercarse semánticamente a información sensible.
4. El sistema devuelve fragmentos no autorizados o información inferida.

Qué intenta conseguir el atacante:

- Enumerar conocimiento interno.
- Recuperar datos de otro tenant.
- Inferir contenido sensible.
- Activar documentos contaminados.
- Manipular respuestas mediante RAG poisoning.

Controles que fallan:

- Vector DB sin permission-aware retrieval.
- Metadatos insuficientes.
- Filtros aplicados después del retrieval o solo en UI.
- Ausencia de clasificación.
- Logs de retrieval incompletos.

Impacto:

- Fuga de documentos.
- Respuestas manipuladas.
- Incumplimiento de privacidad.
- Pérdida de confianza en RAG.

## Impacto

| Dimensión | Impacto |
|---|---|
| Confidencialidad | Recuperación de documentos, chunks o embeddings no autorizados. |
| Integridad | RAG poisoning o fuentes conflictivas alteran respuestas. |
| Disponibilidad | Reindexados costosos o retirada de bases contaminadas. |
| Privacidad | Exposición de PII en embeddings, chunks o metadatos. |
| Cumplimiento | Fallo de segregación por tenant, rol o finalidad. |
| Reputación | Chatbot corporativo revela conocimiento interno. |
| Coste operativo | Rechunking, reindexado, rediseño de permisos y revisión documental. |

## Indicadores de riesgo

- La vector DB no aplica RBAC/ABAC.
- No hay metadatos de sensibilidad.
- Todos los usuarios consultan el mismo índice.
- Los filtros se aplican después de construir el prompt.
- El top-k es alto sin justificación.
- No hay logs de documentos recuperados.
- Se indexan documentos sin revisión.
- No existe estrategia de borrado o reindexado.

## Controles defensivos

- **Diseño seguro**: permission-aware RAG desde la ingesta hasta el contexto.
- **Validación de entradas**: detectar queries de enumeración o scraping semántico.
- **Validación de salidas**: comprobar que las fuentes citadas están autorizadas.
- **Least privilege**: índices separados o filtros obligatorios por usuario, tenant y clasificación.
- **Human-in-the-loop**: revisión para indexar fuentes críticas o sensibles.
- **Logging y monitorización**: registrar query, filtros, top-k, documentos, scores y usuario.
- **Rate limiting**: limitar consultas exploratorias, scraping semántico y top-k abusivo.
- **Evaluaciones automáticas**: pruebas cross-tenant, permisos, retrieval adversario y data leakage.
- **Red teaming**: simular enumeración semántica y documentos contaminados.
- **Controles específicos**: metadatos ricos, pre-filtering, chunking seguro, reindexado tras cambios de permisos, cifrado.

## Checklist de auditoría

- [ ] ¿Cada chunk conserva permisos y clasificación del documento original?
- [ ] ¿El retrieval filtra antes de construir contexto?
- [ ] ¿Se separan tenants o dominios sensibles?
- [ ] ¿Se evita indexar secretos o PII innecesaria?
- [ ] ¿Hay logs de documentos recuperados?
- [ ] ¿Se monitorizan consultas de enumeración?
- [ ] ¿Hay proceso de borrado y reindexado?
- [ ] ¿Se validan documentos antes de indexar?
- [ ] ¿Se evalúa embedding leakage o inferencia?
- [ ] ¿Las citas mostradas están autorizadas para el usuario?
- [ ] ¿Se prueba RAG poisoning?

## Preguntas de entrevista

- ¿Por qué una vector database puede romper controles de acceso?
- ¿Qué es permission-aware retrieval?
- ¿Qué metadatos de seguridad debe tener un chunk?
- ¿Qué diferencia hay entre data poisoning y vector/embedding weaknesses?
- ¿Cómo probarías fuga cross-tenant en RAG?
- ¿Por qué los embeddings no deben tratarse automáticamente como datos no sensibles?

## Relación con Purple Team

**Red Team**:

- Simula consultas de enumeración con datos sintéticos.
- Prueba recuperación cross-tenant.
- Inserta documentos controlados para validar RAG poisoning.

**Blue Team**:

- Monitoriza top-k, documentos sensibles recuperados y queries anómalas.
- Genera alertas por acceso cross-scope.
- Revisa cambios de índice y reindexado.

**Purple Team**:

- Ajusta filtros y metadatos.
- Valida que el sistema no construye contexto con datos no autorizados.
- Retestea permisos tras reindexado.

## Mapeo con controles

- **OWASP ASVS**: control de acceso, protección de datos, logging, validación y seguridad de APIs.
- **NIST AI RMF**: MAP para fuentes y actores; MEASURE para retrieval quality, leakage y groundedness; MANAGE para controles de acceso y respuesta; GOVERN para clasificación de datos.
- **MITRE ATLAS**: `AML.T0070` RAG Poisoning; técnicas de Discovery/Collection para conocimiento RAG cuando aplique.
- **Principios generales**: least privilege, data minimization, tenant isolation, defense in depth, zero trust data access.

## Notas para Obsidian

Enlaces:

- [[OWASP LLM Top 10]]
- [[Vector and Embedding Weaknesses]]
- [[RAG Security]]
- [[Vector Databases]]
- [[Prompt Injection]]
- [[AI Red Teaming]]
- [[AI Security Controls]]

## Resumen final

- Idea clave: RAG seguro requiere autorización y trazabilidad en retrieval, no solo mejores embeddings.
- Riesgo principal: recuperar datos no autorizados o contaminados.
- Defensa más importante: permission-aware retrieval, metadatos de seguridad y logging de fuentes.
- Para entrevista: la vector DB es una base de datos de seguridad crítica, no solo un índice semántico.

## Fuentes base

- [OWASP LLM08:2025 Vector and Embedding Weaknesses](https://genai.owasp.org/llmrisk/llm082025-vector-and-embedding-weaknesses/)
- [MITRE ATLAS data](https://github.com/mitre-atlas/atlas-data)
