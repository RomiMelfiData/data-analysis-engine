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

Cloná el repositorio y copiá la carpeta completa a tu directorio de skills:

**Personal (todos los proyectos):**
```
git clone https://github.com/RomiMelfiData/data-analysis-engine.git
cp -r data-analysis-engine ~/.claude/skills/
```

**Project-scoped (solo un proyecto):**
```
git clone https://github.com/RomiMelfiData/data-analysis-engine.git
cp -r data-analysis-engine .claude/skills/
```

### Uso

Simplemente decile a Claude Code:
> "quiero analizar un dataset"

La skill se activa automáticamente y arranca desde E1.

### Dependencias Python

```
pandas, scipy, statsmodels, scikit-learn, imbalanced-learn
```

### Reportar errores y sugerencias

Si encontrás un error o tenés sugerencias de mejora, abrí un **Issue** en este repositorio:
👉 https://github.com/RomiMelfiData/data-analysis-engine/issues

Toda contribución es bienvenida.

### Licencia

Este proyecto está licenciado bajo **Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)**.

Esto significa que podés:
- ✅ Usar y compartir la skill libremente
- ✅ Dar crédito a la autora (Romina Melfi)

No podés:
- ❌ Modificar o adaptar los archivos
- ❌ Usar la skill con fines comerciales sin permiso

© 2026 Romina Melfi — [Ver licencia completa](https://creativecommons.org/licenses/by-nc-nd/4.0/)

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

Clone the repository and copy the folder to your skills directory:

**Personal (all projects):**
```
git clone https://github.com/RomiMelfiData/data-analysis-engine.git
cp -r data-analysis-engine ~/.claude/skills/
```

**Project-scoped (one project only):**
```
git clone https://github.com/RomiMelfiData/data-analysis-engine.git
cp -r data-analysis-engine .claude/skills/
```

### Usage

Simply tell Claude Code:
> "I want to analyze a dataset"

The skill activates automatically and starts from E1.

### Python dependencies

```
pandas, scipy, statsmodels, scikit-learn, imbalanced-learn
```

### Bug reports and suggestions

If you find a bug or have suggestions for improvement, please open an **Issue** in this repository:
👉 https://github.com/RomiMelfiData/data-analysis-engine/issues

All feedback is welcome.

### License

This project is licensed under **Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)**.

You are free to:
- ✅ Use and share the skill freely
- ✅ Give credit to the author (Romina Melfi)

You may not:
- ❌ Modify or adapt the files
- ❌ Use the skill for commercial purposes without permission

© 2026 Romina Melfi — [View full license](https://creativecommons.org/licenses/by-nc-nd/4.0/)
