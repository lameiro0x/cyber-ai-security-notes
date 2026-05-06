---
tags:
  - ai-security
  - owasp-llm
  - data-poisoning
aliases:
  - Data and Model Poisoning
  - LLM04 Data and Model Poisoning
  - LLM04:2025 Data and Model Poisoning
---

# LLM04:2025 Data and Model Poisoning

## Resumen ejecutivo

**Data and Model Poisoning** ocurre cuando un atacante o proceso inseguro manipula datos de entrenamiento, fine-tuning, evaluación, embeddings o pesos del modelo para alterar su comportamiento. Es principalmente un ataque contra la integridad del sistema AI.

Puede introducir sesgos, backdoors, respuestas falsas, degradación de rendimiento o conductas que solo aparecen bajo ciertas condiciones.

## Descripción técnica

Superficies donde puede aparecer:

- Datos de pre-training.
- Datasets de fine-tuning.
- Datos de evaluación.
- Feedback humano o automático.
- Documentos incorporados a RAG.
- Embeddings y chunks.
- Pesos de modelo o adapters.
- Memoria persistente de agentes.

Formas comunes:

- Inserción de ejemplos sesgados o falsos.
- Manipulación de etiquetas.
- Documentos falsos en una base de conocimiento.
- Fine-tuning con datos de baja calidad o maliciosos.
- Backdoors activados por triggers específicos.
- Modificación directa de pesos o adapters.

Diferencias frente a vulnerabilidades clásicas:

- No siempre hay explotación en runtime.
- El daño puede introducirse antes del despliegue.
- El comportamiento malicioso puede parecer una respuesta normal.
- Puede no existir stack trace, error o IOC tradicional.

Por qué puede pasar desapercibido:

- Las pruebas promedio pueden seguir pasando.
- El trigger puede ser raro.
- El equipo puede medir accuracy general, no seguridad.
- No siempre hay trazabilidad de datos.
- Los datasets externos se asumen confiables.

## Escenario realista

Una empresa fine-tunea un asistente legal interno con documentos procedentes de varias unidades. Un repositorio compartido incluye documentos obsoletos y algunos modificados por un insider. El modelo aprende criterios incorrectos y empieza a recomendar interpretaciones contractuales no válidas en casos específicos.

No hay malware ni intrusión visible. El fallo está en la integridad y gobernanza del dataset.

## Ejemplo de ataque

Ejemplo conceptual y seguro:

1. El atacante identifica una fuente que alimenta entrenamiento, fine-tuning o RAG.
2. Introduce documentos aparentemente legítimos con información falsa o sesgada.
3. El pipeline ingiere los datos sin validación de procedencia ni revisión.
4. El modelo o el sistema RAG devuelve respuestas manipuladas en consultas futuras.

Qué intenta conseguir el atacante:

- Cambiar decisiones de negocio.
- Degradar confianza en el sistema.
- Introducir respuestas favorables al atacante.
- Activar comportamiento bajo condiciones concretas.

Controles que fallan:

- Falta de data lineage.
- Sin revisión de fuentes.
- Sin versionado de datasets.
- Ausencia de evaluación adversaria.
- No hay monitorización de drift o anomalías.

Impacto:

- Decisiones incorrectas.
- Riesgo legal o financiero.
- Sesgo sistemático.
- Pérdida de integridad del modelo.

## Impacto

| Dimensión | Impacto |
|---|---|
| Confidencialidad | Poisoning puede inducir al modelo a revelar datos en condiciones concretas. |
| Integridad | Respuestas, recomendaciones o clasificaciones manipuladas. |
| Disponibilidad | Degradación de calidad que obliga a retirar el sistema. |
| Privacidad | Inclusión indebida de datos personales en entrenamiento o memoria. |
| Cumplimiento | Uso de datos no autorizados o resultados discriminatorios. |
| Reputación | Pérdida de confianza en el sistema AI y en sus decisiones. |
| Coste operativo | Reentrenamiento, reindexado, revisión de datasets y auditoría. |

## Indicadores de riesgo

- No hay data lineage ni control de versiones.
- Se ingieren fuentes externas sin validación.
- No existe separación entre datos aprobados y experimentales.
- Los datasets de evaluación no cubren abuso.
- No se revisan outliers ni cambios de distribución.
- Se aceptan feedback loops sin control.
- No hay ownership claro de datasets.
- No se monitoriza comportamiento post-deploy.

## Controles defensivos

- **Diseño seguro**: gobernanza del ciclo de vida de datos y modelos.
- **Validación de entradas**: validar origen, formato, integridad y clasificación de datos.
- **Validación de salidas**: comparar contra fuentes confiables y métricas de seguridad.
- **Least privilege**: restringir quién puede modificar datasets, índices y modelos.
- **Human-in-the-loop**: revisión de datos críticos antes de entrenamiento o indexado.
- **Logging y monitorización**: trazabilidad de cambios, jobs, versiones y resultados.
- **Rate limiting**: limitar feedback automatizado y escrituras masivas en memoria o conocimiento.
- **Evaluaciones automáticas**: robustness, backdoor checks, drift, bias, groundedness y regresión.
- **Red teaming**: probar triggers conceptuales y manipulación de fuentes.
- **RAG específico**: cuarentena de documentos nuevos, reindexado controlado y revisión de cambios.

## Checklist de auditoría

- [ ] ¿Existe inventario de datasets y fuentes?
- [ ] ¿Cada dataset tiene owner, versión, origen y licencia?
- [ ] ¿Se validan datos antes de entrenamiento o indexado?
- [ ] ¿Hay separación entre datos confiables y no confiables?
- [ ] ¿Se usan hashes o firmas para datasets/modelos?
- [ ] ¿Hay revisión humana para fuentes críticas?
- [ ] ¿Se monitoriza drift de calidad y seguridad?
- [ ] ¿Se prueban backdoors o triggers conceptuales?
- [ ] ¿Puede revertirse un dataset o índice a una versión anterior?
- [ ] ¿Se registra quién modificó datos, embeddings o modelos?
- [ ] ¿Las evaluaciones incluyen casos adversarios?

## Preguntas de entrevista

- ¿Qué diferencia hay entre data poisoning y prompt injection?
- ¿Por qué el poisoning puede ser difícil de detectar?
- ¿Qué controles pondrías en un pipeline de fine-tuning?
- ¿Cómo protegerías una base RAG frente a documentos manipulados?
- ¿Por qué el versionado de datasets es un control de seguridad?
- ¿Cómo probarías si un modelo tiene comportamiento backdoor?

## Relación con Purple Team

**Red Team**:

- Usa datasets sintéticos para simular datos falsos o conflictivos.
- Prueba si el pipeline ingiere documentos sin validación.
- Diseña consultas que activen comportamiento alterado.

**Blue Team**:

- Monitoriza cambios en datasets, índices y métricas.
- Crea alertas por fuentes nuevas no aprobadas.
- Revisa anomalías en respuestas y retrieval.

**Purple Team**:

- Valida controles de data lineage.
- Convierte incidentes de poisoning en pruebas de regresión.
- Mide cuánto tarda el equipo en detectar y revertir datos contaminados.

## Mapeo con controles

- **OWASP ASVS**: control de acceso, integridad de datos, logging, configuración segura y validación.
- **NIST AI RMF**: MAP para fuentes y dependencias; MEASURE para robustez y drift; MANAGE para remediación; GOVERN para ownership y políticas de datos.
- **MITRE ATLAS**: `AML.T0020` Poison Training Data; `AML.T0018` Backdoor AI Model; `AML.T0070` RAG Poisoning cuando el objetivo es contaminar conocimiento recuperado.
- **Principios generales**: integridad, provenance, least privilege, secure MLOps, defense in depth.

## Notas para Obsidian

Enlaces:

- [[OWASP LLM Top 10]]
- [[Data Poisoning]]
- [[RAG Security]]
- [[AI Red Teaming]]
- [[LLM Agents]]
- [[Vector Databases]]
- [[AI Security Controls]]

## Resumen final

- Idea clave: si los datos o modelos son manipulados, las respuestas también lo serán.
- Riesgo principal: pérdida silenciosa de integridad en decisiones AI.
- Defensa más importante: data lineage, validación, versionado, evaluación y capacidad de rollback.
- Para entrevista: poisoning es un riesgo de ciclo de vida, no solo de runtime.

## Fuentes base

- [OWASP LLM04:2025 Data and Model Poisoning](https://genai.owasp.org/llmrisk/llm042025-data-and-model-poisoning/)
- [MITRE ATLAS data](https://github.com/mitre-atlas/atlas-data)
