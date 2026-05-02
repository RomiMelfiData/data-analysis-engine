# MANUAL DE OPERACIONES: ÁRBOL DE DECISIONES DE DATA ANALYSIS

---

## LEYENDA Y CONVENCIONES

| Símbolo | Significado |
|---------|-------------|
| `→` | Condiciona / activa |
| `⊕` | Opciones mutuamente excluyentes |
| `⚠` | Advertencia crítica |
| `✋ STOP` | Punto de control: consulta obligatoria |
| `↺` | Punto de retorno |
| `PCPiv` | Punto de Control de Pivotaje |
| `H₀/H₁` | Hipótesis nula / alternativa |
| `EDA` | Exploratory Data Analysis |

---

## E1 — DUALIDAD DE INICIO

### 1.1 Selección de modo

| Criterio | MODO EDA | MODO HIPÓTESIS |
|----------|----------|----------------|
| Estado del conocimiento | Sin teoría previa | Relación o efecto conocido |
| Pregunta central | ¿Qué hay en los datos? | ¿Confirmamos X? |
| Objetivo inmediato | Descubrimiento, generación de H | Validación/refutación de H₀ |
| Riesgo de entrada | Cherrypicking post-hoc | Confirmatory bias |
| Salida esperada | Hipótesis candidatas, patrones | Decisión binaria H₀/H₁ |
| Corrección de α | Bonferroni/BH obligatoria si > 1 test | α fijo pre-especificado |

### 1.2 Activación de modo

- **MODO EDA activo si**: sin H formal + sin α fijado a priori + contexto exploratorio
- **MODO HIPÓTESIS activo si**: H₀ y H₁ especificadas antes de ver datos + α fijado a priori + n calculado con potencia ≥ 0.80
- **Modo mixto (transición)**: EDA genera H → registrar → completar E3 → pasar a HIPÓTESIS ajustando α por multiplicidad

### 1.3 Árbol de bifurcaciones — E1

```
INICIO E1
├── ¿Existe teoría previa que especifique la relación?
│   ├── SÍ → ¿Se fijó α antes de cargar datos?
│   │         ├── SÍ → MODO HIPÓTESIS → E2
│   │         └── NO → Consultar stakeholder → no proceder sin consenso
│   └── NO → MODO EDA → E2
├── ¿El objetivo final es...?
│   ├── Describir → MODO EDA (E3 es terminal)
│   ├── Inferir → MODO HIPÓTESIS (E5 es terminal)
│   └── Predecir → MODO EDA + E6 como objetivo principal
└── ¿Los datos son experimentales u observacionales?
    ├── Experimentales → causalidad posible en E5/E6
    └── Observacionales → solo asociación; advertencia en E5/E6/E8
```

### 1.4 Contraste bibliográfico — E1

| Opción | Referencia | Argumento técnico central |
|--------|-----------|--------------------------|
| EDA primero siempre | [Python para Análisis de Datos] | La comprensión de distribuciones y outliers es prerrequisito de cualquier test válido |
| H₀ a priori obligatoria | [Probabilidad y estadística - Devore] | Fijar H₀, α y potencia antes de ver datos garantiza validez frecuentista; p-value requiere pre-especificación |
| Enfoque bayesiano desde inicio | [Introduction to Probability - Blitzstein] | Prior + datos = posterior; no requiere distinción confirmatoria/exploratoria en sentido estricto |
| EDA + confirmatorio iterativo | [Learning Statistics with R] | Ciclos exploración-confirmación con corrección de multiplicidad en cada ciclo |
| Diseño precede al análisis | [Estadística aplicada básica - Moore] | El diseño del estudio determina qué inferencias son válidas; sin diseño adecuado E5 es inválido |

### 1.5 Matriz de consecuencias — E1

| Decisión | Impacto directo | Condiciona E5/E6 |
|----------|----------------|-----------------|
| MODO EDA correcto | Análisis libre de bias confirmatorio | E5: ajustar α por comparaciones múltiples; E6: modelos exploratorios, no confirmatorios |
| MODO HIPÓTESIS correcto | Disciplina inferencial estricta | E5: un test por H registrada; E6: modelo pre-especificado, no ajustado post-hoc |
| Modo mixto con registro explícito | Trazabilidad de decisiones | E5: válido si α ajustado; E6: features pre-especificados |
| HARKing (H post-hoc como a priori) | Tasa de error tipo I descontrolada | E5 inválido; E6: sobrefit conceptual; análisis irrepetible |

### 1.6 Filtro ética/sesgos — E1

- ⚠ **HARKing** (Hypothesizing After Results Known): presentar H post-hoc como a priori → [Estadística aplicada básica - Moore]: invalida todo el NHST
- ⚠ **Sesgo de confirmación**: formular H₀ para facilitar su rechazo (H₀ demasiado débil)
- ⚠ **Selección de muestra post-visualización**: sesgo de supervivencia en el dataset
- ↺ Si se detecta que H₀ fue formulada después de ver datos → reclasificar como EDA + registrar; nunca continuar como confirmatorio

### ✋ STOP E1

**Consulta multicriterio:**
1. ¿Existe teoría previa que especifique la relación a testear? (Sí/No)
2. ¿Se fijó α antes de cargar los datos? (Sí/No)
3. ¿El objetivo final es descriptivo, inferencial o predictivo?
4. ¿Los datos son observacionales o experimentales?

**Recomendación técnica síntesis**: Si 1=No y 2=No → MODO EDA obligatorio. Si 1=Sí y 2=Sí → MODO HIPÓTESIS. Si 1=Sí y 2=No → consultar stakeholder antes de proceder. [Probabilidad y estadística - Devore] + [Estadística aplicada básica - Moore]: la validez inferencial depende de la pre-especificación del modo.

---

## REGLA TRANSVERSAL: PIVOTAJE BIDIRECCIONAL CON PERSISTENCIA

### PT.1 Condiciones de activación del PCPiv

Cualquiera es suficiente:

- E3 refuta H₀ de E1 (dirección opuesta o tamaño de efecto trivial)
- EDA detecta estructura no anticipada en H₀ (multimodalidad, subgrupos, no-linealidad severa)
- Datos insuficientes para test confirmatorio con potencia ≥ 0.80
- Hallazgo en E3/E4 cambia la variable respuesta relevante

### PT.2 Protocolo PCPiv

```
ACTIVAR PCPiv:
1. Documentar estado actual: etapa, hallazgos confirmados, tests ejecutados
2. Reutilizar resultados previos → vincular por referencia (PROHIBIDO recalcular)
3. Determinar nuevo encuadre:
   a. EDA → HIPÓTESIS (hallazgo E3 específico para H₀ formal):
      → Sí: registrar H₀_nueva, ajustar α_ajustado = α/k (k comparaciones)
      → No: continuar EDA, registrar como exploratorio
   b. HIPÓTESIS → EDA (refutación o n insuficiente):
      → Refutación: preservar resultado (p-value, IC) como hallazgo EDA; retomar E3
      → Insuficiente: calcular n para potencia=0.80; si inviable → continuar EDA
4. Justificar bibliográficamente la transición (ver PT.4)
```

### PT.3 Regla de no recálculo

- Prohibido recalcular estadísticos descriptivos de E3 con nueva mirada confirmatoria
- Vincular: "Ver E3.b — distribución de X" en lugar de re-ejecutar con sesgo de selección
- Si nuevos datos llegan post-PCPiv → ejecutar E3 incremental solo sobre datos nuevos

### PT.4 Condicionamiento sobre E5/E6

| Transición | Impacto E5 | Impacto E6 |
|-----------|-----------|-----------|
| EDA→HIPÓTESIS tras E3 | α_ajustado = α/k; variables candidatas = hallazgos E3 | Modelo pre-especificado; sin tuning post-hoc de features |
| HIPÓTESIS→EDA por refutación | Liberar pre-especificación; E5 como generador de H | E6 exploratorio; cross-val obligatoria; no usar test set |
| HIPÓTESIS→EDA por n insuficiente | Suspender inferencial formal; usar IC bootstrap | E6 con regularización fuerte; reportar incertidumbre en E8 |

**Justificación bibliográfica**: [An Introduction to Statistical Learning]: distinción inferencia/predicción determina estrategia de modelado. [Análisis Econométrico - Greene]: pre-especificación requerida para modelos estructurales. [Learning Statistics with R]: análisis exploratorio con control de multiplicidad.

---

## E2 — ADQUISICIÓN Y CARGA DE DATOS

### 2.1 Árbol de bifurcaciones

```
INICIO E2
├── Fuente de datos
│   ├── Archivos planos (CSV/TSV/Excel)
│   │   ├── < 1 GB → pandas.read_csv / read_excel
│   │   ├── 1–50 GB → polars / dask
│   │   └── > 50 GB → PySpark / PyArrow
│   ├── Base de datos relacional
│   │   ├── Consulta ad-hoc → sqlalchemy + pandas.read_sql
│   │   ├── Volumen grande → chunk_size o dask-sql
│   │   └── GIS → geopandas + PostGIS
│   ├── API REST
│   │   ├── Paginada → requests + loop con rate limiting
│   │   └── Streaming → kafka-python / websockets
│   └── Big Data distribuido
│       ├── Batch → PySpark + HDFS/S3
│       └── Streaming → Spark Streaming / Flink
├── Validación inicial obligatoria (en orden)
│   ├── shape, dtypes, head(5), info(), describe()
│   ├── Detectar encoding (UTF-8 / Latin-1 / ISO)
│   ├── Detectar separador y decimal locale
│   └── df.isnull().sum() → mapa de nulos por columna
└── Decisión de continuar
    ├── Datos suficientes para objetivo → E3
    ├── Datos insuficientes → PCPiv + análisis de potencia
    └── Problemas estructurales graves → E4 antes de E3
```

### 2.2 Contraste bibliográfico — E2

| Opción | Referencia | Argumento técnico central |
|--------|-----------|--------------------------|
| pandas primero | [Python para Análisis de Datos] | API consistente; integración directa statsmodels/sklearn; suficiente para < 1 GB |
| polars para volumen medio | [Python para Análisis de Datos 3ra ed.] | 5–10× más rápido que pandas por backend Rust/Arrow; lazy evaluation |
| PySpark para > 50 GB | [Machine Learning Engineering in Action] | Procesamiento distribuido; evita memory overflow en nodo único |
| Validación de schema a priori | [Fundamentos de bases de datos - Silberschatz] | Integridad referencial y tipos deben verificarse antes del análisis; errores silenciosos |

### 2.3 Matriz de consecuencias — E2

| Decisión | Impacto directo | Condiciona E3/E4 |
|----------|----------------|-----------------|
| pandas con datos > 1 GB | MemoryError en análisis iterativos | E3 requiere muestreo o chunking; E4 pipeline processing |
| Carga sin validar encoding | Caracteres corruptos silenciosos | E4 limpieza de texto costosa; E3 estadísticos inválidos |
| Skip validación inicial | Columnas con tipo incorrecto a E3 | E3 estadísticos engañosos; E5 tests sobre tipos incorrectos |
| Datos con PII sin anonimizar | Riesgo legal/ético | E3/E6 potencialmente ilegales; requiere detener pipeline |

### 2.4 Filtro ética/sesgos — E2

- ⚠ **Sesgo de acceso**: datos disponibles ≠ datos representativos ([Estadística aplicada básica - Moore]: population vs. sample)
- ⚠ **Temporal**: datos históricos con cambios de definición silenciosos → revisar metadatos de la fuente
- ⚠ **PII/GDPR**: datos personales requieren anonimización antes de E3
- ↺ Si fuente es incompleta → reportar a stakeholder + documentar antes de continuar; nunca proceder en silencio

### ✋ STOP E2

**Consulta multicriterio:**
1. ¿El esquema de datos coincide con la descripción del problema de E1?
2. ¿El volumen es manejable con la herramienta seleccionada?
3. ¿Existen datos personales que requieren tratamiento especial?

**Recomendación técnica**: Inconsistencia con E1 → PCPiv antes de E3. [Big Data - Joyanes]: la calidad del dato de entrada es el principal determinante de la calidad del análisis; ninguna técnica de modelado compensa datos mal adquiridos.

---

## E3 — EXPLORACIÓN Y ESTADÍSTICA DESCRIPTIVA

### 3.1 Árbol de bifurcaciones

```
INICIO E3
├── Variable continua
│   ├── Distribución → histograma + KDE + QQ-plot
│   ├── Resumen → mean, median, std, IQR, skewness, kurtosis
│   ├── Outliers univariados
│   │   ├── |skew| < 1 (aprox. normal) → Z-score: |z| > 3
│   │   └── |skew| ≥ 1 → IQR-fence: Q1−1.5·IQR / Q3+1.5·IQR
│   └── Normalidad
│       ├── n < 50 → Shapiro-Wilk
│       ├── n ≥ 50 → Anderson-Darling + QQ-plot
│       └── |skew| > 1 o |kurt| > 3 → transformación candidata (log, Box-Cox)
├── Variable categórica
│   ├── value_counts() + proporciones + IC para proporción
│   └── Desequilibrio < 10% clase minoritaria → advertencia para E6
├── Variable ordinal
│   ├── Frecuencias como categórica
│   └── Spearman/Kendall para correlación (NO Pearson)
├── Variable temporal
│   ├── Descomposición STL: tendencia + estacionalidad + ruido
│   └── ACF/PACF → diagnóstico antes de E5/E6
├── Análisis bivariado
│   ├── Continua–Continua → scatter + Pearson r + Spearman ρ
│   │   └── r ≠ ρ significativamente → no-linealidad o influencia de outliers
│   ├── Continua–Categórica → boxplot por grupo + Cohen's d preliminar
│   ├── Categórica–Categórica → tabla contingencia + V de Cramér
│   └── Muchas variables → heatmap + pairplot (n_vars < 20)
├── Outliers multivariados
│   ├── n < 1000 → Mahalanobis distance
│   ├── n ≥ 1000 → Isolation Forest / LOF
│   └── DECISIÓN: outlier ≠ error necesariamente → documentar; no eliminar aún (→ E4)
├── MODO EDA activo → documentar hallazgos como candidatos a H
└── MODO HIPÓTESIS activo → NO generar nuevas H; solo verificar supuestos del test
```

### 3.2 Bifurcación crítica: EDA detecta estructura → activar PCPiv

Condiciones:

- **Bimodalidad** → posible mezcla de poblaciones → subgrupos no anticipados
- **Correlación fuerte no contemplada** en E1 → variable confusora potencial
- **|skew| > 2** → transformación necesaria antes de E5
- **Nulos > 20% en variable clave** → decisión de imputación/eliminación antes de continuar

**Protocolo**: activar PCPiv (PT.1) → documentar hallazgo → determinar si reformula H₀ de E1

### 3.3 Contraste bibliográfico — E3

| Opción | Referencia | Argumento técnico central |
|--------|-----------|--------------------------|
| IQR para outliers | [Estadística - Triola] | Robusto a distribuciones no normales; no asume simetría; universalmente aplicable |
| Z-score para outliers | [Probabilidad y estadística - Devore] | Válido solo bajo aprox. normalidad; sensible a los propios outliers en la media |
| Isolation Forest multivariado | [The Elements of Statistical Learning] | Detecta anomalías en alta dimensión que métodos univariados no capturan |
| LOF (Local Outlier Factor) | [The Elements of Statistical Learning] | Identifica outliers en densidad local; útil con clústeres de diferente densidad |
| Pearson para correlación | [Estadística matemática con aplicaciones - Wackerly] | Linealidad + normalidad bivariada requeridas para inferencia válida del coeficiente |
| Spearman para correlación | [Estadística aplicada - De la Horra] | Monotonía sin asumir normalidad; robusto a outliers; válido para ordinales |

> **Contradicción entre fuentes**: [Estadística - Triola] prefiere IQR universalmente; [Probabilidad y estadística - Devore] prefiere Z-score bajo normalidad → **no resolver**; decisión condicional a verificación previa de normalidad.

### 3.4 Matriz de consecuencias — E3

| Decisión | Impacto directo | Condiciona E4/E5/E6 |
|----------|----------------|---------------------|
| Outlier → error → eliminar | Muestra reducida | E5: menor potencia; E6: training set más limpio pero posiblemente sesgado |
| Outlier → legítimo → conservar | Distribución con colas pesadas | E4: puede requerir transformación; E5: tests no paramétricos; E6: modelos robustos |
| Transformación log en E3 | Distribución más simétrica | E5: tests paramétricos válidos; E6: coeficientes en escala log (interpretar con cuidado) |
| No transformar | Asimetría persiste | E5: tests no paramétricos obligatorios; E6: modelos tolerantes a no-normalidad |
| Ignorar correlaciones altas entre predictores | Multicolinealidad no detectada | E6: coeficientes MCO inestables (VIF >> 5); E5: CIs inflados |

### 3.5 Filtro ética/sesgos — E3

- ⚠ **Sesgo de medición**: variable proxy ≠ constructo real ([Didáctica de la Estadística - Batanero])
- ⚠ **Cherry-picking visual**: reportar TODAS las visualizaciones, no solo las "interesantes"
- ⚠ **Correlación ≠ causalidad**: nunca interpretar r como evidencia causal en E3
- ↺ Si distribución de variable respuesta sugiere sesgo de selección → documentar y evaluar usabilidad del dataset

### ✋ STOP E3

**Consulta multicriterio:**
1. ¿Se documentaron todos los outliers y su clasificación (error / legítimo / dudoso)?
2. ¿Se verificó normalidad de las variables que entrarán a E5?
3. ¿Hay variables con > 20% de nulos que requieren decisión de imputación en E4?
4. ¿Surgieron hipótesis en EDA que requieren activar PCPiv?

**Recomendación técnica**: No avanzar a E4 sin responder 1–3. Si 4=Sí → PCPiv activo antes de E4. [Python para Análisis de Datos]: la documentación de E3 es el contrato de calidad que valida todos los análisis posteriores.

---

## E4 — LIMPIEZA Y PREPROCESAMIENTO

### 4.1 Árbol de bifurcaciones

```
INICIO E4
├── Valores faltantes (diagnóstico de mecanismo desde E3)
│   ├── MCAR (Missing Completely At Random)
│   │   ├── % nulos < 5% → eliminación de filas
│   │   └── % nulos 5–30% → imputar (media si simétrico; mediana si asimétrico)
│   ├── MAR (Missing At Random)
│   │   ├── Imputación múltiple (IterativeImputer / MICE) → estándar
│   │   └── NO usar media/moda directa si MAR
│   └── MNAR (Missing Not At Random)
│       ├── Crear indicador binario de ausencia
│       ├── Modelar mecanismo de falta
│       └── Consultar con experto de dominio
│   └── ⚑ RAMA ANÁLISIS DE SENSIBILIDAD (→ 4.2)
├── Outliers (decisión de E3)
│   ├── Error confirmado → eliminar o corregir con fuente original
│   ├── Legítimo → conservar + documentar
│   └── Dudoso → análisis de sensibilidad: ejecutar E5/E6 con y sin outlier
├── Codificación de categóricas
│   ├── Nominal k ≤ 5 → One-Hot Encoding (OHE)
│   ├── Nominal k > 15 → Target Encoding o Embedding (E6 NN)
│   ├── Nominal k 5–15 → OHE o Target según cardinalidad y modelo
│   └── Ordinal → Ordinal Encoding con mapeo explícito y justificado
├── Escalado (condicional al modelo de E6)
│   ├── SVM, KNN, PCA, Redes Neuronales → StandardScaler o MinMaxScaler
│   ├── Árboles, RF, GBM, XGBoost → NO escalar (invariante a transformaciones monotónicas)
│   └── ⚠ fit() SOLO en train; transform() en train y test
├── Feature engineering
│   ├── Interacciones si teoría (E1/E5) lo sugiere
│   ├── Polinomios si E3 muestra relación curvilínea
│   └── Temporales: lag, rolling window, diferenciación
└── División de datos
    ├── No temporal → train/val/test (70/15/15 o 60/20/20); random_state fijo
    ├── Temporal → split cronológico, NO aleatorio (TimeSeriesSplit)
    └── Desbalanceado → stratify=True + SMOTE solo en train
```

### 4.2 RAMA: ANÁLISIS DE SENSIBILIDAD POR ESTRATEGIA DE IMPUTACIÓN

**Criterios de decisión por mecanismo y porcentaje:**

| Mecanismo | % nulos | Estrategia A | Estrategia B | Criterio de elección |
|-----------|---------|-------------|-------------|---------------------|
| MCAR | < 5% | Eliminación de casos | Mediana | Si n suficiente → eliminar; si n pequeño → mediana |
| MCAR | 5–30% | Media (si simétrico) | KNN Imputer | Comparar distribución post-imputación vs. original |
| MAR | cualquiera | MICE/IterativeImputer | Modelo predictivo de imputación | MICE si variables correlacionadas; modelo propio si predictores fuertes disponibles |
| MNAR | cualquiera | Indicador binario + imputación | Análisis de casos completos | Comparar E5/E6 con ambas → reportar como sensibilidad |

**Protocolo de sensibilidad:**

1. Ejecutar E5/E6 con Estrategia A → guardar resultados (coeficientes, p-values, métricas)
2. Ejecutar E5/E6 con Estrategia B → guardar resultados
3. Calcular diferencia relativa en parámetros clave: Δ = |A − B| / |A|
4. **Δ > 10%**: reportar ambos análisis; conclusión es sensible a la estrategia → no colapsar
5. **Δ ≤ 10%**: elegir la más simple; documentar robustez; mencionar en E8

**Condicionamiento sobre E5/E6:**

- Alta sensibilidad (Δ > 10%) → E5: reportar IC bajo ambas estrategias; E6: usar regularización fuerte o ensemble
- Baja sensibilidad (Δ ≤ 10%) → E5/E6: continuar con estrategia elegida + mención en limitaciones de E8

### 4.3 Contraste bibliográfico — E4

| Opción | Referencia | Argumento técnico central |
|--------|-----------|--------------------------|
| Eliminación listwise | [Estadística matemática con aplicaciones - Wackerly] | Insesgado bajo MCAR; introduce sesgo sistemático bajo MAR/MNAR |
| Imputación por media | [Estadística - Triola] | Simple; reduce varianza artificial; inapropiado si distribución asimétrica o nulos > 5% |
| MICE / Imputación múltiple | [Análisis Econométrico - Greene] | Preserva incertidumbre de imputación; gold standard bajo MAR; computacionalmente costoso |
| KNN Imputer | [Hands-On Machine Learning] | Usa información contextual de vecinos; sensible a escala previa (requiere fit antes) |
| Target Encoding | [The Elements of Statistical Learning] | Reduce cardinalidad; riesgo de leakage si no se aplica dentro de CV folds |
| OHE estándar | [An Introduction to Statistical Learning] | Estándar para nominal; crea alta dimensionalidad si k grande → regularización en E6 |

> **Contradicción entre fuentes**: [Análisis Econométrico - Greene] defiende MICE como estándar; [Hands-On Machine Learning] acepta imputación simple si % nulos es bajo → **no resolver**; decisión condicional a mecanismo y porcentaje de nulos.

### 4.4 Matriz de consecuencias — E4

| Decisión | Impacto directo | Condiciona E5/E6 |
|----------|----------------|-----------------|
| Eliminación con MNAR | Muestra con sesgo sistemático | E5: estimadores sesgados; E6: modelo entrenado en subpoblación no representativa |
| Media en distribución asimétrica | Subestima varianza real | E5: ICs demasiado estrechos; sobreconfianza en precisión |
| OHE con k grande | Dimensionalidad explota | E6: regularización necesaria; E5: problemas de grados de libertad |
| fit() scaler en test | Data leakage → métricas optimistas | E7: métricas de E6 no reproducibles en producción; análisis invalido |
| Sin feature engineering | Relaciones no lineales no capturadas | E6: MCO subestima efecto; modelos de árbol pueden capturar donde MCO no |

### 4.5 Filtro ética/sesgos — E4

- ⚠ **Leakage**: fit del imputador/scaler en test contamina E7 ([Hands-On Machine Learning])
- ⚠ **MNAR sin modelar**: imputar sin entender el mecanismo distorsiona la distribución real ([Inferencia Estadística])
- ⚠ **Target encoding sin CV**: información del target filtra al modelo → métricas irreproducibles
- ⚠ **Eliminar outliers de subgrupos minoritarios** → sesgo discriminatorio en E6
- ↺ Si se detecta leakage post-E4 → retrogradar a E4; reconstruir pipeline completo; ningún resultado de E5/E6 es válido

### ✋ STOP E4

**Consulta multicriterio:**
1. ¿Se ejecutó análisis de sensibilidad de imputación si % nulos > 5%?
2. ¿El scaler/imputador fue ajustado ÚNICAMENTE en datos de entrenamiento?
3. ¿Existe riesgo de data leakage en el pipeline de preprocesamiento?
4. ¿Los datos de test permanecen intactos y no influenciaron ninguna decisión?

**Recomendación técnica**: Si cualquier respuesta = No → detener y corregir antes de E5/E6. [Hands-On Machine Learning] + [An Introduction to Statistical Learning]: construir sklearn.Pipeline garantiza que la transformación se aplique correctamente en CV y evita leakage por diseño.

---

## E5 — ANÁLISIS INFERENCIAL

### 5.1 Árbol de bifurcaciones

```
INICIO E5
├── MODO activo (de E1 + PCPiv)
│   ├── MODO HIPÓTESIS → usar 5.2; α pre-fijado; un test por H registrada
│   └── MODO EDA → usar 5.2 como generación de H; ajustar α por multiplicidad
├── Verificación de supuestos ANTES del test
│   ├── Normalidad (si test paramétrico)
│   │   ├── n < 50 → Shapiro-Wilk (W)
│   │   ├── n ≥ 50 → Anderson-Darling + QQ-plot visual
│   │   └── |skew| > 1 o |kurt| > 3 → considerar no paramétrico
│   ├── Homocedasticidad (si comparación de grupos)
│   │   ├── Levene → robusto a no-normalidad (preferido)
│   │   └── Bartlett → solo si normalidad confirmada
│   └── Independencia
│       ├── Diseño experimental → verificar por diseño
│       └── Series temporales → Durbin-Watson (valor ≈ 2 = ok)
└── Árbol de selección de test → 5.2
```

### 5.2 Árbol de selección de test

```
¿Cuántas variables respuesta?
├── 1 variable respuesta
│   ├── Continua
│   │   ├── 1 grupo vs. valor teórico
│   │   │   ├── Normal → t de Student una muestra
│   │   │   └── No normal → Wilcoxon sign-rank
│   │   ├── 2 grupos independientes
│   │   │   ├── Normal + varianzas iguales → t de Student
│   │   │   ├── Normal + varianzas distintas → Welch t-test
│   │   │   └── No normal o n pequeño → Mann-Whitney U
│   │   ├── 2 grupos pareados
│   │   │   ├── Normal → t pareada
│   │   │   └── No normal → Wilcoxon sign-rank pareado
│   │   ├── ≥ 3 grupos independientes
│   │   │   ├── Normal + homocedástico → ANOVA + Tukey/Bonferroni post-hoc
│   │   │   └── No normal o heterocedástico → Kruskal-Wallis + Dunn post-hoc
│   │   └── ≥ 3 grupos medidas repetidas
│   │       ├── Normal → ANOVA medidas repetidas + Mauchly (esfericidad)
│   │       └── No normal → Friedman + Wilcoxon pareado post-hoc
│   └── Categórica (frecuencias)
│       ├── 2 variables → Chi-cuadrado (esperados ≥ 5) / Fisher exacto (< 5)
│       └── Bondad de ajuste → Chi-cuadrado vs. distribución teórica
├── Relación entre variables continuas
│   ├── Lineal → Pearson r (con test H₀: ρ = 0)
│   ├── Monotónica → Spearman ρ
│   └── Regresión → F-test global + t-tests de coeficientes
├── Tiempo al evento → Log-rank test / Cox proportional hazards
└── Múltiples outcomes → MANOVA (verificar Box's M para covarianza)
```

### 5.3 Tamaño del efecto — obligatorio siempre

| Test | Medida de efecto | Interpretación (Cohen) |
|------|-----------------|------------------------|
| t de Student | d de Cohen | 0.2 = pequeño; 0.5 = medio; 0.8 = grande |
| ANOVA | η² (eta cuadrado) | 0.01 = pequeño; 0.06 = medio; 0.14 = grande |
| Chi-cuadrado | V de Cramér | 0.1 = pequeño; 0.3 = medio; 0.5 = grande |
| Pearson/Spearman | r | 0.1 = pequeño; 0.3 = medio; 0.5 = grande |
| Regresión | R², R² ajustado | Proporción de varianza explicada |
| No paramétrico | r = Z/√n | Misma escala que Pearson r |

### 5.4 Bifurcación crítica: si H₀ no refutada pero dudosa

- **H₀ no refutada + ICs amplios** → potencia insuficiente → calcular n para β = 0.80; si inviable → PCPiv → EDA
- **H₀ refutada pero efecto trivial** → significancia estadística sin relevancia práctica → registrar como EDA; no usar para E6 confirmatorio
- **Resultado contradictorio con literatura** → no resolver; documentar ambas versiones como opciones distintas

### 5.5 Contraste bibliográfico — E5

| Opción | Referencia | Argumento técnico central |
|--------|-----------|--------------------------|
| NHST clásico α = 0.05 | [Probabilidad y estadística - Devore] | Marco frecuentista estándar; α pre-especificado; p-value ≠ P(H₀ verdadera) |
| IC en lugar de p-values | [Estadística aplicada básica - Moore] | IC reporta magnitud e incertidumbre; p-value binario es insuficiente para decisión |
| Bayesiano (factor Bayes) | [Introduction to Probability - Blitzstein] | Cuantifica evidencia a favor de H₀ y H₁ explícitamente; requiere prior informativo |
| Tamaño de efecto como métrica principal | [Learning Statistics with R] | Significancia estadística depende de n; tamaño del efecto es independiente de n |
| IC bootstrap | [The Elements of Statistical Learning] | No paramétrico; válido sin supuesto distribucional; recomendado para estimadores complejos |

> **Contradicción entre fuentes**: [Probabilidad y estadística - Devore] defiende NHST clásico; [Estadística aplicada básica - Moore] y [Learning Statistics with R] priorizan IC y efecto → **no resolver**; en contexto regulatorio usar NHST; en investigación exploratoria preferir IC + efecto.

### 5.6 Matriz de consecuencias — E5

| Decisión | Impacto directo | Condiciona E6/E7 |
|----------|----------------|-----------------|
| Test paramétrico con supuestos violados | Tasa de error tipo I inflada | E6: coeficientes MCO + CIs inválidos |
| No reportar tamaño de efecto | Significancia sin relevancia práctica | E6: modelo puede sobrefit para capturar efecto trivial |
| Comparaciones múltiples sin corrección | Falsos positivos acumulados (α real > α nominal) | E6: features seleccionados por azar incluidos en modelo |
| NHST con n grande | Todo es significativo; efecto puede ser trivial | E6: incluir variables sin relevancia práctica |
| Bootstrap IC | No requiere supuesto distribucional | E6: puede aplicarse al modelo completo para IC de predicciones |

### 5.7 Filtro ética/sesgos — E5

- ⚠ **p-hacking**: ajustar α después de ver resultados invalida todo el NHST ([Probabilidad y estadística - Devore])
- ⚠ **Múltiples comparaciones sin corrección**: tasa de error tipo I acumulada = 1 − (1 − α)^k
- ⚠ **Reportar solo resultados significativos**: publication bias; reportar TODOS los tests ejecutados
- ⚠ **Causalidad en observacionales**: correlación estadística ≠ relación causal
- ↺ Si se detecta p-hacking en E5 → reclasificar todo el análisis como EDA; ningún resultado es confirmatorio

### ✋ STOP E5

**Consulta multicriterio:**
1. ¿Se verificaron supuestos del test antes de ejecutarlo?
2. ¿Se reportó tamaño de efecto para cada test significativo?
3. ¿Se corrigió α si se ejecutaron > 1 test?
4. ¿Los resultados son consistentes con los hallazgos de E3?

**Recomendación técnica síntesis**: [Estadística aplicada básica - Moore] + [Probabilidad y estadística - Devore]: el rigor de E5 depende de la pre-especificación de E1. Si α no fue fijado a priori → todos los resultados son exploratorios. Reportar siempre IC junto al p-value y el tamaño del efecto.

---

## E6 — MODELADO PREDICTIVO

### 6.1 Árbol de selección de modelo

```
INICIO E6
├── Paso obligatorio: baseline simple primero
│   ├── Regresión → DummyRegressor (media de y_train)
│   └── Clasificación → DummyClassifier (clase mayoritaria)
├── Supervisado
│   ├── Respuesta continua → REGRESIÓN
│   │   ├── Relación lineal (E3) + supuestos Gauss-Markov → OLS (statsmodels)
│   │   │   ├── VIF < 5 para todos los predictores → continuar
│   │   │   └── VIF ≥ 5 → Ridge / Lasso / eliminar variable
│   │   ├── No lineal moderada → GAM / polinomios / splines
│   │   ├── Alta dimensionalidad (p >> n) → Lasso / ElasticNet
│   │   ├── Interacciones complejas + n > 1000 → Random Forest / GBM
│   │   └── n > 10K + features no estructuradas → Deep Learning
│   └── Respuesta categórica → CLASIFICACIÓN
│       ├── 2 clases + interpretabilidad → Regresión Logística
│       ├── 2 clases + no lineal + n > 500 → SVM / Random Forest
│       ├── Multi-clase → OvR Logística / Random Forest / XGBoost
│       └── Desbalance severo (< 10% clase minoritaria)
│           → SMOTE en train + métricas F1/AUC-PR (no accuracy)
├── No supervisado
│   ├── Agrupamiento
│   │   ├── Grupos esféricos + k conocido → K-Means (Elbow + Silhouette)
│   │   ├── Grupos irregulares / ruido → DBSCAN
│   │   └── Exploración jerárquica → Hierarchical clustering + dendrograma
│   └── Reducción de dimensión
│       ├── Lineal + interpretabilidad → PCA
│       ├── No lineal + visualización → UMAP / t-SNE (no para E5)
│       └── Variables latentes → Factor Analysis
└── Series temporales
    ├── Estacionaria → ARIMA (ACF/PACF para órdenes)
    ├── Estacional → SARIMAX
    └── Multi-estacional o holidays → Prophet
```

### 6.2 Supuestos Gauss-Markov para OLS — verificar en orden

| Supuesto | Diagnóstico | Violación → Acción |
|----------|------------|-------------------|
| Linealidad | Residuos vs. fitted: patrón aleatorio | Curvilínea → transformar variable o usar GAM |
| Homocedasticidad | Breusch-Pagan / Goldfeld-Quandt | Heterocedástico → WLS o errores robustos HC3 |
| Independencia | Durbin-Watson (≈ 2 = ok) | Autocorrelación → GLS / Newey-West HAC SE |
| Normalidad residuos | QQ-plot + Shapiro-Wilk | Solo crítico para n pequeño; bootstrap IC si se viola |
| No multicolinealidad | VIF: < 5 ok; 5–10 moderado; > 10 grave | VIF ≥ 5 → Ridge o eliminar variable redundante |

### 6.3 Bifurcación: reencuadre por PCPiv activo al llegar a E6

**Si EDA→HIPÓTESIS:**

- Variable adicional descubierta en EDA → incluir en modelo; comparar AIC/BIC pre vs. post inclusión
- Relación no lineal en E3 → usar GAM o RF en lugar de OLS; documentar
- Subgrupos detectados → modelos estratificados vs. modelo único + interacción; comparar ambos

**Si HIPÓTESIS→EDA por refutación:**

- E6 es exploratorio: usar k-fold CV; no reportar métricas de test set como definitivas
- Documentar como generación de hipótesis para futuro estudio confirmatorio
- Regularización fuerte obligatoria para evitar sobrefit en modo exploratorio

### 6.4 Contraste bibliográfico — E6

| Opción | Referencia | Argumento técnico central |
|--------|-----------|--------------------------|
| OLS como baseline | [Análisis Econométrico - Greene] | BLUE bajo Gauss-Markov; interpretabilidad máxima; coeficientes con significado causal si supuestos se cumplen |
| Random Forest | [The Elements of Statistical Learning] | Reduce varianza sin incrementar sesgo; estable; bias-variance tradeoff favorable en datasets medianos |
| Gradient Boosting / XGBoost | [Hands-On Machine Learning] | Mejor desempeño empírico en tabular data; sensible a hiperparámetros; requiere CV obligatoria |
| Lasso / Regularización | [An Introduction to Statistical Learning] | Selección automática de features; coeficientes esparcidos; útil en alta dimensionalidad (p > n) |
| Deep Learning | [Neural Networks and Deep Learning] | Ventaja en datos no estructurados (imagen, texto) y n > 10K; no interpretable por defecto; alto costo computacional |
| GAM | [The Elements of Statistical Learning] | Preserva interpretabilidad de OLS con flexibilidad para no-linealidades; subestimado en práctica aplicada |

> **Contradicción entre fuentes**: [The Elements of Statistical Learning] prefiere Random Forest sobre boosting por estabilidad; [Hands-On Machine Learning] prefiere XGBoost por performance en tabular data → **no resolver**; depende del tamaño del dataset y tiempo de entrenamiento disponible.

### 6.5 Matriz de consecuencias — E6

| Decisión | Impacto directo | Condiciona E7 |
|----------|----------------|---------------|
| Skip baseline | Sin referencia para evaluar mejora | E7: imposible cuantificar ganancia del modelo complejo |
| No verificar Gauss-Markov | CIs y p-values de coeficientes inválidos | E7: métricas de ajuste engañosas; modelo no interpretable |
| XGBoost sin CV de hiperparámetros | Sobrefit en training | E7: performance en test << performance en train |
| Deep Learning con n < 1000 | Sobrefit severo; convergencia inestable | E7: métricas excelentes en train, malas en test |
| No aplicar SMOTE en desbalance | Modelo trivial (predice clase mayoritaria) | E7: accuracy alta pero F1 clase minoritaria ≈ 0 |

### 6.6 Filtro ética/sesgos — E6

- ⚠ **Proxy discrimination**: variable no protegida correlacionada con variable protegida ([Machine Learning Engineering in Action])
- ⚠ **Feedback loops**: modelo entrenado en decisiones históricas perpetúa sesgos pasados
- ⚠ **Extrapolación**: no predecir fuera del rango de los datos de entrenamiento
- ↺ Si modelo presenta disparidad de desempeño entre grupos demográficos → retornar a E4 para re-examinar features; considerar fairness constraints

### ✋ STOP E6

**Consulta multicriterio:**
1. ¿Se ejecutó el baseline antes del modelo complejo?
2. ¿Se verificaron los supuestos del modelo elegido?
3. ¿El conjunto de test permanece intacto?
4. ¿El modelo es interpretable al nivel requerido por el caso de uso?

**Recomendación técnica síntesis**: [An Introduction to Statistical Learning] + [The Elements of Statistical Learning]: el modelo con mejor interpretabilidad que logre desempeño aceptable debe preferirse al modelo complejo de igual desempeño. La elección entre OLS, Random Forest y XGBoost depende del tamaño del dataset, la necesidad de interpretabilidad y los supuestos verificados en E5.

---

## E7 — VALIDACIÓN Y SELECCIÓN DE MODELOS

### 7.1 Árbol de bifurcaciones

```
INICIO E7
├── Estrategia de evaluación
│   ├── No temporal
│   │   ├── n < 1000 → Leave-One-Out CV (LOOCV)
│   │   ├── n 1000–100K → K-Fold CV (k = 5 o k = 10)
│   │   └── Desbalance → Stratified K-Fold
│   ├── Temporal → TimeSeriesSplit (sin shuffle; respetar orden)
│   └── Jerárquico / agrupado → GroupKFold
├── Selección de métricas
│   ├── Regresión
│   │   ├── Errores grandes críticos → RMSE
│   │   ├── Todos los errores igual peso → MAE
│   │   ├── Escala variable → MAPE (solo si y > 0)
│   │   └── Varianza explicada → R² / R² ajustado
│   └── Clasificación
│       ├── Clases balanceadas → Accuracy + F1 macro
│       ├── Desbalance → F1 weighted + AUC-ROC + AUC-PR
│       └── Umbral crítico → curva Precision-Recall
├── Comparación de modelos
│   ├── Mismo número de parámetros → métrica de test
│   ├── Diferente número de parámetros → AIC / BIC
│   └── Test estadístico de diferencia
│       ├── Clasificación → McNemar
│       └── Series temporales → Diebold-Mariano (DM)
├── Explicabilidad
│   ├── OLS → coeficientes + IC + VIF
│   ├── Árbol único → feature importance + plot del árbol
│   ├── Ensamble → SHAP values (Shapley)
│   └── Caja negra → SHAP (global) + LIME (local)
└── Producción (si aplica)
    ├── Serializar → joblib / ONNX
    ├── Tracking → MLflow
    └── Monitoreo → data drift (Evidently AI) + alertas
```

### 7.2 Contraste bibliográfico — E7

| Opción | Referencia | Argumento técnico central |
|--------|-----------|--------------------------|
| K-Fold CV (k = 10) | [An Introduction to Statistical Learning] | Estima error de generalización con baja varianza; k = 10 empíricamente óptimo para balance sesgo-varianza |
| LOOCV | [The Elements of Statistical Learning] | Estimado casi insesgado; varianza alta; apropiado para n pequeño; computacionalmente costoso |
| AIC para selección | [Análisis Econométrico - Greene] | Penaliza complejidad; asintóticamente equivalente a K-Fold; más rápido de calcular |
| BIC para selección | [Análisis Econométrico - Greene] | Penaliza más fuerte que AIC; selecciona modelos más parsimoniosos; consistente (selecciona modelo verdadero con n → ∞) |
| SHAP values | [Machine Learning Engineering in Action] | Basado en teoría de juegos cooperativos; único valor que satisface eficiencia, simetría, dummy y aditividad |
| LIME (local) | [Hands-On Machine Learning] | Aproximación lineal local; interpretabilidad local; no consistente globalmente; rápido |

> **Contradicción entre fuentes**: [The Elements of Statistical Learning] prefiere LOOCV por menor sesgo; [An Introduction to Statistical Learning] prefiere K-Fold por balance sesgo-varianza → **no resolver**; depende de n disponible y costo computacional.

### 7.3 Matriz de consecuencias — E7

| Decisión | Impacto directo | Condiciona E8 |
|----------|----------------|---------------|
| Test set usado múltiples veces | Métricas optimistas no reproducibles | E8: reportar cifras que no se repetirán en producción |
| K-Fold sin estratificar en desbalance | Folds con distribuciones distintas | E8: performance real diferente a reportada |
| No reportar IC de métricas | Incertidumbre de desempeño desconocida | E8: stakeholder no puede evaluar confiabilidad del modelo |
| SHAP sobre modelo no aditivo sin verificación | Interpretación puede ser engañosa | E8: explicaciones potencialmente incorrectas al usuario final |
| AIC/BIC sin comparar con CV | Puede seleccionar modelo sobrefit en muestra | E8: performance real menor a reportada |

### 7.4 Filtro ética/sesgos — E7

- ⚠ **Métrica única**: accuracy puede ocultar disparidades de desempeño entre grupos → evaluar métricas por subgrupo
- ⚠ **Overfitting del pipeline de selección**: cada decisión de hiperparámetros consume información del conjunto de validación
- ⚠ **Test set sagrado**: usarlo solo al final; cualquier decisión basada en él contamina la evaluación
- ↺ Si métricas de E7 divergen significativamente de E6 (sobrefit visible) → retrogradar a E6; revisar leakage o complejidad del modelo

### ✋ STOP E7

**Consulta multicriterio:**
1. ¿Se usó el test set exactamente una vez?
2. ¿Las métricas reportadas incluyen varianza entre folds o IC?
3. ¿El modelo seleccionado es explicable al nivel requerido?
4. ¿Se evaluaron métricas por subgrupos relevantes (género, región, período temporal)?

**Recomendación técnica síntesis**: [An Introduction to Statistical Learning] + [Machine Learning Engineering in Action]: el modelo en producción debe evaluarse en condiciones que simulen la distribución real de los datos. El test set es el único indicador confiable del desempeño real — usarlo solo al final es el principio de mayor impacto en la confiabilidad del análisis.

---

## E8 — COMUNICACIÓN Y REPORTES

### 8.1 Árbol de bifurcaciones

```
INICIO E8
├── Audiencia
│   ├── Técnica → métricas completas + código reproducible + supuestos
│   ├── Ejecutiva → KPIs + IC + impacto de negocio (sin fórmulas)
│   └── Mixta → resumen ejecutivo + apéndice técnico separado
├── Tipo de salida
│   ├── Informe estático → Jupyter → HTML/PDF (nbconvert)
│   ├── Dashboard interactivo → Streamlit / Dash / Plotly
│   └── Modelo en producción → FastAPI + MLflow + monitoreo (Evidently AI)
├── Selección de gráfico según mensaje
│   ├── Distribución continua → histograma + KDE
│   ├── Comparación de grupos → boxplot / violin plot
│   ├── Relación continua-continua → scatter + línea de tendencia + IC
│   ├── Serie temporal → línea con banda de confianza
│   ├── Proporciones (≤ 3 categorías) → pie chart
│   ├── Proporciones (> 3 categorías) → bar chart horizontal
│   └── Correlación matricial → heatmap con valores numéricos
└── Requisitos mínimos de reporte (no negociables)
    ├── n de muestra visible en cada estimación
    ├── IC o error estándar en todas las estimaciones puntuales
    ├── Supuestos violados documentados explícitamente
    ├── Limitaciones del análisis en sección dedicada
    └── Distinción explícita: hallazgos confirmatorios vs. exploratorios
```

### 8.2 Contraste bibliográfico — E8

| Opción | Referencia | Argumento técnico central |
|--------|-----------|--------------------------|
| Reproducibilidad via Jupyter | [Python para Análisis de Datos] | Código + resultado en mismo documento; permite auditoría, replicación y actualización |
| Dashboard con Streamlit/Dash | [Machine Learning Engineering in Action] | Monitoreo continuo; detecta data drift antes de fallo silencioso en producción |
| Tabla antes de gráfico | [Estadística aplicada básica - Moore] | La tabla permite verificar números exactos; el gráfico comunica tendencia — ambos necesarios |
| Reportar siempre IC | [Probabilidad y estadística - Devore] | Un resultado sin IC no permite evaluar precisión; estimación puntual sola es insuficiente |
| Documentar supuestos en el reporte | [Estadística matemática con aplicaciones - Wackerly] | El lector debe poder evaluar la validez de las conclusiones con la información del reporte |

### 8.3 Matriz de consecuencias — E8

| Decisión | Impacto directo | Riesgo final |
|----------|----------------|-------------|
| Truncar eje Y | Amplifica diferencias visualmente | Conclusiones erróneas del stakeholder ([Didáctica de la Estadística - Batanero]) |
| Omitir n en gráfico | Audiencia no puede evaluar representatividad | Sobreconfianza en resultados de muestra pequeña |
| Sin sección de limitaciones | Análisis parece más concluyente de lo que es | Decisiones basadas en certeza falsa |
| Modelo sin monitoreo en producción | Data drift no detectado | Predicciones degradadas silenciosamente sin alerta |
| Presentar resultado EDA como confirmatorio | Conclusión más fuerte de lo que los datos soportan | Mala decisión de negocio basada en análisis exploratorio |

### 8.4 Filtro ética/sesgos — E8

- ⚠ **Gráficos engañosos**: truncar eje Y, escala logarítmica sin aviso, áreas vs. alturas ([Didáctica de la Estadística - Batanero])
- ⚠ **Omitir outliers del gráfico** sin mencionar → oculta información relevante para el lector
- ⚠ **Omitir limitaciones**: un reporte sin sección de limitaciones es un reporte incompleto
- ↺ Si stakeholder interpreta resultado EDA como causal → corregir explícitamente en el reporte; documentar alcance

### ✋ STOP E8

**Consulta multicriterio:**
1. ¿El reporte distingue explícitamente hallazgos confirmatorios de exploratorios?
2. ¿Cada estimación incluye IC o error estándar?
3. ¿Las limitaciones y supuestos están documentados en sección dedicada?
4. ¿Si el modelo está en producción, hay sistema de monitoreo activo con alertas?

**Recomendación técnica síntesis**: [Machine Learning Engineering in Action] + [Python para Análisis de Datos]: la comunicación es el último paso del pipeline pero no el menos riguroso. Un análisis correcto mal comunicado genera las mismas malas decisiones que un análisis incorrecto. La reproducibilidad garantiza que el análisis pueda ser auditado, actualizado y repetido.

---

## TABLA MAESTRA: ÍNDICE DE DECISIONES CRÍTICAS

| Etapa | Decisión crítica | Bifurcación clave | Activa PCPiv | Fuentes con contradicción |
|-------|-----------------|------------------|-------------|--------------------------|
| E1 | Modo EDA vs. Hipótesis | H₀ a priori vs. post-hoc | Si HARKing detectado | [Devore] vs. [Moore] vs. [Blitzstein] |
| E2 | Herramienta por volumen | pandas / polars / PySpark | Datos insuficientes para objetivo E1 | [McKinney] vs. [ML Engineering] |
| E3 | Método de outlier | IQR vs. Z-score vs. Isolation Forest | EDA detecta estructura no anticipada | [Triola] vs. [Devore] vs. [ESL] |
| E4 | Estrategia de imputación | MCAR/MAR/MNAR → método | Sensibilidad Δ > 10% | [Wackerly] vs. [Greene] vs. [Hands-On ML] |
| E5 | Tipo de test | Paramétrico vs. no param. vs. bayesiano | H₀ refutada o n insuficiente | [Devore] vs. [Moore] vs. [Blitzstein] |
| E6 | Tipo de modelo | OLS / regularización / ensamble / DL | EDA→H detecta no-linealidad o subgrupos | [ESL] vs. [Hands-On ML] vs. [Greene] |
| E7 | Estrategia de CV | K-Fold / LOOCV / TimeSeriesSplit | Métricas E7 divergen significativamente de E6 | [ISLR] vs. [ESL] |
| E8 | Formato de comunicación | Estático / interactivo / producción | — | [McKinney] vs. [ML Engineering] |

---

## BIBLIOGRAFÍA DE REFERENCIA (abreviaciones usadas)

| Abreviación en texto | Nombre completo del libro |
|---------------------|--------------------------|
| [An Introduction to Statistical Learning] | An Introduction to Statistical Learning |
| [The Elements of Statistical Learning] | The Elements of Statistical Learning |
| [Hands-On Machine Learning] | Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow |
| [Machine Learning Engineering in Action] | Machine Learning Engineering in Action |
| [Neural Networks and Deep Learning] | Neural Networks and Deep Learning |
| [Python para Análisis de Datos] | Python for Data Analysis (McKinney, 2017) |
| [Python para Análisis de Datos 3ra ed.] | Python for Data Analysis (McKinney, 2022) |
| [Análisis Econométrico - Greene] | Econometric Analysis |
| [Estadística aplicada básica - Moore] | Estadística aplicada básica |
| [Estadística - Triola] | Estadística |
| [Probabilidad y estadística - Devore] | Probabilidad y estadística para ingeniería y ciencias |
| [Probabilidad y estadística - Walpole] | Probabilidad y estadística para ingeniería y ciencias (Walpole) |
| [Estadística matemática con aplicaciones - Wackerly] | Estadística matemática con aplicaciones |
| [Estadística aplicada - De la Horra] | Estadística aplicada |
| [Inferencia Estadística] | Inferencia Estadística — Cuaderno de apuntes del estudiante |
| [Learning Statistics with R] | Learning Statistics with R |
| [Introduction to Probability - Blitzstein] | Introduction to Probability (Blitzstein & Hwang) |
| [Introduction to Probability - Bertsekas] | Introduction to Probability (Bertsekas & Tsitsiklis) |
| [Didáctica de la Estadística - Batanero] | Didáctica de la Estadística |
| [Big Data - Joyanes] | Big Data. Análisis de grandes volúmenes de datos en organizaciones |
| [Fundamentos de bases de datos - Silberschatz] | Fundamentos de bases de datos |
| [Fundamentos de Sistemas de BD - Elmasri] | Fundamentos de Sistemas de Bases de Datos |
