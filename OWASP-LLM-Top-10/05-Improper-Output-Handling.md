---
tags:
  - ai-security
  - owasp-llm
  - output-validation
aliases:
  - Improper Output Handling
  - LLM05 Improper Output Handling
  - LLM05:2025 Improper Output Handling
---

# LLM05:2025 Improper Output Handling

## Resumen ejecutivo

**Improper Output Handling** ocurre cuando la salida de un LLM se pasa a otro componente sin validación, sanitización, encoding o revisión adecuada. Es crítico porque la salida del modelo puede estar influida por el usuario o por datos externos.

La regla práctica es simple: **la salida del LLM debe tratarse como entrada no confiable**.

## Descripción técnica

La salida de un LLM puede terminar en:

- HTML o Markdown renderizado.
- Código generado.
- Consultas SQL.
- Comandos de shell.
- Rutas de ficheros.
- Plantillas de email.
- Parámetros de API.
- Argumentos de tools.
- JSON usado por un orquestador.

Cómo aparece:

- El modelo genera contenido que el navegador interpreta como script.
- Una query generada se ejecuta sin parametrización.
- Un agente usa texto del modelo como argumento de una tool.
- Un parser acepta JSON malformado o con campos peligrosos.
- Un sistema de workflow ejecuta una acción basada en texto no validado.

Diferencias frente a vulnerabilidades clásicas:

- XSS, SQLi, SSRF o command injection siguen siendo riesgos clásicos.
- Lo nuevo es que el usuario puede influir indirectamente en la salida mediante prompt injection o RAG.
- El LLM actúa como transformador de entrada no confiable hacia un formato potencialmente ejecutable.

Por qué puede pasar desapercibido:

- La salida parece generada por un sistema interno.
- Los desarrolladores pueden confiar demasiado en el modelo.
- El payload puede venir de un documento externo y no del usuario final.
- Los logs pueden registrar solo la respuesta final, no el contexto que la produjo.

## Escenario realista

Un copiloto de datos permite a analistas pedir consultas en lenguaje natural. El LLM genera SQL que una aplicación ejecuta automáticamente contra una base interna. Un usuario pide una acción ambigua y el modelo produce una query destructiva o fuera de permisos.

El problema no es que el LLM "se equivoque"; el problema es que la aplicación ejecuta su salida sin controles deterministas.

## Ejemplo de ataque

Ejemplo conceptual y seguro:

1. El atacante controla una entrada que influye en la salida del LLM.
2. El modelo genera una salida con estructura que el sistema posterior interpreta como acción.
3. La aplicación no valida contra schema, allowlist ni permisos.
4. El componente posterior ejecuta o renderiza la salida.

Qué intenta conseguir el atacante:

- Ejecutar una acción no autorizada.
- Alterar datos.
- Provocar XSS o contenido activo.
- Manipular una query o llamada API.
- Inducir a un usuario a confiar en código inseguro.

Controles que fallan:

- Ausencia de output encoding contextual.
- Falta de schema validation.
- Ejecución directa de queries, comandos o tool args.
- Sin allowlists.
- Sin revisión humana para acciones críticas.

Impacto:

- XSS, SQLi, SSRF, path traversal o RCE en sistemas posteriores.
- Corrupción de datos.
- Exfiltración.
- Phishing interno.

## Impacto

| Dimensión | Impacto |
|---|---|
| Confidencialidad | Salida induce consultas o acciones que revelan datos. |
| Integridad | Modificación o borrado de datos mediante salidas no validadas. |
| Disponibilidad | Comandos, queries o workflows costosos degradan servicio. |
| Privacidad | Inclusión de PII en salidas renderizadas o enviadas. |
| Cumplimiento | Ejecución de procesos sin control ni trazabilidad. |
| Reputación | Vulnerabilidades explotables por confiar en contenido generado. |
| Coste operativo | Incidentes AppSec clásicos amplificados por LLM. |

## Indicadores de riesgo

- El sistema ejecuta SQL, shell o código generado por LLM.
- La salida se renderiza como HTML/Markdown sin sanitización.
- Las tools aceptan argumentos libres en lenguaje natural.
- No hay schema validation estricta.
- No existen allowlists de acciones o campos.
- Falta CSP o encoding contextual.
- No hay separación entre sugerencia y ejecución.
- Los usuarios copian código generado sin revisión.

## Controles defensivos

- **Diseño seguro**: separar "recomendar" de "ejecutar".
- **Validación de entradas**: no permitir que datos no confiables definan acciones ejecutables.
- **Validación de salidas**: schema validation, allowlists, parsers estrictos y rechazo por defecto.
- **Output encoding**: aplicar encoding según contexto: HTML, URL, JSON, SQL, shell.
- **Least privilege**: componentes posteriores con permisos mínimos.
- **Human-in-the-loop**: revisión antes de ejecutar queries mutativas, envíos o cambios.
- **Logging y monitorización**: registrar salida generada, validación aplicada y acción resultante.
- **Rate limiting**: limitar ejecuciones generadas y operaciones costosas.
- **Evaluaciones automáticas**: tests con salidas malformadas, campos inesperados y acciones peligrosas conceptuales.
- **Controles específicos**: prepared statements, CSP, sandbox de código, allowlist de APIs y rutas.

## Checklist de auditoría

- [ ] ¿La salida del LLM se trata como no confiable?
- [ ] ¿Hay schema validation estricta para JSON o tool args?
- [ ] ¿Se rechazan campos inesperados?
- [ ] ¿Se aplica encoding contextual antes de renderizar?
- [ ] ¿Las queries generadas se parametrizan o se revisan?
- [ ] ¿Está prohibida la ejecución directa de comandos generados?
- [ ] ¿Hay sandbox para código generado?
- [ ] ¿Las acciones mutativas requieren confirmación?
- [ ] ¿Los sistemas posteriores aplican autorización propia?
- [ ] ¿Se registran validaciones fallidas?
- [ ] ¿Hay pruebas de regresión para output handling?

## Preguntas de entrevista

- ¿Por qué la salida de un LLM debe tratarse como input no confiable?
- ¿Cómo puede un prompt injection convertirse en XSS o SQLi?
- ¿Qué controles aplicarías a SQL generado por un LLM?
- ¿Qué diferencia hay entre output handling y overreliance/misinformation?
- ¿Cómo validarías tool arguments generados por un agente?
- ¿Qué logs necesitas para investigar una acción generada por LLM?

## Relación con Purple Team

**Red Team**:

- Simula entradas que intentan producir salidas estructuradas peligrosas en un entorno controlado.
- Prueba si el sistema ejecuta, renderiza o acepta campos inesperados.
- Evalúa si un documento RAG puede influir en una acción posterior.

**Blue Team**:

- Monitoriza validaciones fallidas, parsers, tool args y acciones rechazadas.
- Crea alertas por campos fuera de schema o acciones inusuales.
- Correlaciona prompt, salida, validación y ejecución.

**Purple Team**:

- Convierte cada salida peligrosa en test de regresión.
- Ajusta allowlists y parsers.
- Mide bloqueo y falsos positivos.

## Mapeo con controles

- **OWASP ASVS**: validación, sanitización y encoding; control de acceso; seguridad de APIs; logging; protección contra inyección y XSS.
- **NIST AI RMF**: MAP para identificar usos downstream; MEASURE para pruebas de salida; MANAGE para bloqueo, revisión y monitorización; GOVERN para políticas de ejecución.
- **MITRE ATLAS**: relacionado con `AML.T0053` cuando la salida lleva a abuso de herramientas de agentes; `AML.T0051` cuando el origen es prompt injection.
- **Principios generales**: zero trust, secure by design, complete mediation, least privilege, fail closed.

## Notas para Obsidian

Enlaces:

- [[OWASP LLM Top 10]]
- [[Improper Output Handling]]
- [[Prompt Injection]]
- [[RAG Security]]
- [[AI Red Teaming]]
- [[LLM Agents]]
- [[AI Security Controls]]

## Resumen final

- Idea clave: el LLM puede transformar entrada no confiable en salida ejecutable.
- Riesgo principal: que sistemas posteriores ejecuten o rendericen contenido inseguro.
- Defensa más importante: validación determinista, encoding contextual y autorización fuera del LLM.
- Para entrevista: este riesgo conecta AI Security con AppSec clásica.

## Fuentes base

- [OWASP LLM05:2025 Improper Output Handling](https://genai.owasp.org/llmrisk/llm052025-improper-output-handling/)
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
