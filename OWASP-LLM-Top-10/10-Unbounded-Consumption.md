---
tags:
  - ai-security
  - owasp-llm
  - availability
aliases:
  - Unbounded Consumption
  - LLM10 Unbounded Consumption
  - LLM10:2025 Unbounded Consumption
---

# LLM10:2025 Unbounded Consumption

## Resumen ejecutivo

**Unbounded Consumption** ocurre cuando una aplicación LLM permite uso excesivo o no controlado de inferencia, tokens, tool calls, colas, coste, cómputo o APIs. Puede causar denegación de servicio, degradación, costes inesperados, abuso de cuota o extracción funcional del modelo.

En entornos cloud, este riesgo incluye **Denial of Wallet**: consumir recursos hasta generar costes significativos.

## Descripción técnica

Superficies típicas:

- Endpoints de chat o completion.
- APIs públicas o internas.
- Agentes con loops o múltiples pasos.
- RAG con documentos enormes o top-k alto.
- Tools costosas.
- Colas de tareas.
- Streaming sin timeout.
- Exposición de información como logits/logprobs.

Cómo aparece:

- Prompts muy largos o repetidos.
- Solicitudes que fuerzan contexto máximo.
- Agentes que iteran sin límite.
- Usuarios que automatizan consultas masivas.
- Queries diseñadas para ser caras.
- Extracción de comportamiento del modelo mediante muchas consultas.

Diferencias frente a vulnerabilidades clásicas:

- Se parece a DoS y abuso de API, pero con coste variable por tokens, modelo, tool calls y contexto.
- El coste económico puede ser impacto principal aunque el servicio siga disponible.
- El abuso puede buscar clonar comportamiento del modelo, no solo tirarlo.

Por qué puede pasar desapercibido:

- Las llamadas parecen legítimas.
- El coste se ve más tarde en facturación.
- El sistema puede degradar calidad antes de caer.
- Los límites tradicionales por request no consideran tokens o pasos de agente.

## Escenario realista

Una startup expone un endpoint de asistente con una cuota generosa por usuario. No limita tokens de salida, número de conversaciones ni pasos de agente. Un actor automatiza miles de preguntas largas y activa rutas RAG costosas. El servicio sigue respondiendo, pero la factura cloud y del proveedor LLM se dispara.

El incidente es de disponibilidad y coste, aunque no haya intrusión clásica.

## Ejemplo de ataque

Ejemplo conceptual y seguro:

1. El atacante identifica un endpoint LLM con límites débiles.
2. Envía muchas solicitudes largas o costosas.
3. El sistema procesa contexto amplio, retrieval y tool calls.
4. No hay cuotas, timeouts ni backpressure efectivos.
5. El servicio se degrada o genera coste excesivo.

Qué intenta conseguir el atacante:

- Denegar servicio a usuarios legítimos.
- Generar coste económico.
- Extraer comportamiento del modelo.
- Agotar cuotas de proveedor.
- Degradar reputación del servicio.

Controles que fallan:

- Sin rate limiting por usuario/API key/tenant.
- Sin límites de tokens.
- Sin timeouts.
- Sin cuotas de coste.
- Sin detección de anomalías.
- Sin límites de pasos de agente.

Impacto:

- Denial of service.
- Denial of wallet.
- Degradación de experiencia.
- Posible extracción funcional.

## Impacto

| Dimensión | Impacto |
|---|---|
| Confidencialidad | Extracción de comportamiento o inferencia del modelo mediante consultas masivas. |
| Integridad | Respuestas degradadas por presión de recursos o fallback inseguro. |
| Disponibilidad | Saturación de API, colas, workers, vector DB o proveedor. |
| Privacidad | Logging masivo de prompts puede acumular datos sensibles. |
| Cumplimiento | Falta de controles de uso y retención bajo abuso. |
| Reputación | Caídas, latencia alta o facturación abusiva visible. |
| Coste operativo | Costes LLM/cloud, mitigación urgente y rediseño de cuotas. |

## Indicadores de riesgo

- No hay límites por tokens de entrada/salida.
- No hay cuotas por usuario, tenant o API key.
- Agentes sin máximo de pasos.
- RAG permite documentos enormes.
- No hay timeout por request.
- No se monitoriza coste por sesión.
- Se exponen detalles innecesarios del modelo.
- No hay alertas de facturación o consumo.
- Las colas crecen sin backpressure.

## Controles defensivos

- **Diseño seguro**: presupuestos de coste y límites por flujo desde el diseño.
- **Validación de entradas**: tamaño máximo, formatos permitidos y rechazo de prompts excesivos.
- **Validación de salidas**: límite de tokens, streaming controlado y corte seguro.
- **Least privilege**: acceso a modelos caros solo cuando esté justificado.
- **Human-in-the-loop**: aprobación para tareas batch o análisis masivos.
- **Logging y monitorización**: coste, tokens, latencia, errores, top users, tool calls y colas.
- **Rate limiting**: por usuario, IP, API key, tenant, modelo, tool y endpoint.
- **Evaluaciones automáticas**: stress tests, abuso de tokens, loops de agente y coste.
- **Red teaming**: simulaciones de Denial of Wallet y model extraction conceptual.
- **Controles específicos**: quotas, timeouts, throttling, graceful degradation, caching, backpressure, circuit breakers y alertas de facturación.

## Checklist de auditoría

- [ ] ¿Hay rate limiting por usuario, IP, tenant y API key?
- [ ] ¿Hay límites de tokens de entrada y salida?
- [ ] ¿Hay límites de pasos de agente y tool calls?
- [ ] ¿Existen timeouts por request y por tool?
- [ ] ¿Se monitoriza coste por sesión y por tenant?
- [ ] ¿Hay alertas de consumo anómalo?
- [ ] ¿Existe presupuesto diario/mensual y corte automático?
- [ ] ¿El sistema degrada de forma controlada?
- [ ] ¿Se limitan documentos y top-k en RAG?
- [ ] ¿Se protege frente a extracción masiva por API?
- [ ] ¿Hay pruebas de carga específicas para LLM?

## Preguntas de entrevista

- ¿Qué es Denial of Wallet?
- ¿Por qué rate limiting clásico puede ser insuficiente en LLMs?
- ¿Qué límites pondrías en un agente?
- ¿Cómo monitorizarías coste y abuso?
- ¿Qué relación hay entre unbounded consumption y model extraction?
- ¿Cómo diseñarías graceful degradation para un asistente LLM?

## Relación con Purple Team

**Red Team**:

- Simula consumo excesivo con límites autorizados.
- Prueba prompts largos, loops controlados y rutas RAG costosas.
- Evalúa si se puede automatizar scraping del comportamiento.

**Blue Team**:

- Monitoriza tokens, coste, latencia, errores y colas.
- Crea alertas por spikes y usuarios atípicos.
- Activa throttling o bloqueo temporal.

**Purple Team**:

- Ajusta cuotas y umbrales.
- Valida alertas de Denial of Wallet.
- Mide tiempo de detección y coste máximo antes del bloqueo.

## Mapeo con controles

- **OWASP ASVS**: protección contra abuso de recursos, seguridad de APIs, logging, manejo de errores y configuración.
- **NIST AI RMF**: MAP para identificar recursos críticos; MEASURE para carga y coste; MANAGE para cuotas, respuesta y degradación; GOVERN para políticas de uso aceptable.
- **MITRE ATLAS**: `AML.T0029` Denial of AI Service; técnicas de model extraction y exfiltration cuando el abuso busca replicar comportamiento.
- **Principios generales**: availability by design, fail safe, rate limiting, least privilege, cost governance.

## Notas para Obsidian

Enlaces:

- [[OWASP LLM Top 10]]
- [[Unbounded Consumption]]
- [[Denial of Wallet]]
- [[LLM Agents]]
- [[RAG Security]]
- [[AI Red Teaming]]
- [[AI Security Controls]]

## Resumen final

- Idea clave: en LLMs, disponibilidad incluye coste, tokens, herramientas y colas.
- Riesgo principal: consumo excesivo que degrada servicio o dispara costes.
- Defensa más importante: cuotas, límites de tokens, timeouts, monitorización y graceful degradation.
- Para entrevista: Denial of Wallet es DoS adaptado al modelo económico de inferencia.

## Fuentes base

- [OWASP LLM10:2025 Unbounded Consumption](https://genai.owasp.org/llmrisk/llm102025-unbounded-consumption/)
- [MITRE ATLAS data](https://github.com/mitre-atlas/atlas-data)
