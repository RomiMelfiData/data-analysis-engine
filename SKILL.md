---
name: data-analysis-engine
description: >
  Motor de ejecución de análisis de datos estructurado en etapas (E1–E8), operando bajo
  Manual_Operaciones.md y Mapa Conceptual cargados como referencias. Usar esta skill
  siempre que el usuario mencione: análisis de datos, EDA, hipótesis estadística,
  carga de dataset, limpieza de datos, feature engineering, modelado predictivo,
  inferencia estadística, validación de modelos, o reporte de resultados — incluso si
  el pedido parece simple ("cargá el CSV", "quiero explorar estos datos", "tengo una
  hipótesis sobre X"). La skill garantiza rigor metodológico, no-redundancia entre pasos
  y decisiones documentadas con bibliografía del Manual. También triggear ante: pivotaje
  EDA↔HIPÓTESIS, rollback de etapas, y cualquier flujo de ciencia de datos end-to-end.
compatibility: Python (pandas, scipy, statsmodels, sklearn, imbalanced-learn), bash_tool
---

# Data Analysis Engine — Motor de Ejecución

## Recursos bundled

- **Manual de referencia completo**: `references/Manual_Operaciones.md`
  - Leer la sección correspondiente a cada etapa ANTES de ejecutarla.
  - Índice de secciones: E1, REGLA PIVOTAJE, E2, E3, E4, E5, E6, E7, E8, TABLA MAESTRA, BIBLIOGRAFÍA.

> ⚠ NUNCA avances de etapa sin completar el ✋ STOP de la etapa anterior.
> ⚠ NUNCA recalcules análisis ya ejecutados en la sesión. Referencialos por nombre de variable Python.

---

## REGLAS TÉCNICAS DE EJECUCIÓN

### Scripts externos — obligatorio
- NUNCA usar `python -c "..."` con código inline. Siempre escribir un archivo `.py`.
- Los scripts se guardan en la **carpeta del proyecto activo**, no en `Temp/`.
- Ejemplo correcto: `Write(proyecto/scripts/e3_exploracion.py)` → `Bash(python proyecto/scripts/e3_exploracion.py)`
- Esto evita errores de encoding, advertencias de seguridad y pérdida de archivos.

### Encoding — obligatorio en Windows
- Todo script que lea `Manual_Operaciones.md` o archivos con tildes/símbolos debe incluir:
  ```python
  with open(ruta, encoding='utf-8') as f:
  ```
- Nunca asumir encoding por defecto (cp1252 en Windows rompe caracteres como H₀, α, ñ).

### Lectura del Manual
- Leer secciones específicas con un script `.py` guardado en el proyecto:
  ```python
  with open('references/Manual_Operaciones.md', encoding='utf-8') as f:
      content = f.read()
  start = content.find('## E3')
  end = content.find('## E4', start)
  print(content[start:end])
  ```

### Dependencias — verificar al inicio de E2
- Antes de instalar cualquier librería, verificar qué falta y avisar al usuario:
  ```python
  import importlib
  requeridas = ['pandas', 'scipy', 'statsmodels', 'sklearn', 'imblearn', 'shap']
  faltantes = [p for p in requeridas if not importlib.util.find_spec(p)]
  print('Faltantes:', faltantes or 'Ninguna')
  ```
- Mostrar lista de faltantes y pedir confirmación ANTES de instalar:
  ```
  ⚠ Librerías faltantes: [statsmodels, imblearn]
  ¿Instalo ahora? (sí/no)
  ✋ STOP: esperando confirmación
  ```

---

## FORMATO DE SALIDA GLOBAL

- Estilo CLI: conciso, técnico, sin prosa explicativa innecesaria.
- Mínimo de tokens: omitir redundancia, ir directo al output relevante.
- Rigor bibliográfico: toda recomendación técnica debe citar la fuente del Manual.
- Tablas de decisión en formato Markdown con columna de recomendación IA (ver abajo).

---

## ESTADO DE SESIÓN (actualizar mentalmente en cada paso)

```
MODO: [EDA | HIPÓTESIS | MIXTO]
HIPÓTESIS: [H₀ + H₁ registradas o None]
ETAPA_ACTUAL: [E1..E8]
ANÁLISIS_EJECUTADOS: [lista de variables Python ya calculadas]
PCPiv_ACTIVO: [Sí | No]
ROLLBACK_PENDIENTE: [Sí → EX | No]
```

---

## E1 — DISEÑO

**Acción automática al iniciar:**

```
> ¿Modo de análisis?
  [1] EDA   — sin teoría previa; objetivo: descubrimiento
  [2] HIPÓTESIS — H₀ y H₁ especificadas a priori; α fijado antes de ver datos
  [3] Ayudarme a decidir
```

**Si HIPÓTESIS:**
- Registrar: `H₀ = ___, H₁ = ___, α = ___, potencia_target = ___`
- No avanzar a E2 sin estos 4 campos completos.
- Consultar `references/Manual_Operaciones.md §E1 §1.2` para validar modo.

**✋ STOP E1:** Esperar confirmación de modo + hipótesis antes de continuar.

---

## E2 — ADQUISICIÓN Y CARGA

**Verificar dependencias primero** (ver Reglas Técnicas arriba). Guardar script en proyecto.

**Acción automática al cargar dataset** (`scripts/e2_carga.py` en carpeta proyecto):
```python
import pandas as pd
df = pd.read_csv(PATH)
print(df.info())
print(df.describe(include='all'))
```

**Verificaciones inmediatas** (consultar `references/Manual_Operaciones.md §E2`):

1. **Compatibilidad con H₀**: ¿El dataset contiene las variables necesarias?
   - NO → **STOP CRÍTICO**: sugerir reformular E1 o cambiar dataset.

2. **Tipo de dato detectado**:
   - Serie temporal → verificar frecuencia, gaps, estacionariedad inicial
   - Panel → verificar balance, unidades, periodos
   - Cross-section → verificar representatividad muestral

3. **Calidad inicial**: NaN%, duplicados, dtypes incorrectos.

**✋ STOP E2:** Presentar hallazgos. Esperar aprobación para continuar a E3.

---

## E3–E8 — PROTOCOLO DE EJECUCIÓN POR ETAPA

Para **cada etapa** (E3 Exploración, E4 Limpieza, E5 Inferencia, E6 Modelado, E7 Validación, E8 Comunicación):

### Paso 1 — Consultar Manual
Leer `references/Manual_Operaciones.md` sección correspondiente con script `.py` guardado en proyecto (ver Reglas Técnicas). Secciones:
- `§E3` — exploración y estadística descriptiva
- `§E4` — limpieza y preprocesamiento
- `§E5` — análisis inferencial
- `§E6` — modelado predictivo
- `§E7` — validación y selección de modelos
- `§E8` — comunicación y reportes

### Paso 2 — Presentar tabla de decisión CON recomendación IA

Siempre incluir una fila final `★ IA` con la opción recomendada y su justificación bibliográfica:

```
┌────┬─────────────────────────┬──────────────────────┬──────────────────────────┐
│ ID │ Opción                  │ Libro                │ Riesgo / Impacto         │
├────┼─────────────────────────┼──────────────────────┼──────────────────────────┤
│ A  │ [opción A]              │ [referencia Manual]  │ [riesgo si se elige]     │
│ B  │ [opción B]              │ [referencia Manual]  │ [riesgo si se elige]     │
│ C  │ [opción C]              │ [referencia Manual]  │ [riesgo si se elige]     │
├────┼─────────────────────────┼──────────────────────┼──────────────────────────┤
│ ★  │ Recomiendo [ID]: [razón │ [cita bibliográfica] │ [por qué es la mejor     │
│ IA │ técnica concreta]       │                      │ opción en este contexto] │
└────┴─────────────────────────┴──────────────────────┴──────────────────────────┘
```

### Paso 3 — ✋ STOP
Esperar input del usuario. **No asumir camino.** No ejecutar hasta recibir ID elegido.

### Paso 4 — Ejecutar opción elegida
- Guardar script en carpeta del proyecto (no en Temp/).
- Reutilizar variables Python ya calculadas (no recalcular).
- Referenciar análisis previos: `"reutilizando df_clean definido en E4"`.

---

## HIPÓTESIS CANDIDATAS — registro en un solo paso

Cuando el usuario elige una hipótesis candidata (HC-X), mostrar TODO junto para confirmar o modificar antes de continuar:

```
HC-A seleccionada. Confirmá o modificá antes de continuar:

H₀ = [texto completo de H₀]
H₁ = [texto completo de H₁]
α  = 0.05  ← estándar; alternativa: 0.01 (más estricto)
potencia_target = 0.80  ← mínimo recomendado; alternativa: 0.90

¿Confirmás estos valores o modificás algún campo?
✋ STOP: respondé con los valores finales o escribí "confirmar" para usar los de arriba.
```

No hacer dos rondas de preguntas separadas. Todo en un solo bloque.

---

## E8 — COMUNICACIÓN: opciones de formato

Al llegar a E8, presentar las siguientes dimensiones de decisión:

**Audiencia:**
- A — Técnica (métricas completas, supuestos, código reproducible)
- B — Ejecutiva (KPIs + impacto negocio, sin fórmulas)
- C — Mixta (resumen ejecutivo + apéndice técnico)

**Formato de salida** (se pueden combinar):
- MD — Markdown estructurado
- HTML — Reporte interactivo con gráficos (distribuciones, series temporales, rankings)
- IPYNB — Jupyter Notebook reproducible con todo el código
- TXT — Transcript completo de la sesión (todas las decisiones, hallazgos y outputs)
- PDF — Reporte en PDF (requiere pandoc o similar)

> ⚠ Todos los archivos de salida se guardan en la **carpeta del proyecto**, no en Temp/.
> ⚠ El HTML debe incluir gráficos inline (matplotlib/plotly exportado) sin dependencias externas.
> ⚠ El TXT debe incluir: modo elegido, hipótesis registradas, decisiones por etapa, hallazgos principales y outputs de cada script.

**✋ STOP E8:** Esperar elección de audiencia + formato(s) antes de generar.

---

## REGLAS TRANSVERSALES

### NO-REDUNDANCIA
- Prohibido recalcular análisis ya ejecutados en la sesión.
- Mantener registro mental de `ANÁLISIS_EJECUTADOS`.
- Si se necesita un resultado previo: referenciarlo, no repetirlo.

### PIVOTAJE BIDIRECCIONAL (PCPiv)
**Activar si cualquiera:**
- E3 produce evidencia que refuta la hipótesis de E1
- EDA sugiere que el modo debería cambiar (EDA↔HIPÓTESIS)
- Nueva variable relevante emerge en E3/E4

**Protocolo** (ver `references/Manual_Operaciones.md §REGLA PIVOTAJE §PT.2`):
```
PCPiv ACTIVADO
> Evidencia: [descripción del hallazgo]
> Opciones:
  [P1] Mantener modo actual — justificación: ___
  [P2] Pivotar a [nuevo modo] — impacto en α/H₀: ___
  [P3] Modo mixto con registro explícito
> ★ IA recomienda [P?]: [razón técnica + cita Manual]
✋ STOP: esperando decisión del usuario
```

### ROLLBACK
**Activar si** E5–E7 detecta baja calidad (supuestos violados, métricas inaceptables, data leakage):

```
ROLLBACK SUGERIDO
> Problema detectado: [descripción]
> Etapa sugerida de retorno: [E2 | E4]
> Justificación bibliográfica: [cita de references/Manual_Operaciones.md]
✋ STOP: confirmación requerida antes de retroceder
```

---

## REFERENCIA RÁPIDA — ÍNDICE DEL MANUAL

| Sección | Contenido clave |
|---------|----------------|
| §E1 | Árbol EDA vs HIPÓTESIS, filtros ética, STOP multicriterio |
| §PIVOTAJE | PT.1–PT.4: condiciones, protocolo, no-recálculo, condicionamiento |
| §E2 | Carga, tipos de dato, advertencias muestreo, STOP E2 |
| §E3 | Descriptiva, correlaciones, distribuciones, PCPiv desde EDA |
| §E4 | Imputación, outliers, encoding, análisis de sensibilidad |
| §E5 | Árbol de tests, tamaño del efecto, bifurcación H₀ no refutada |
| §E6 | Selección de modelo, supuestos OLS, reencuadre por PCPiv |
| §E7 | CV, métricas, comparación de modelos, rollback desde E7 |
| §E8 | Árbol de audiencia, formatos de reporte, ética de comunicación |
| §TABLA MAESTRA | Índice de todas las decisiones críticas del flujo |
| §BIBLIOGRAFÍA | Abreviaciones de fuentes usadas en tablas de contraste |
