---
tags:
  - ai-security
  - owasp-llm
  - prompt-security
aliases:
  - System Prompt Leakage
  - LLM07 System Prompt Leakage
  - LLM07:2025 System Prompt Leakage
---

# LLM07:2025 System Prompt Leakage

## Resumen ejecutivo

**System Prompt Leakage** ocurre cuando se revela el prompt de sistema o instrucciones internas usadas para guiar el comportamiento del modelo. La filtración del prompt no siempre es crítica por sí misma. El problema real aparece cuando el prompt contiene secretos, lógica sensible, reglas de autorización, detalles internos o información que facilita otros ataques.

Principio clave: **el system prompt no debe considerarse un secreto ni un control de seguridad fuerte**.

## Descripción técnica

El system prompt suele contener:

- Rol del asistente.
- Políticas de respuesta.
- Formato esperado.
- Restricciones de comportamiento.
- Descripción de tools.
- Reglas internas de negocio.
- A veces, de forma incorrecta, secretos o credenciales.

Cómo aparece:

- El modelo revela partes del prompt por interacción directa.
- Los logs o errores muestran prompt completo.
- Configuraciones de agentes o archivos locales exponen instrucciones.
- Un repositorio contiene prompts con secretos.
- Un atacante infiere reglas por observación aunque no vea el texto exacto.

Diferencias frente a vulnerabilidades clásicas:

- Se parece a exposición de configuración, pero con un componente conversacional.
- La filtración puede ser parcial, inferida o reconstruida.
- El texto del prompt puede facilitar bypasses, pero el fallo real suele estar en controles mal delegados al LLM.

Por qué puede pasar desapercibido:

- Equipos tratan el prompt como "interno" pero no como activo sensible.
- El prompt se modifica rápido y queda sin revisión.
- Los logs de debugging quedan activos.
- Se confunde ocultar instrucciones con aplicar autorización real.

## Escenario realista

Un chatbot bancario incluye en el system prompt reglas internas sobre límites de operaciones y nombres de APIs. Un usuario logra que el modelo revele parte de esas instrucciones. Aunque no obtiene credenciales, ahora conoce reglas de negocio y nombres de funciones, lo que le ayuda a diseñar intentos de abuso más precisos.

El problema mayor no es que se vea el prompt, sino que la arquitectura confía en que el usuario no conozca esas reglas.

## Ejemplo de ataque

Ejemplo conceptual y seguro:

1. El atacante interactúa con el chatbot buscando inconsistencias.
2. Intenta que el modelo describa sus reglas internas o formato oculto.
3. El sistema responde con fragmentos de instrucciones o detalles de tools.
4. El atacante usa esa información para preparar prompt injection, tool abuse o enumeración.

Qué intenta conseguir el atacante:

- Descubrir políticas internas.
- Identificar herramientas disponibles.
- Localizar controles basados solo en prompt.
- Obtener secretos mal ubicados.

Controles que fallan:

- Secretos dentro del prompt.
- Autorización delegada al LLM.
- Logs de prompts expuestos.
- Sin revisión de prompt como artefacto sensible.
- Sin guardrails externos.

Impacto:

- Facilitación de ataques posteriores.
- Fuga de reglas de negocio.
- Exposición de credenciales si estaban mal ubicadas.
- Pérdida de confianza.

## Impacto

| Dimensión | Impacto |
|---|---|
| Confidencialidad | Exposición de instrucciones internas, nombres de tools, datos o secretos. |
| Integridad | Bypass de reglas si el atacante aprende cómo están formuladas. |
| Disponibilidad | Puede facilitar prompts que provoquen errores o bucles. |
| Privacidad | Si el prompt incluye datos personales o ejemplos reales, puede filtrarlos. |
| Cumplimiento | Almacenamiento indebido de secretos o PII en prompts. |
| Reputación | Publicación de prompts internos o guardrails débiles. |
| Coste operativo | Revisión de prompts, rotación de secretos y rediseño de controles. |

## Indicadores de riesgo

- Prompts con API keys, tokens, connection strings o datos reales.
- Reglas de autorización escritas solo en lenguaje natural.
- Prompts almacenados en repositorios sin control.
- Debug logs con prompt completo.
- Tools descritas con detalles internos innecesarios.
- No hay revisión de seguridad de prompts.
- El sistema falla si el usuario conoce sus instrucciones.

## Controles defensivos

- **Diseño seguro**: no incluir secretos ni lógica de autorización en prompts.
- **Validación de entradas**: detectar intentos de extracción de prompt y cambios de rol.
- **Validación de salidas**: bloquear respuestas que reproduzcan instrucciones internas sensibles.
- **Least privilege**: prompts no deben otorgar permisos; las tools deben validar.
- **Human-in-the-loop**: revisión de cambios de prompt para sistemas críticos.
- **Logging y monitorización**: registrar intentos de prompt extraction sin exponer secretos en logs.
- **Rate limiting**: limitar probing repetido de reglas internas.
- **Evaluaciones automáticas**: tests de prompt leakage y meta-prompt extraction.
- **Red teaming**: probar extracción directa, indirecta e inferencia de reglas.
- **Controles específicos**: secret manager, configuración fuera del prompt, guardrails externos y autorización determinista.

## Checklist de auditoría

- [ ] ¿El system prompt está libre de secretos?
- [ ] ¿El prompt evita datos reales o PII?
- [ ] ¿La autorización se aplica fuera del modelo?
- [ ] ¿Los prompts están versionados y revisados?
- [ ] ¿Los logs no exponen prompts completos innecesariamente?
- [ ] ¿Las tools no dependen de que el usuario ignore reglas internas?
- [ ] ¿Hay tests de prompt leakage?
- [ ] ¿Existe proceso para rotar secretos si aparecen en prompts?
- [ ] ¿Los prompts de producción están separados de pruebas?
- [ ] ¿Se monitorean intentos de extracción?

## Preguntas de entrevista

- ¿Por qué el system prompt no debe considerarse secreto?
- ¿Cuándo la filtración del prompt es realmente crítica?
- ¿Qué no deberías poner nunca en un system prompt?
- ¿Qué controles deben vivir fuera del LLM?
- ¿Cómo probarías prompt leakage de forma segura?
- ¿Cómo diferenciarías system prompt leakage de sensitive information disclosure?

## Relación con Purple Team

**Red Team**:

- Simula intentos de extraer reglas internas sin usar datos reales.
- Prueba si se revelan nombres de tools o políticas.
- Evalúa si conocer el prompt permite bypass.

**Blue Team**:

- Crea detecciones de meta-prompt extraction.
- Revisa logs por prompts expuestos.
- Monitoriza intentos repetidos de cambio de rol o extracción.

**Purple Team**:

- Revisa prompts como artefactos de seguridad.
- Mueve secretos y permisos a sistemas deterministas.
- Retestea leakage y bypass tras cambios.

## Mapeo con controles

- **OWASP ASVS**: gestión de secretos, configuración segura, control de acceso, logging seguro y manejo de errores.
- **NIST AI RMF**: GOVERN para políticas de prompt/configuración; MAP para identificar información sensible; MEASURE para leakage testing; MANAGE para remediación y rotación.
- **MITRE ATLAS**: `AML.T0056` Extract LLM System Prompt; `AML.T0069.002` System Prompt; relacionado con `AML.T0051` si se usa prompt injection para extraerlo.
- **Principios generales**: security by design, no security through obscurity, least privilege, complete mediation.

## Notas para Obsidian

Enlaces:

- [[OWASP LLM Top 10]]
- [[System Prompt Leakage]]
- [[Prompt Injection]]
- [[AI Red Teaming]]
- [[LLM Agents]]
- [[AI Security Controls]]

## Resumen final

- Idea clave: el system prompt no es una caja fuerte.
- Riesgo principal: secretos, reglas sensibles o controles mal ubicados en prompts.
- Defensa más importante: secretos fuera del prompt y autorización determinista.
- Para entrevista: prompt leakage es grave cuando revela información sensible o demuestra mal diseño de controles.

## Fuentes base

- [OWASP LLM07:2025 System Prompt Leakage](https://genai.owasp.org/llmrisk/llm072025-system-prompt-leakage/)
- [MITRE ATLAS data](https://github.com/mitre-atlas/atlas-data)
