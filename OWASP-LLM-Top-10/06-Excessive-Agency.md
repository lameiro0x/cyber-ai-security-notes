---
tags:
  - ai-security
  - owasp-llm
  - agents
aliases:
  - Excessive Agency
  - LLM06 Excessive Agency
  - LLM06:2025 Excessive Agency
---

# LLM06:2025 Excessive Agency

## Resumen ejecutivo

**Excessive Agency** ocurre cuando un sistema LLM tiene más funciones, permisos o autonomía de los necesarios. El riesgo aparece especialmente en [[LLM Agents]] que pueden llamar herramientas, consultar APIs, escribir datos, enviar mensajes o ejecutar workflows.

La idea central: un modelo que puede equivocarse, alucinar o ser manipulado no debe tener capacidad ilimitada para actuar.

## Descripción técnica

OWASP resume la causa raíz en tres excesos:

- **Excessive functionality**: herramientas o funciones innecesarias disponibles para el agente.
- **Excessive permissions**: permisos más amplios de lo que exige la tarea.
- **Excessive autonomy**: acciones críticas sin confirmación humana ni verificación externa.

Cómo aparece:

- Tool genérica para hacer peticiones HTTP arbitrarias.
- Plugin de email que puede leer y enviar cuando solo debería leer.
- Cuenta de servicio con permisos globales.
- Agente que decide por sí mismo borrar, comprar, publicar o modificar.
- Multi-agent workflows donde un agente hereda contexto o permisos de otro.

Componentes afectados:

- Orquestador de agentes.
- Tools, plugins y MCP servers.
- APIs internas.
- OAuth scopes y cuentas de servicio.
- Sistemas downstream: correo, repositorios, ticketing, CRM, cloud.

Diferencias frente a vulnerabilidades clásicas:

- Se parece al confused deputy problem y a permisos excesivos.
- La diferencia es que la decisión de usar una función puede venir de una salida probabilística o manipulada por prompt injection.
- El impacto depende de lo que el agente pueda hacer, no solo de lo que pueda decir.

Por qué puede pasar desapercibido:

- En demos, dar más tools acelera desarrollo.
- Las acciones correctas en casos normales ocultan permisos excesivos.
- Los equipos revisan el prompt, pero no los scopes reales.
- Las herramientas de terceros traen funciones no usadas.

## Escenario realista

Un asistente ejecutivo puede leer emails y crear borradores. Para simplificar, se integra con una API de correo que también permite enviar mensajes y borrar correos. Un email entrante contiene instrucciones no confiables. El agente lo resume, interpreta la instrucción como una tarea y prepara una acción fuera del objetivo original.

Con permisos read-only, el impacto sería bajo. Con permisos de envío, el riesgo se convierte en acción real.

## Ejemplo de ataque

Ejemplo conceptual y seguro:

1. El atacante introduce instrucciones en un canal que el agente procesa.
2. El agente interpreta el contenido como una orden.
3. Selecciona una tool disponible aunque no sea necesaria para la tarea.
4. La tool ejecuta con permisos amplios.
5. No hay revisión humana ni autorización backend que bloquee la acción.

Qué intenta conseguir el atacante:

- Enviar información a un tercero.
- Modificar tickets o documentos.
- Ejecutar acciones con identidad de servicio.
- Escalar impacto de prompt injection.

Controles que fallan:

- Tools demasiado genéricas.
- Permisos amplios.
- Sin complete mediation en backend.
- Falta de confirmación humana.
- No hay límites de pasos ni acciones.

Impacto:

- Acciones no autorizadas.
- Exfiltración.
- Modificación o destrucción de datos.
- Fraude operativo.

## Impacto

| Dimensión | Impacto |
|---|---|
| Confidencialidad | El agente consulta o envía datos a destinos no autorizados. |
| Integridad | Modifica documentos, tickets, registros o configuraciones. |
| Disponibilidad | Ejecuta operaciones repetidas, borra datos o consume servicios. |
| Privacidad | Accede a datos personales fuera del contexto del usuario. |
| Cumplimiento | Acciones sin aprobación, trazabilidad o segregación de funciones. |
| Reputación | El sistema AI actúa de forma visible e indebida. |
| Coste operativo | Contención, rollback, revisión de permisos y rediseño de workflows. |

## Indicadores de riesgo

- Tools con funciones no necesarias.
- APIs conectadas con permisos de escritura amplios.
- Cuentas genéricas privilegiadas.
- Acciones críticas sin confirmación.
- Tool calls no registradas.
- Sin límites de iteraciones o pasos del agente.
- El LLM decide autorización.
- Falta de allowlists de dominios, tablas, acciones o destinatarios.

## Controles defensivos

- **Diseño seguro**: tools específicas, pequeñas y orientadas a una sola tarea.
- **Validación de entradas**: clasificar contexto no confiable antes de permitir acciones.
- **Validación de salidas**: validar argumentos de tool con schemas estrictos.
- **Least privilege**: scopes mínimos y permisos separados por lectura/escritura.
- **Human-in-the-loop**: aprobación para enviar, borrar, comprar, publicar o modificar.
- **Logging y monitorización**: registrar tool name, argumentos, usuario, autorización y resultado.
- **Rate limiting**: límites de tool calls, pasos, coste y acciones por sesión.
- **Evaluaciones automáticas**: pruebas de indirect prompt injection y tool abuse.
- **Red teaming**: simular agentes confundidos por emails, documentos o API responses.
- **Controles específicos**: ejecutar tools en contexto del usuario, autorización backend, allowlists y kill switch.

## Checklist de auditoría

- [ ] ¿Existe inventario de tools y permisos?
- [ ] ¿Cada tool es necesaria para el objetivo del agente?
- [ ] ¿Se eliminaron tools de desarrollo o prueba?
- [ ] ¿Las tools tienen funciones específicas, no genéricas?
- [ ] ¿Los permisos son mínimos?
- [ ] ¿El agente actúa en contexto del usuario cuando corresponde?
- [ ] ¿Las APIs aplican autorización independiente del LLM?
- [ ] ¿Las acciones críticas requieren confirmación humana?
- [ ] ¿Hay límites de pasos y tool calls?
- [ ] ¿Se registran argumentos y resultados de tools?
- [ ] ¿Existe kill switch o modo read-only?

## Preguntas de entrevista

- ¿Qué significa excessive agency?
- ¿Cuál es la diferencia entre excessive functionality, permissions y autonomy?
- ¿Por qué prompt injection es más peligroso en agentes con tools?
- ¿Cómo diseñarías una tool segura para un LLM?
- ¿Qué acciones requerirían human-in-the-loop?
- ¿Por qué la autorización debe vivir fuera del modelo?

## Relación con Purple Team

**Red Team**:

- Simula contextos que intentan activar tools fuera de scope.
- Prueba si el agente usa funciones innecesarias.
- Evalúa si acciones sensibles se ejecutan sin confirmación.

**Blue Team**:

- Monitoriza tool calls inusuales.
- Alerta por acciones mutativas, destinos externos y cambios de permisos.
- Revisa secuencias largas de agente.

**Purple Team**:

- Ajusta scopes, schemas y allowlists.
- Convierte tool abuse en casos de regresión.
- Mide si los controles bloquean antes de la acción real.

## Mapeo con controles

- **OWASP ASVS**: control de acceso, autorización, seguridad de APIs, validación de entrada/salida, logging y gestión de sesión.
- **NIST AI RMF**: MAP para identificar acciones y actores; MEASURE para validar límites; MANAGE para controles operativos; GOVERN para accountability y aprobación.
- **MITRE ATLAS**: técnicas relacionadas con abuso de agentes y tool invocation, incluyendo `AML.T0053` cuando se comprometen plugins/tools y técnicas de impacto como data destruction via AI agent tool invocation cuando aplica.
- **Principios generales**: least privilege, complete mediation, zero trust, separation of duties, fail closed.

## Notas para Obsidian

Enlaces:

- [[OWASP LLM Top 10]]
- [[Excessive Agency]]
- [[LLM Agents]]
- [[Prompt Injection]]
- [[RAG Security]]
- [[AI Red Teaming]]
- [[AI Security Controls]]

## Resumen final

- Idea clave: la capacidad de actuar debe estar más restringida que la capacidad de responder.
- Riesgo principal: que una salida manipulada se convierta en acción real.
- Defensa más importante: tools mínimas, permisos mínimos, autorización backend y aprobación humana.
- Para entrevista: excessive agency es el puente entre AI Security y seguridad de identidad/API.

## Fuentes base

- [OWASP LLM06:2025 Excessive Agency](https://genai.owasp.org/llmrisk/llm062025-excessive-agency/)
- [MITRE ATLAS data](https://github.com/mitre-atlas/atlas-data)
