---
tags:
  - ai-security
  - owasp
  - llm-security
  - purple-team
aliases:
  - OWASP LLM Top 10
  - OWASP Top 10 for LLM Applications
---

# OWASP Top 10 for LLM Applications

## Introducción

El **OWASP Top 10 for LLM Applications** es una guía de referencia para identificar los riesgos más relevantes en aplicaciones que integran modelos de lenguaje, RAG, agentes, herramientas externas, plugins, memoria, APIs y datos empresariales.

La versión usada en estas notas es **OWASP Top 10 for LLM Applications 2025**, verificada el **2026-05-06** contra el sitio oficial de OWASP GenAI Security Project.

Estas notas no tratan solo de "seguridad del modelo". Tratan de la seguridad del **sistema completo**:

- Usuario y canal de entrada.
- Prompt de sistema y políticas.
- Contexto recuperado desde [[RAG Security]].
- [[Vector Databases]] y embeddings.
- Modelo base o modelo fine-tuned.
- Output parsers, renderizado y acciones posteriores.
- [[LLM Agents]], herramientas, APIs, identidad y permisos.
- Logging, monitorización, evaluación y respuesta.

## Por qué importa en AI Security

Las aplicaciones LLM cambian el modelo de amenaza porque introducen un componente probabilístico que interpreta lenguaje natural, combina instrucciones con datos y puede tomar decisiones sobre acciones externas.

En una aplicación tradicional, una entrada maliciosa suele afectar a un parser, una query, una plantilla o una API. En una aplicación LLM, esa entrada también puede afectar al **razonamiento aparente**, a la selección de herramientas, al contexto que se recupera y a la forma en que otros sistemas reciben la salida.

Esto importa porque muchas empresas están conectando LLMs a:

- Documentación interna.
- CRM y ticketing.
- Repositorios de código.
- Herramientas de correo y calendario.
- Sistemas financieros.
- Bases de datos vectoriales.
- Automatizaciones con permisos reales.

El riesgo principal no es que el modelo "diga algo raro". El riesgo es que una salida no confiable se convierta en una acción, decisión o filtración dentro de un proceso empresarial.

## Seguridad tradicional vs seguridad específica de LLMs

| Área | AppSec tradicional | Seguridad LLM |
|---|---|---|
| Entrada | Formularios, APIs, archivos, cabeceras | Prompts, documentos, emails, páginas web, imágenes, contexto RAG |
| Límite de confianza | Usuario vs backend | Usuario, datos recuperados, herramientas, memoria, modelo y salida |
| Control principal | Validación determinista, autorización, encoding | Validación determinista + guardrails + evaluación + límites de agencia |
| Vulnerabilidad típica | SQLi, XSS, SSRF, auth bypass | Prompt injection, RAG poisoning, tool abuse, leakage, hallucination |
| Salida | HTML, JSON, queries, ficheros | Lenguaje natural, código, comandos sugeridos, tool calls, decisiones |
| Riesgo operativo | Explotación de aplicación | Explotación de flujo AI + sistemas conectados |
| Pruebas | SAST, DAST, pentest, fuzzing | AppSec + evals, adversarial testing, red teaming, telemetry review |
| Mitigación | Parches, validación, hardening | Defensa en profundidad, mínimo privilegio, HITL, trazabilidad, políticas de datos |

La diferencia clave: en LLM Security hay que tratar al modelo como un componente **no determinista y no confiable**, incluso cuando sea útil y esté alineado. El prompt no sustituye a la autorización, al control de acceso ni a la validación de salida.

## Tabla resumen de los 10 riesgos

| Código | Riesgo oficial 2025 | Idea principal | Archivo |
|---|---|---|---|
| LLM01 | Prompt Injection | Entradas directas o indirectas cambian el comportamiento previsto del modelo. | [[01-Prompt-Injection]] |
| LLM02 | Sensitive Information Disclosure | El sistema revela PII, secretos, datos internos o información propietaria. | [[02-Sensitive-Information-Disclosure]] |
| LLM03 | Supply Chain | Dependencias, modelos, datasets, herramientas o proveedores introducen riesgo. | [[03-Supply-Chain]] |
| LLM04 | Data and Model Poisoning | Datos o modelos manipulados degradan integridad, seguridad o comportamiento. | [[04-Data-and-Model-Poisoning]] |
| LLM05 | Improper Output Handling | La salida del LLM se usa sin validación en navegadores, APIs, queries o acciones. | [[05-Improper-Output-Handling]] |
| LLM06 | Excessive Agency | El agente tiene demasiadas funciones, permisos o autonomía. | [[06-Excessive-Agency]] |
| LLM07 | System Prompt Leakage | El sistema expone prompts, reglas internas o secretos mal ubicados. | [[07-System-Prompt-Leakage]] |
| LLM08 | Vector and Embedding Weaknesses | Fallos en embeddings, retrieval o vector stores causan leakage o manipulación. | [[08-Vector-and-Embedding-Weaknesses]] |
| LLM09 | Misinformation | El modelo genera información falsa, no verificada o engañosamente convincente. | [[09-Misinformation]] |
| LLM10 | Unbounded Consumption | Uso no acotado causa costes, DoS, degradación o extracción de modelo. | [[10-Unbounded-Consumption]] |

## Mapa mental textual

```text
OWASP LLM Top 10
├── Control del comportamiento
│   ├── LLM01 Prompt Injection
│   ├── LLM07 System Prompt Leakage
│   └── LLM09 Misinformation
├── Datos y conocimiento
│   ├── LLM02 Sensitive Information Disclosure
│   ├── LLM04 Data and Model Poisoning
│   └── LLM08 Vector and Embedding Weaknesses
├── Integraciones y ejecución
│   ├── LLM03 Supply Chain
│   ├── LLM05 Improper Output Handling
│   └── LLM06 Excessive Agency
└── Operación y abuso de recursos
    └── LLM10 Unbounded Consumption
```

## Relación con Red Team, Blue Team y Purple Team

### Red Team

El Red Team simula abuso realista de la aplicación LLM:

- Inyección directa e indirecta.
- Manipulación de documentos usados por RAG.
- Descubrimiento de herramientas y límites.
- Intentos de inducir filtraciones o acciones no autorizadas.
- Pruebas de resistencia frente a alucinaciones y dependencia excesiva.

El objetivo no es conseguir una frase llamativa del modelo, sino demostrar un impacto verificable: dato filtrado, tool call indebido, acción no autorizada, resultado de negocio manipulado o coste excesivo.

### Blue Team

El Blue Team diseña detección y respuesta:

- Logs de prompts, respuestas, tool calls, retrieval y errores.
- Detección de patrones de abuso y anomalías de consumo.
- Alertas por acceso a documentos sensibles o tool calls de alto impacto.
- Métricas de bloqueo, latencia, falsos positivos y severidad residual.
- Procedimientos de contención: desactivar herramientas, rotar tokens, bloquear fuentes RAG, invalidar embeddings.

### Purple Team

El Purple Team convierte ataques en mejoras defensivas:

- Define test cases por riesgo OWASP.
- Ejecuta simulaciones seguras y controladas.
- Mide qué controles detectan, bloquean o fallan.
- Documenta gaps y remediaciones.
- Retestea hasta que el control sea observable, repetible y defendible.

## Principios de diseño seguro

- **No confiar en el modelo**: tratar la salida como entrada no confiable.
- **Separar instrucciones y datos**: delimitar prompt de sistema, usuario y contexto recuperado.
- **Mínimo privilegio**: herramientas y agentes con scopes estrechos y auditables.
- **Autorización fuera del LLM**: las decisiones de permisos deben ser deterministas.
- **Human-in-the-loop**: aprobación humana para acciones irreversibles o sensibles.
- **Validación de salida**: schema validation, allowlists, encoding, sanitización y revisión.
- **Observabilidad**: logging de prompt, contexto, retrieval, tool calls, costes y decisiones.
- **Evaluación continua**: pruebas adversarias, regresión de prompts y monitorización en producción.

## Uso práctico en auditoría

Durante una revisión de seguridad, analiza la aplicación como un flujo:

```text
Entrada -> Orquestador -> Prompt -> Modelo -> Retrieval -> Herramientas -> Salida -> Acción -> Logs
```

Preguntas clave:

- ¿Qué datos no confiables entran al contexto?
- ¿Qué fuentes puede consultar el sistema?
- ¿Qué herramientas puede invocar?
- ¿Con qué identidad actúa?
- ¿Qué salida se usa en sistemas posteriores?
- ¿Qué acciones requieren confirmación humana?
- ¿Qué queda registrado para investigación?
- ¿Qué límites existen de coste, tasa y tamaño?

## Fuentes base

- [OWASP Top 10 for LLM Applications 2025](https://genai.owasp.org/llm-top-10/)
- [OWASP GenAI Security Project](https://genai.owasp.org/)
- [MITRE ATLAS data](https://github.com/mitre-atlas/atlas-data)
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)

## Notas para Obsidian

Enlaces relacionados:

- [[OWASP LLM Top 10]]
- [[Prompt Injection]]
- [[RAG Security]]
- [[AI Red Teaming]]
- [[LLM Agents]]
- [[Vector Databases]]
- [[AI Security Controls]]
- [[Purple Team]]
