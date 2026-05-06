---
tags:
  - ai-security
  - owasp-llm
  - data-protection
aliases:
  - Sensitive Information Disclosure
  - LLM02 Sensitive Information Disclosure
  - LLM02:2025 Sensitive Information Disclosure
---

# LLM02:2025 Sensitive Information Disclosure

## Resumen ejecutivo

**Sensitive Information Disclosure** ocurre cuando una aplicación LLM revela información que no debería exponer: PII, datos financieros, datos de salud, secretos, credenciales, código propietario, documentos internos, prompts, logs o resultados de herramientas.

El riesgo no se limita a la respuesta final. La filtración puede ocurrir en prompts, RAG, embeddings, logs, proveedores externos, trazas de agentes, errores, telemetría o interfaces de administración.

## Descripción técnica

En aplicaciones LLM, la información sensible puede entrar al sistema por:

- Prompts de usuarios.
- Documentos indexados en RAG.
- Logs históricos.
- Bases de datos internas.
- Herramientas conectadas a correo, CRM, repositorios o APIs.
- System prompts mal diseñados.
- Datasets de fine-tuning.
- Memoria conversacional.

Cómo aparece:

- El LLM responde con datos de otro usuario.
- El RAG recupera documentos sin aplicar permisos.
- El sistema guarda prompts con PII en logs sin protección.
- Un agente consulta una API con permisos excesivos.
- Un proveedor usa datos enviados para entrenamiento sin control contractual.
- Los embeddings permiten inferir parte del texto original.

Diferencias frente a vulnerabilidades clásicas:

- En una app clásica, el leakage suele venir de control de acceso roto, errores o endpoints expuestos.
- En LLMs, también puede venir de una respuesta generada que combina fragmentos, contexto y memoria.
- El leakage puede ser parcial, probabilístico o indirecto.

Por qué puede pasar desapercibido:

- La respuesta puede parecer útil y legítima.
- Los logs pueden contener datos sensibles sin alertas.
- La fuga puede requerir varias interacciones.
- La información puede aparecer como resumen, no como copia literal.
- El equipo puede revisar solo el modelo y no el pipeline completo.

## Escenario realista

Un asistente de soporte corporativo usa RAG sobre tickets internos. Un usuario de un equipo pregunta por "incidencias similares" y el sistema devuelve fragmentos de tickets de otro departamento que contienen nombres, emails, identificadores de cliente y detalles contractuales.

El problema no está en el modelo, sino en que la base vectorial no aplica filtros por rol y el contexto recuperado se entrega completo al LLM.

## Ejemplo de ataque

Ejemplo conceptual y seguro:

1. El atacante formula preguntas amplias sobre incidencias, clientes o configuraciones.
2. El retriever recupera documentos de varios permisos porque solo optimiza similitud semántica.
3. El modelo resume la información sin conocer la autorización real del usuario.
4. La respuesta revela datos que el usuario no debería ver.

Qué intenta conseguir el atacante:

- Obtener PII o datos internos.
- Inferir nombres de clientes, proyectos o proveedores.
- Descubrir credenciales mal incluidas en documentos.
- Mapear sistemas internos.

Controles que fallan:

- Falta de clasificación de datos.
- RAG sin permission-aware retrieval.
- Prompts o logs con secretos.
- Ausencia de DLP/redaction.
- Tools con permisos genéricos.

Impacto:

- Fuga de datos regulados.
- Incidente de privacidad.
- Notificación legal.
- Pérdida de confianza de clientes.
- Rotación urgente de secretos.

## Impacto

| Dimensión | Impacto |
|---|---|
| Confidencialidad | Exposición de PII, secretos, documentos internos o propiedad intelectual. |
| Integridad | Uso de datos sensibles fuera de contexto para decisiones incorrectas. |
| Disponibilidad | Desactivación temporal del sistema durante investigación. |
| Privacidad | Violación de principios de minimización, finalidad y acceso autorizado. |
| Cumplimiento | Riesgo frente a GDPR, contratos, normativa sectorial o políticas internas. |
| Reputación | Pérdida de confianza en productos AI de la organización. |
| Coste operativo | Forense, notificaciones, rotación de credenciales, reindexado y revisión legal. |

## Indicadores de riesgo

- El sistema no clasifica documentos por sensibilidad.
- El RAG no filtra por usuario, rol o tenant.
- Los prompts contienen secretos o credenciales.
- Los logs guardan prompts completos con PII.
- La memoria conversacional no tiene límites ni borrado.
- No hay redaction antes de enviar datos al proveedor.
- No hay revisión contractual del proveedor LLM.
- Se usan cuentas de servicio con acceso amplio a datos.

## Controles defensivos

- **Diseño seguro**: data flow diagram con clasificación de datos en cada etapa.
- **Validación de entradas**: detectar PII, secretos, claves, tokens y datos regulados.
- **Validación de salidas**: DLP, redaction y bloqueo de respuestas con datos no autorizados.
- **Least privilege**: RAG y tools deben operar con permisos del usuario o scopes mínimos.
- **Human-in-the-loop**: revisión para exportaciones, informes o respuestas con datos sensibles.
- **Logging seguro**: masking, tokenización, cifrado y retención mínima.
- **Monitorización**: alertas por consultas masivas, términos sensibles, retrieval anómalo y accesos cross-tenant.
- **Evaluaciones automáticas**: tests de leakage, membership inference conceptual y recuperación no autorizada.
- **Controles RAG**: metadatos de sensibilidad, ABAC/RBAC, filtros previos al retrieval y post-filtering.
- **Gobierno de datos**: revisión de términos del proveedor, opt-out de entrenamiento y data processing agreement.

## Checklist de auditoría

- [ ] ¿Existe inventario de datos enviados al modelo?
- [ ] ¿Se clasifican documentos antes de indexar?
- [ ] ¿El retrieval aplica permisos del usuario?
- [ ] ¿Los embeddings excluyen secretos y PII innecesaria?
- [ ] ¿Los prompts de sistema están libres de credenciales?
- [ ] ¿Los logs aplican masking o minimización?
- [ ] ¿Hay redaction antes de enviar datos a proveedores externos?
- [ ] ¿El proveedor permite excluir datos de entrenamiento?
- [ ] ¿Las tools usan scopes mínimos?
- [ ] ¿Hay alertas por exposición de datos sensibles?
- [ ] ¿Existe procedimiento de borrado o reindexado?

## Preguntas de entrevista

- ¿Dónde puede filtrarse información sensible en una aplicación LLM?
- ¿Por qué RAG puede romper controles de acceso si solo usa similitud semántica?
- ¿Qué diferencia hay entre filtrar salida y aplicar autorización antes del retrieval?
- ¿Por qué no debes poner credenciales en el system prompt?
- ¿Qué logs necesitas y cómo los protegerías?
- ¿Cómo evaluarías leakage en un chatbot corporativo?

## Relación con Purple Team

**Red Team**:

- Simula consultas para recuperar documentos de otros roles usando datos sintéticos.
- Prueba si el sistema resume secretos ficticios colocados en documentos controlados.
- Evalúa leakage por memoria o logs visibles.

**Blue Team**:

- Crea alertas por recuperación de documentos sensibles.
- Monitoriza respuestas con PII o tokens.
- Correlaciona usuario, rol, fuente recuperada y salida.

**Purple Team**:

- Convierte leakage en casos de regresión.
- Valida que filtros de permisos se apliquen antes del contexto del modelo.
- Mide falsos positivos de DLP y eficacia de redaction.

## Mapeo con controles

- **OWASP ASVS**: protección de datos, control de acceso, gestión de secretos, logging seguro, seguridad de APIs y validación de salidas.
- **NIST AI RMF**: GOVERN para políticas de datos y proveedores; MAP para flujos y actores; MEASURE para leakage testing; MANAGE para respuesta, minimización y monitorización.
- **MITRE ATLAS**: técnicas de Collection/Exfiltration y extracción de información del entorno AI cuando aplique; `AML.T0057` aparece en mitigaciones de ATLAS relacionadas con privacidad y revelación de información sensible.
- **Principios generales**: minimización, need-to-know, least privilege, privacy by design, defense in depth.

## Notas para Obsidian

Enlaces:

- [[OWASP LLM Top 10]]
- [[Sensitive Information Disclosure]]
- [[RAG Security]]
- [[AI Red Teaming]]
- [[LLM Agents]]
- [[Vector Databases]]
- [[AI Security Controls]]

## Resumen final

- Idea clave: el leakage puede ocurrir en cualquier punto del pipeline LLM.
- Riesgo principal: revelar datos internos o regulados a usuarios no autorizados.
- Defensa más importante: minimización, permission-aware retrieval, redaction y logging seguro.
- Para entrevista: la protección real se diseña en arquitectura y datos, no solo en el prompt.

## Fuentes base

- [OWASP LLM02:2025 Sensitive Information Disclosure](https://genai.owasp.org/llmrisk/llm022025-sensitive-information-disclosure/)
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
