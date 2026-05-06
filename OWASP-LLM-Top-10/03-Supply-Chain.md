---
tags:
  - ai-security
  - owasp-llm
  - supply-chain
aliases:
  - Supply Chain
  - LLM03 Supply Chain
  - LLM03:2025 Supply Chain
---

# LLM03:2025 Supply Chain

## Resumen ejecutivo

**Supply Chain** en aplicaciones LLM cubre riesgos derivados de modelos, datasets, librerías, pipelines, plugins, adaptadores, proveedores, servicios cloud, herramientas de evaluación y componentes de despliegue. La cadena de suministro AI amplía la AppSec tradicional porque incluye artefactos difíciles de inspeccionar: pesos de modelos, datasets, LoRA adapters, embeddings y proveedores de inferencia.

Un fallo de supply chain puede introducir malware, backdoors, sesgos, leakage, licencias incompatibles o comportamientos inseguros en producción.

## Descripción técnica

Componentes típicos de la cadena de suministro LLM:

- Modelo base o fine-tuned.
- Datasets de pre-training, fine-tuning y evaluación.
- Adaptadores LoRA/PEFT.
- Librerías Python/JavaScript y frameworks de orquestación.
- Plugins, tools y MCP servers.
- Servicios de vector database.
- Proveedores de inferencia y APIs.
- Contenedores, notebooks, pipelines MLOps y CI/CD.
- Prompts, plantillas y evaluaciones externas.

Cómo aparece:

- Modelo descargado de repositorio público sin verificación.
- Dependencia vulnerable en pipeline de entrenamiento o inferencia.
- Dataset con licencia incompatible o contenido contaminado.
- Plugin que solicita permisos excesivos.
- Proveedor cambia términos de uso o retención de datos.
- Artefacto serializado ejecuta código al cargarse.

Diferencias frente a vulnerabilidades clásicas:

- AppSec tradicional revisa dependencias y contenedores.
- En AI también hay que revisar datos, pesos, prompts, embeddings, adapters y procedencia del modelo.
- Un modelo puede comportarse mal sin que haya una vulnerabilidad de código visible.

Por qué puede pasar desapercibido:

- Los modelos son artefactos opacos y grandes.
- Las pruebas funcionales pueden no activar backdoors.
- Los equipos de datos pueden no estar integrados con seguridad.
- Los cambios de proveedor pueden pasar por compra o producto, no por AppSec.
- Los benchmarks públicos pueden no cubrir el caso de uso real.

## Escenario realista

Un equipo crea un copiloto interno de desarrollo y descarga un modelo open source con un adaptador LoRA recomendado en un foro. El adaptador mejora resultados en una tarea concreta, pero no se verifica su procedencia ni se ejecutan pruebas adversarias.

Semanas después, el copiloto empieza a recomendar paquetes inexistentes o inseguros en ciertos contextos. La causa es un componente no evaluado de la cadena AI.

## Ejemplo de ataque

Ejemplo conceptual y seguro:

1. Un atacante publica un modelo, dataset o adaptador que parece útil.
2. Lo documenta con métricas atractivas y ejemplos benignos.
3. Un equipo lo integra sin verificar hash, procedencia, licencia ni comportamiento adversario.
4. En producción, el componente introduce respuestas sesgadas, fuga de datos o recomendaciones inseguras.

Qué intenta conseguir el atacante:

- Comprometer el pipeline de AI.
- Introducir una puerta trasera conductual.
- Forzar uso de dependencias maliciosas.
- Obtener datos enviados a un proveedor o plugin.
- Dañar la confianza en el sistema.

Controles que fallan:

- Falta de AI BOM/SBOM.
- Ausencia de verificación criptográfica.
- Sin revisión de licencias y términos.
- No hay evaluación adversaria del modelo.
- No existe proceso de aprobación de plugins o adapters.

Impacto:

- Compromiso de entorno de desarrollo o inferencia.
- Respuestas inseguras en producción.
- Exposición legal por licencias o datos.
- Manipulación del comportamiento del modelo.

## Impacto

| Dimensión | Impacto |
|---|---|
| Confidencialidad | Proveedor, plugin o dependencia accede a datos sensibles. |
| Integridad | Modelo, dataset o adapter introduce comportamiento manipulado. |
| Disponibilidad | Dependencia vulnerable o proveedor causa caída del servicio. |
| Privacidad | Términos de proveedor permiten retención o entrenamiento no deseado. |
| Cumplimiento | Licencias incompatibles, copyright o tratamiento de datos no autorizado. |
| Reputación | Uso de modelos contaminados o respuestas peligrosas. |
| Coste operativo | Sustitución de modelos, revisión legal, reentrenamiento, auditoría y parches. |

## Indicadores de riesgo

- No existe inventario de modelos, datasets y dependencias.
- Se usan modelos públicos sin verificación de origen.
- No hay hash, firma ni control de versiones de artefactos.
- Se cargan formatos inseguros o serializados sin sandbox.
- Los plugins se aprueban sin análisis de permisos.
- No se revisan términos de proveedores LLM.
- Los entornos de notebooks tienen acceso a secretos.
- No hay proceso formal para cambios de modelo.

## Controles defensivos

- **Diseño seguro**: proceso de aprobación para modelos, datasets, adapters, tools y proveedores.
- **Inventario**: AI BOM/SBOM con versión, origen, licencia, hash, owner y uso.
- **Validación de artefactos**: firmas, hashes, repositorios confiables y escaneo.
- **Sandboxing**: cargar modelos y datasets en entornos aislados.
- **Least privilege**: pipelines y notebooks sin acceso amplio a secretos o producción.
- **Evaluaciones automáticas**: seguridad, privacidad, sesgo, jailbreak, leakage y robustez antes de producción.
- **Revisión legal/GRC**: licencias, copyright, retención de datos y uso comercial.
- **Monitorización**: cambios de comportamiento tras actualizaciones.
- **Red teaming**: pruebas con triggers, casos de uso reales y escenarios de abuso.
- **MLOps seguro**: control de cambios, aprobación, rollback y trazabilidad.

## Checklist de auditoría

- [ ] ¿Existe AI BOM/SBOM actualizado?
- [ ] ¿Todos los modelos tienen origen, versión, licencia y hash?
- [ ] ¿Se verifican modelos y adapters antes de usarlos?
- [ ] ¿Se revisan datasets por procedencia, licencia y sensibilidad?
- [ ] ¿Los formatos de carga son seguros o están aislados?
- [ ] ¿Los proveedores LLM tienen revisión de privacidad y seguridad?
- [ ] ¿Los plugins/tools pasan revisión de permisos?
- [ ] ¿Hay escaneo de dependencias y contenedores?
- [ ] ¿El cambio de modelo requiere aprobación y pruebas?
- [ ] ¿Hay rollback si una versión se degrada?
- [ ] ¿Se evalúa comportamiento adversario antes de producción?

## Preguntas de entrevista

- ¿Por qué la supply chain AI es más amplia que la supply chain de software?
- ¿Qué incluirías en un AI BOM?
- ¿Qué riesgos tiene descargar modelos de repositorios públicos?
- ¿Cómo evaluarías un proveedor LLM antes de producción?
- ¿Qué controles aplicarías a LoRA adapters o modelos fine-tuned?
- ¿Cómo detectarías un cambio de comportamiento tras actualizar un modelo?

## Relación con Purple Team

**Red Team**:

- Simula integración de un componente no confiable en entorno de prueba.
- Evalúa si un modelo/adaptador responde mal ante triggers conceptuales.
- Prueba si el pipeline acepta datasets sin procedencia.

**Blue Team**:

- Monitoriza integridad de artefactos.
- Genera alertas por cambios de modelo, dependencia o proveedor.
- Revisa logs de carga de artefactos y ejecución de notebooks.

**Purple Team**:

- Valida que el proceso bloquee componentes no aprobados.
- Convierte cambios de supply chain en pruebas de control.
- Define evidencias de cumplimiento para auditoría.

## Mapeo con controles

- **OWASP ASVS**: gestión de dependencias, configuración segura, seguridad de APIs, control de acceso y logging.
- **NIST AI RMF**: GOVERN para políticas de proveedores y AI BOM; MAP para identificar dependencias y datos; MEASURE para evaluación de componentes; MANAGE para vulnerabilidades, cambios y respuesta.
- **MITRE ATLAS**: `AML.T0010` AI Supply Chain Compromise; `AML.T0020` Poison Training Data; `AML.T0018` Backdoor AI Model cuando la cadena introduce modelos manipulados.
- **Principios generales**: secure by design, provenance, least privilege, defense in depth, change control.

## Notas para Obsidian

Enlaces:

- [[OWASP LLM Top 10]]
- [[Supply Chain Security]]
- [[LLMOps]]
- [[AI Red Teaming]]
- [[LLM Agents]]
- [[Vector Databases]]
- [[AI Security Controls]]

## Resumen final

- Idea clave: en AI, la cadena de suministro incluye modelos, datos, prompts, plugins y proveedores.
- Riesgo principal: introducir comportamiento o código no confiable en sistemas LLM.
- Defensa más importante: inventario, procedencia, verificación, evaluación y control de cambios.
- Para entrevista: un modelo es un artefacto de software y datos, no una caja mágica fuera de AppSec.

## Fuentes base

- [OWASP LLM03:2025 Supply Chain](https://genai.owasp.org/llmrisk/llm032025-supply-chain/)
- [MITRE ATLAS data](https://github.com/mitre-atlas/atlas-data)
