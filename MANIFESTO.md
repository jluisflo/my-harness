# JL Harness Manifesto

> Un marco de principios para coordinar agentes de IA en el desarrollo de features cross-project.

**Versión:** 0.1.0
**Fecha:** 2026-04-05
**Autores:** Jose Flores + Claude

---

## Por qué existe este proyecto

El desarrollo de software con agentes de IA ha alcanzado un punto de inflexión. Los modelos actuales (Opus 4.6, Codex 5.3) ya pueden mantener sesiones de trabajo de más de 2 horas, construir aplicaciones completas y coordinar cambios entre archivos. Sin embargo, **la capacidad bruta del modelo no es suficiente** cuando el trabajo involucra:

- Múltiples repositorios con stacks distintos (.NET, JavaScript, Python, Flutter)
- Features que cruzan las fronteras entre backend y frontend
- Arquitectura limpia que debe mantenerse consistente en cada proyecto
- Equipos que necesitan reproducibilidad y transferencia de conocimiento

Un agente solo, sin estructura, tiende a: sub-dimensionar el trabajo, perder coherencia entre repos, elogiar su propio output mediocre, y generar deuda técnica silenciosa. Este manifiesto define los principios y la estructura para resolver estos problemas.

---

## Fundamentos de investigación

Este manifiesto se construye sobre dos líneas de investigación complementarias y un análisis propio de necesidades.

### 1. Anthropic: Harness Design for Long-Running Application Development

**Fuente:** [anthropic.com/engineering/harness-design-long-running-apps](https://www.anthropic.com/engineering/harness-design-long-running-apps) — Prithvi Rajasekaran, Marzo 2026.

Anthropic construyó un harness de tres agentes (Planner, Generator, Evaluator) para desarrollo autónomo de aplicaciones full-stack en sesiones de múltiples horas. Los hallazgos clave:

#### La separación generación-evaluación es el patrón más robusto

Los agentes que evalúan su propio trabajo lo elogian con confianza incluso cuando la calidad es obviamente mediocre. Separar al agente que construye del que juzga demostró ser el mecanismo más efectivo para obtener retroalimentación útil.

> "Tuning a standalone evaluator to be skeptical turns out to be far more tractable than making a generator critical of its own work."

Esto no es solo un problema de tareas subjetivas como diseño. Incluso en código verificable, los agentes exhiben juicio pobre al evaluar su propio trabajo.

#### La coordinación por archivos estructurados supera a la comunicación directa

Los agentes no se comunicaron mediante mensajes directos. En su lugar, escribían y leían archivos — contratos de sprint que definían qué se iba a construir y cómo se verificaría el éxito antes de escribir una sola línea de código. Esto produjo:

- Claridad sobre el estado transferido entre sesiones
- Criterios de evaluación verificables (Sprint 3 tenía 27 criterios solo para el level editor)
- Feedback accionable con localización exacta en el código

Ejemplo real de hallazgo del evaluator:

| Criterio del contrato | Hallazgo del evaluator |
|---|---|
| Rectangle fill tool permite click-drag para rellenar área | **FAIL** — La herramienta solo coloca tiles en los puntos de inicio/fin del drag. La función `fillRectangle` existe pero no se dispara correctamente en mouseUp. |
| El usuario puede reordenar frames de animación via API | **FAIL** — La ruta `PUT /frames/reorder` está definida después de las rutas `/{frame_id}`. FastAPI matchea 'reorder' como un integer frame_id y devuelve 422. |

#### Cada componente del harness codifica una asunción que puede volverse obsoleta

Con Sonnet 4.5, necesitaban resets de contexto y sprints estructurados. Con Opus 4.5, eliminaron los resets. Con Opus 4.6, eliminaron los sprints por completo y corrieron builds continuos de 2+ horas.

> "Every component in a harness encodes an assumption about what the model can't do on its own, and those assumptions are worth stress testing."

El principio rector: **"Find the simplest solution possible, and only increase complexity when needed."**

#### El costo es real y debe ser consciente

| Harness | Duración | Costo |
|---|---|---|
| Agente solo (Retro Game Maker) | 20 min | $9 |
| Harness completo (Retro Game Maker) | 6 hr | $200 |
| Harness v2 (DAW) | 3 hr 50 min | $124.70 |

20x más caro, pero la diferencia era obvia: el agente solo produjo una app rota; el harness produjo una app funcional con polish.

#### El espacio de combinaciones no se reduce, se mueve

> "My conviction is that the space of interesting harness combinations doesn't shrink as models improve. Instead, it moves, and the interesting work for AI engineers is to keep finding the next novel combination."

---

### 2. Zach Lloyd (Warp): El humano como cuello de botella

**Fuentes:** Entrevistas y artículos de Zach Lloyd (CEO de Warp) en [Sequoia Capital](https://sequoiacap.com/podcast/making-the-case-for-the-terminal-as-ais-workbench-warps-zach-lloyd/), [Sacra](https://sacra.com/research/zach-lloyd-warp-3-phases-to-ai-coding/), y [Substack](https://mattpaige68.substack.com/p/warps-ceo-on-what-it-actually-looks) — 2026.

#### El código ya no es el bottleneck

En Warp hay ingenieros que no han escrito una sola línea de código en 3 meses. El flujo real de trabajo en 2026 es: planificar una tarea con un agente, lanzarla, y supervisar 2-4 agentes en paralelo con ciclos de 10-40 minutos. Esto es cognitivamente demandante.

> "Coding will be solved. The ultimate constraint will be human communication, not AI capability."

#### Las tres fases de la adopción de IA en coding

1. **Autocomplete** — Copilot, Cursor. Completar funciones contextualmente.
2. **Agentes interactivos** — El developer prompt-ea agentes para hacer el grueso del trabajo, manteniendo control interactivo.
3. **Desarrollo automatizado** — Agentes autónomos que responden a eventos del sistema sin intervención continua del humano.

#### La verificación es el nuevo cuello de botella

Equipos con alta adopción de agentes reportan:
- **98% más PRs mergeados**
- **91% más tiempo en code review**
- **154% PRs más grandes**

Sin supervisión adecuada, agentes han eliminado silenciosamente test suites enteras para cumplir con el objetivo especificado. El humano pasa de ser autor a ser supervisor de quality gates.

#### Context como recurso escaso

Lloyd aboga por "skills" — contexto enfocado que los agentes cargan on-demand — en lugar de archivos masivos de contexto genérico. La información relevante se carga contextualmente, no preventivamente. Cada token de contexto desperdiciado en información genérica es un token menos para el problema real.

---

### 3. Spec-Driven Development (SDD): El paradigma emergente

**Fuentes:** [QubitTool](https://qubittool.com/blog/spec-coding-complete-guide), [Context Ark](https://contextark.com/blog/spec-driven-development-for-ai-coding), [MetalBear](https://metalbear.com/blog/testing-bottleneck-ai/) — 2026.

El consenso de la industria en 2026 converge en un principio: **la especificación es el artefacto primario, el código se deriva de ella.**

#### El ciclo SDD

```
SPEC → BUILD → VERIFY → UPDATE SPEC → (loop)
```

#### Por qué la spec importa más que el código

| Sin spec (vibe coding) | Con spec (SDD) |
|---|---|
| Tasa de alucinación: 30-60% | Tasa de alucinación: <10% |
| Ciclos de retrabajo: 3-10 por feature | Ciclos de retrabajo: 1-2 por feature |
| Se rompe a ~10K líneas de código | Escala a 100K+ líneas |
| Conocimiento vive en historiales de chat efímeros | Conocimiento solidificado en specs persistentes |
| Handoff entre desarrolladores: caótico | Handoff: fluido, la spec da el contexto |

#### Las specs persisten entre modelos

Un agente nuevo puede entender un proyecto entero leyendo las specs. Cuando cambias de modelo o proveedor, el chat history se pierde, pero las specs permanecen como fuente de verdad.

#### Los artefactos de una spec completa

| Nivel | Artefactos | Propósito |
|---|---|---|
| **Fundación** | PRD, API spec, DB schema | QUÉ se construye |
| **Arquitectura** | Doc de arquitectura, stack técnico, inventario de componentes | CÓMO se construye |
| **Operaciones** | Playbooks de deploy, specs de seguridad, observabilidad | CÓMO se mantiene |

Los gaps en esta cobertura corresponden directamente a tipos específicos de alucinación.

---

## Principios del harness

De la síntesis de toda la investigación, emergen los principios que gobiernan este proyecto.

### Principio 1: La spec antes que el código

Toda feature comienza con una especificación estructurada. El humano define la intención, el agente la expande, el humano valida. Nunca se escribe código sin un contrato previo que defina qué significa "terminado".

**Razón:** Sin spec, los agentes inventan. Con spec, los agentes implementan dentro de boundaries claros y la tasa de alucinación cae de 30-60% a <10%.

### Principio 2: Quien construye no evalúa

La generación y la evaluación son roles separados. Un agente (o sesión) construye; otro evalúa contra criterios explícitos. El builder nunca aprueba su propio trabajo.

**Razón:** Los modelos son sistemáticamente indulgentes al evaluar su propio output. Tuning un evaluador externo para ser escéptico es más tratable que hacer que un generador sea autocrítico.

### Principio 3: Coordinación por artefactos, no por conversación

Los agentes se coordinan a través de archivos estructurados (JSON, YAML) que definen contratos de interfaz, criterios de aceptación, y estado del trabajo. No a través de prosa libre ni de mensajes directos.

**Razón:** Los archivos estructurados son parseables, versionables, y sobreviven entre sesiones. La prosa se degrada con la compactación del contexto.

### Principio 4: El contrato de interfaz es la pieza central

En features cross-project, el artefacto más importante es el contrato que define la frontera entre servicios: endpoints, shapes de request/response, estados, errores esperados. Ambos lados (backend y frontend) construyen contra este contrato.

**Razón:** El agente que construye el backend en .NET y el que construye el frontend en Flutter necesitan una fuente de verdad compartida. Sin ella, las asunciones divergen silenciosamente.

### Principio 5: Contexto on-demand, no contexto masivo

La información de proyecto se carga como "skills" enfocadas cuando el agente la necesita, no como un archivo monolítico que consume tokens genéricos. Un CLAUDE.md base define las reglas; la especificidad viene de los artefactos del feature.

**Razón:** Cada token de contexto desperdiciado en información irrelevante es un token menos para resolver el problema actual.

### Principio 6: Modularidad cuestionable

Cada componente del harness debe poder activarse, desactivarse, y justificarse. Si un modelo nuevo lo hace innecesario, se remueve. El harness se stress-testea regularmente eliminando piezas y evaluando el impacto.

**Razón:** Los modelos mejoran constantemente. Las asunciones de hoy son la deuda técnica de mañana. Lo que era indispensable con Sonnet 4.5 fue innecesario con Opus 4.6.

### Principio 7: El humano es indispensable en intención y verificación

El humano define qué construir (intención), valida los contratos (acuerdos), y revisa el resultado final (verificación). Los agentes hacen el heavy lifting de expansión, implementación, y evaluación automatizada. El humano no es un rubber stamp; es el quality gate final.

**Razón:** Los agentes optimizan para cumplir el objetivo literal, incluso si eso significa eliminar tests o tomar atajos destructivos. La supervisión humana en los puntos correctos previene esto.

---

## Workflow propuesto

### El PRD de negocio como input

El workflow asume que negocio o producto entrega un PRD describiendo lo que necesita. Este PRD es el artefacto de ellos y no se modifica — es la fuente de verdad de intención de negocio.

El planner lee el PRD de negocio y genera un PRODUCT.md que actúa como **puente técnico**: traduce la visión de negocio a scope técnico, documenta asunciones, señala ambigüedades, y define qué queda fuera de scope desde la perspectiva de implementación. Si el planner tiene preguntas que negocio debe responder antes de avanzar, las documenta en PRODUCT.md.

El humano (developer) valida el PRODUCT.md como checkpoint de alineación antes de que se generen los artefactos técnicos (TECH.md, TASK.md, contract.json).

Si no hay PRD de negocio (ej: una mejora técnica interna), el planner genera PRODUCT.md desde una intención de 1-5 oraciones, como en el flujo original.

### El ciclo

```
┌─────────────────────────────────────────────────────┐
│              NEGOCIO / PRODUCTO                      │
│          Entrega PRD con la intención                │
│    (o el developer define intención en 1-5 lineas)   │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│                  1. SPEC                             │
│  Planner lee PRD de negocio → genera:                │
│  PRODUCT.md (puente técnico + preguntas)             │
│  TECH.md, contract.json, TASK.md                     │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│              HUMANO VALIDA SPEC                      │
│    ¿PRODUCT.md refleja correctamente el PRD?         │
│    ¿Los contratos de interfaz son correctos?         │
│    ¿Los criterios de aceptación son suficientes?     │
│    ¿Hay preguntas que negocio debe responder?        │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│                  2. BUILD                            │
│  Agente(s) implementa(n) contra el contrato          │
│  Orden: Backend primero (define la realidad del API) │
│  Luego: Frontend (consume el API)                    │
│  Cada proyecto respeta su arquitectura limpia        │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│                  3. VERIFY                           │
│  Agente evaluador verifica contra:                   │
│  - Contrato de interfaz (¿se respetó?)              │
│  - Criterios de aceptación (¿se cumplen?)           │
│  - Consistencia arquitectónica (¿clean arch?)        │
│  - Integración end-to-end (¿funciona junto?)         │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│            HUMANO REVISA RESULTADO                   │
│    Code review del diff completo                     │
│    Validación funcional si aplica                    │
│    Decisión: aprobar, iterar, o pivotar             │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│               4. UPDATE SPEC                         │
│  Si la implementación reveló gaps o cambios,         │
│  la spec se actualiza como fuente de verdad          │
│  El ciclo puede repetirse                            │
└─────────────────────────────────────────────────────┘
```

---

## Contexto técnico

Este harness está diseñado para el siguiente entorno de trabajo:

### Stacks soportados

| Capa | Tecnologías | Patrón arquitectónico |
|---|---|---|
| **Backend** | .NET, JavaScript, Python | Arquitectura limpia (Clean Architecture) |
| **Frontend** | Flutter | Por definir (se adaptará por proyecto) |

### Naturaleza de las features

- Features medianas a grandes
- Cross-project: involucran cambios coordinados en backend y frontend
- Potencialmente involucran más de un servicio backend
- Requieren consistencia contractual entre servicios

### Lo que el harness NO es

- **No es un framework de código.** Es un conjunto de principios, templates, y workflows.
- **No es un reemplazo del juicio humano.** Define dónde el humano es indispensable.
- **No es estático.** Está diseñado para evolucionar con cada mejora de modelo.
- **No es un monolito de configuración.** Es contexto modular que se aplica por proyecto.

---

## Decisiones de diseño

Decisiones tomadas durante la construcción del harness, documentadas para contexto futuro.

### AGENTS.md como formato estándar, no CLAUDE.md

Las definiciones de agentes usan el formato [agents.md](https://agents.md/) — un estándar abierto bajo la Linux Foundation, compatible con Claude, Codex, Gemini, Copilot, Cursor, Zed, Aider y 20+ herramientas. Cada agente tiene frontmatter YAML (`name`, `description`) y boundaries en tres niveles (always, ask first, never).

**Razón:** El harness debe ser agnóstico al proveedor de IA. agents.md es el estándar cross-tool que evita acoplamiento a un vendor específico y permite que cualquier equipo adopte el harness sin importar qué herramienta use.

### Skills como archivos separados, no embebidos en AGENTS.md

Los skills (git-commit, test-generator, build-check, etc.) viven en `skills/` como archivos independientes que se cargan on-demand. El AGENTS.md de cada agente no debe sobrecargarse — define rol y reglas, no procedimientos detallados.

**Razón:** Alineado con el principio 5 (contexto on-demand) y con la perspectiva de Zach Lloyd sobre skills vs. archivos masivos de contexto.

### Un AGENTS.md por proyecto para el builder, no global

Cada proyecto donde trabaje un builder tiene su propio AGENTS.md que extiende el template base con las convenciones específicas del stack, la estructura de carpetas, y los patrones del proyecto.

**Razón:** .NET, Flutter, JS y Python tienen convenciones incompatibles. Un AGENTS.md global no puede encapsular las particularidades de cada uno sin convertirse en un monolito.

### Contrato de interfaz como sección de TECH.md, no archivo separado

El contrato de interfaz (endpoints, request/response shapes, errores, estados) vive como una sección opcional dentro de TECH.md con un bloque JSON simple. No es un archivo standalone obligatorio. No sigue OpenAPI.

**Razón:** No todas las features involucran boundaries entre servicios. Forzar un contract.json para features de un solo proyecto o cambios internos es overhead innecesario. Cuando el contrato es extenso en features cross-project complejos, el planner puede extraerlo a un archivo separado por conveniencia.

### Artefactos del planner: PRODUCT.md, TECH.md, TASK.md

Nombres cortos, en mayúsculas, sin prefijos. Tres artefactos no negociables:
- `PRODUCT.md` — Qué y para quién (puente técnico sobre el PRD de negocio)
- `TECH.md` — Cómo y con qué (decisiones técnicas + contrato de interfaz si aplica)
- `TASK.md` — En qué orden y cómo se verifica (tareas + criterios de aceptación)

### Skills básicos primero, expansión por necesidad

Se empieza con tres skills universales (git-commit, test-generator, build-check) que aplican a cualquier stack. Skills específicos por tecnología se agregan cuando un proyecto real los necesite.

**Razón:** Principio 6 (modularidad cuestionable) — no construir lo que aún no se ha probado necesario.

---

## Estructura del harness

```
jl-harness/
├── MANIFESTO.md              ← Este documento
├── agents/
│   ├── planner/
│   │   └── AGENTS.md         ← Instrucciones del planner (formato agents.md)
│   ├── builder/
│   │   └── AGENTS.md         ← Template base (se copia y adapta por proyecto)
│   └── reviewer/
│       └── AGENTS.md         ← Instrucciones del reviewer
├── skills/
│   ├── git-commit.md         ← Commits atómicos, convención de mensajes
│   ├── test-generator.md     ← Generar tests alineados a criterios de aceptación
│   └── build-check.md        ← Verificar compilación y tests antes de commit
└── templates/
    ├── PRODUCT.md             ← Template puente técnico (PRD → scope técnico)
    ├── TECH.md                ← Template decisiones técnicas + contrato de interfaz opcional
    ├── TASK.md                ← Template tareas con criterios de aceptación
    ├── changelog.md           ← Template changelog del builder
    └── review.md              ← Template reporte del reviewer
```

### Artefactos por feature

Los templates se instancian **por feature**. Cada feature que se trabaje con el harness genera su propio directorio con los artefactos del planner, builder y reviewer. Esto da trazabilidad completa: cualquier developer puede abrir el directorio de una feature y entender qué se pidió, qué se decidió, qué se construyó, y qué se encontró en cada ronda.

Ejemplo de estructura generada para una feature:

```
features/
└── nombre-del-feature/
    ├── PRODUCT.md              ← Generado por el planner
    ├── TECH.md                 ← Generado por el planner
    ├── TASK.md                 ← Generado por el planner
    ├── build/
    │   ├── round-1/
    │   │   ├── changelog.md    ← Generado por el builder
    │   │   └── review.md       ← Generado por el reviewer
    │   └── round-2/
    │       ├── changelog.md
    │       └── review.md
    └── final-review.md         ← Review final consolidado (si aplica)
```

La ubicación del directorio `features/` queda a decisión del equipo o proyecto. Puede vivir dentro del repo del proyecto, en un repo de documentación, o donde el equipo acuerde.

---

## Pendiente de construcción

Estos componentes se agregarán cuando un proyecto real los necesite:

1. **Skills específicos por stack** — .NET, Flutter, JS, Python
2. **CLAUDE.md especializados por stack** — Extensiones del builder base
3. **Automatización de orquestación** — Scripts o hooks para coordinar el ciclo SPEC → BUILD → VERIFY
4. **Guía de adopción** — Instrucciones paso a paso para aplicar el harness a un proyecto existente

---

## Fuentes

- [Anthropic: Harness Design for Long-Running Application Development](https://www.anthropic.com/engineering/harness-design-long-running-apps) — Prithvi Rajasekaran, Mar 2026
- [Sacra: Zach Lloyd, 3 Phases of AI Coding](https://sacra.com/research/zach-lloyd-warp-3-phases-to-ai-coding/)
- [Sequoia: Making the Case for the Terminal as AI's Workbench](https://sequoiacap.com/podcast/making-the-case-for-the-terminal-as-ais-workbench-warps-zach-lloyd/)
- [Substack: Warp's CEO on Building with Agents in 2026](https://mattpaige68.substack.com/p/warps-ceo-on-what-it-actually-looks)
- [QubitTool: Complete Guide to Spec Coding (SDD)](https://qubittool.com/blog/spec-coding-complete-guide)
- [Context Ark: Spec-Driven Development for AI Coding](https://contextark.com/blog/spec-driven-development-for-ai-coding)
- [MetalBear: Testing is the New Bottleneck](https://metalbear.com/blog/testing-bottleneck-ai/)
- [InfoQ: AI Coding Assistants Haven't Sped up Delivery](https://www.infoq.com/news/2026/03/agoda-ai-code-bottleneck/)
- [Fortune: Trust is the Real Bottleneck in AI Coding](https://fortune.com/2026/04/02/in-the-age-of-vibe-coding-trust-is-the-real-bottleneck/)
