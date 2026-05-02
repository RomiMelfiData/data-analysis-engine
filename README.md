# Data Analysis Engine — Motor de Ejecución

---

## 🇦🇷 Español

### ¿Qué es?

Motor de ejecución de análisis de datos estructurado en etapas (E1–E8), diseñado para Claude Code. Opera bajo un Manual de Operaciones y árbol de decisiones con respaldo bibliográfico. Garantiza rigor metodológico, no-redundancia y decisiones documentadas en cada paso.

### ¿Qué hace?

- **E1 — Diseño**: elige entre modo EDA o HIPÓTESIS antes de tocar los datos
- **E2 — Carga**: ejecuta `info()` y `describe()` automático; detecta tipo de dato y alertas de calidad
- **E3 — Exploración**: estadística descriptiva, correlaciones, distribuciones, detección de outliers
- **E4 — Limpieza**: imputación, winsorización, encoding, análisis de sensibilidad
- **E5 — Inferencia**: árbol de selección de tests, tamaño del efecto, intervalos de confianza
- **E6 — Modelado**: selección de modelo, CV estratificada, feature importance
- **E7 — Validación**: comparación de modelos, métricas, rollback si baja calidad
- **E8 — Reporte**: salida en Markdown, HTML con gráficos, Jupyter Notebook, TXT o PDF

### Características clave

- ✅ Pivotaje bidireccional EDA ↔ HIPÓTESIS con registro explícito
- ✅ Rollback justificado bibliográficamente si se detecta baja calidad
- ✅ Sin redundancia: nunca recalcula análisis ya ejecutados en la sesión
- ✅ Recomendación de la IA en cada tabla de decisión
- ✅ Todos los scripts se guardan en la carpeta del proyecto (no en Temp/)
- ✅ Compatible con Windows (encoding UTF-8 explícito)

### Instalación en Claude Code

**Personal (todos los proyectos):**
```
unzip data-analysis-engine.skill -d ~/.claude/skills/
```

**Project-scoped (solo un proyecto):**
```
unzip data-analysis-engine.skill -d .claude/skills/
```

### Uso

Simplemente decile a Claude Code:
> "quiero analizar un dataset"

La skill se activa automáticamente y arranca desde E1.

### Dependencias Python

```
pandas, scipy, statsmodels, scikit-learn, imbalanced-learn
```

---

## 🇺🇸 English

### What is it?

A structured data analysis execution engine for Claude Code, organized in stages (E1–E8). It operates under an Operations Manual and a bibliographically-backed decision tree. It guarantees methodological rigor, no redundancy, and documented decisions at every step.

### What does it do?

- **E1 — Design**: chooses between EDA or HYPOTHESIS mode before touching the data
- **E2 — Load**: automatically runs `info()` and `describe()`; detects data type and quality alerts
- **E3 — Exploration**: descriptive statistics, correlations, distributions, outlier detection
- **E4 — Cleaning**: imputation, winsorization, encoding, sensitivity analysis
- **E5 — Inference**: test selection tree, effect size, confidence intervals
- **E6 — Modeling**: model selection, stratified CV, feature importance
- **E7 — Validation**: model comparison, metrics, rollback if low quality
- **E8 — Report**: output in Markdown, HTML with charts, Jupyter Notebook, TXT or PDF

### Key features

- ✅ Bidirectional pivoting EDA ↔ HYPOTHESIS with explicit logging
- ✅ Bibliographically justified rollback if low quality is detected
- ✅ No redundancy: never recalculates analyses already run in the session
- ✅ AI recommendation included in every decision table
- ✅ All scripts saved in the project folder (not in Temp/)
- ✅ Windows compatible (explicit UTF-8 encoding)

### Installation in Claude Code

**Personal (all projects):**
```
unzip data-analysis-engine.skill -d ~/.claude/skills/
```

**Project-scoped (one project only):**
```
unzip data-analysis-engine.skill -d .claude/skills/
```

### Usage

Simply tell Claude Code:
> "I want to analyze a dataset"

The skill activates automatically and starts from E1.

### Python dependencies

```
pandas, scipy, statsmodels, scikit-learn, imbalanced-learn
```
