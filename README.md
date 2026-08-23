# 📊 Social Anxiety Dataset — Statistical Analysis
### *Análisis Estadístico — Social Anxiety Dataset*

---

> 🇬🇧 [English](#english) · 🇪🇸 [Español](#español)

---

## English

### Overview

End-to-end statistical analysis of the **Social Anxiety Dataset** (Kaggle, Australia 2019–2022), covering data cleaning, inferential statistics, goodness of fit, and linear regression — both simple and non-linear with transformations. All results are benchmarked against an external reference: **Stanton et al. (2020)**, a peer-reviewed study of 1,491 Australian adults using the DASS-21 instrument during the COVID-19 pandemic.

---

### Dataset

| Field | Details |
|---|---|
| **Source** | [Kaggle — Social Anxiety Dataset](https://www.kaggle.com/datasets/natezhang123/social-anxiety-dataset/data) |
| **Records** | +10,000 |
| **Period** | 2019 – 2022 |
| **Coverage** | Australia |
| **Columns** | 20 variables |
| **Key variables** | Age, Gender, Sleep Hours, Anxiety Level, Stress Level, Caffeine Intake, Physical Activity, Heart Rate, among others |

### External Reference

**Stanton, R. et al. (2020).** *Depression, Anxiety and Stress during COVID-19: Associations with Changes in Physical Activity, Sleep, Tobacco and Alcohol Use in Australian Adults.* International Journal of Environmental Research and Public Health, 17(11), 4065.

| Metric | Value |
|---|---|
| n | 1,491 Australian adults |
| Instrument | DASS-21 |
| Anxiety Mean | 3.0 (SD = 3.9) |
| Sleep Mean | 7.1 hrs (SD = 1.3) |
| Sleep Variance | 1.69 |

---

### Project Structure

```
📦 social-anxiety-analysis
├── 📓 analysis.ipynb          # Main notebook with full analysis
├── 📊 data/
│   └── social_anxiety.csv     # Original dataset
├── 📈 figures/                # All generated plots
│   ├── ic_media.png
│   ├── ic_varianza.png
│   ├── hipotesis_una.png
│   ├── hipotesis_dos.png
│   ├── bondad_ansiedad.png
│   ├── bondad_stress.png
│   ├── regresion_lineal.png
│   └── regresion_no_lineal.png
└── 📄 README.md
```

---

### Analysis Pipeline

#### 1. 🧹 Data Cleaning & Transformation
- **Birth Year** derived as `2022 − Age`
- **Anxiety Level** converted from 1–10 scale to DASS-21 (0–21): `((value − 1) / 9) × 21`
- **Stress Level** same linear conversion with clinical cutoffs: Normal ≤7, Mild 8–9, Moderate 10–12, Severe 13–16, Extremely Severe >16
- **Physical Activity** converted from hours to minutes (×60)

> ⚠️ **Limitation:** the 1–10 → DASS-21 conversion assumes perfect proportionality between two distinct instruments, which is not clinically validated.

---

#### 2. 📐 Confidence Intervals — Sleep Hours
Variable: `Sleep Hours` · Sample: n = 200 · α = 0.05

| Parameter | Sample | IC 95% | Stanton ref | In IC? |
|---|---|---|---|---|
| Mean μ | 6.70 hrs | [6.54, 6.87] | 7.1 hrs | ❌ No |
| Variance σ² | 1.37 | [1.13, 1.68] | 1.69 | ❌ No |

Both reference values fall outside the confidence intervals — the dataset population sleeps significantly less and with less variability than the general Australian population.

---

#### 3. 🔬 Hypothesis Testing

**One-sample test — Anxiety Level (DASS-21)**

| | Value |
|---|---|
| H₀ | μ = 3.0 (Stanton et al. 2020) |
| H₁ | μ > 3.0 (right tail) |
| x̄ | 7.17 |
| t observed | 11.52 |
| t critical | 1.65 |
| p-value | < 0.0001 |
| **Decision** | **Reject H₀** |

**Two-sample test — Anxiety by Gender (Welch's t-test)**

| | Value |
|---|---|
| H₀ | μ_female = μ_male |
| H₁ | μ_female ≠ μ_male (bilateral) |
| t observed | −0.39 |
| t critical | ±1.97 |
| p-value | 0.6944 |
| **Decision** | **Fail to reject H₀** |

---

#### 4. 📊 Goodness of Fit (Chi-square)

Comparing observed category distributions against Stanton et al. (2020) proportions.

| Variable | χ² observed | χ² critical | p-value | Decision |
|---|---|---|---|---|
| Anxiety DASS-21 | 20,524.40 | 9.49 | ≈ 0 | ❌ Reject H₀ |
| Stress DASS-21 | 22,450.71 | 9.49 | ≈ 0 | ❌ Reject H₀ |

The dataset has far fewer Normal cases and far more Severe/Extremely Severe cases than the general Australian population — explained by the clinical nature of the sample and the COVID-19 pandemic context.

---

#### 5. 📈 Linear Regression — Sleep Hours → Anxiety Level

```
ŷ = 19.69 − 1.98x
```

| Metric | Value |
|---|---|
| β₁ | −1.9812 |
| IC 95% β₁ | [−2.05, −1.92] |
| R² | 0.2421 |
| σ | 4.3025 |
| T observed | −59.28 |
| p-value | ≈ 0 → **Reject H₀** |
| Prediction z = 7.1 hrs | ŷ = 5.63 pts DASS-21 (Mild) |

---

#### 6. 📉 Non-linear Regression — Reciprocal Transformation

```
ŷ = −5.9487 + 79.7263 · (1/x)
```

| Metric | Linear | Non-linear | Δ |
|---|---|---|---|
| R² | 0.2421 | 0.3018 | +6% ↑ |
| r | −0.4921 | 0.5494 | ↑ |
| σ | 4.3025 | 4.1296 | −0.17 ↓ |

The reciprocal model better captures the clinical reality: losing one hour of sleep has a much greater impact when already sleep-deprived (3h → 2h hurts more than 8h → 7h).

---

### Key Findings

1. **More severe psychological profile** — Both anxiety and stress levels are significantly higher than in the general Australian population, explained by the clinical nature of the sample and the pandemic context.

2. **Sleep predicts anxiety** — Each additional hour of sleep reduces anxiety by 1.98 DASS-21 points on average. Sleep alone explains 24% of anxiety variability.

3. **Non-linear effect** — The reciprocal model (R²=0.30) outperforms the linear model (R²=0.24) and is clinically more realistic: the effect of sleep loss accelerates exponentially at very low sleep levels.

4. **Same pattern in stress** — The Extremely Severe stress category shows 8× more cases than expected (2,716 vs 330), confirming a global psychological overload — not an isolated symptom.

---

### Tech Stack

```python
Python 3.x
├── pandas
├── numpy
├── scipy.stats
├── matplotlib
└── seaborn
```

---

### How to Run

```bash
# Clone the repository
git clone https://github.com/your-username/social-anxiety-analysis.git
cd social-anxiety-analysis

# Install dependencies
pip install pandas numpy scipy matplotlib seaborn

# Open the notebook
jupyter notebook analysis.ipynb
```

---

### References

- Stanton, R., To, Q. G., Khalesi, S., Williams, S. L., Alley, S. J., Thwaite, T. L., Fenning, A. S., & Vandelanotte, C. (2020). Depression, Anxiety and Stress during COVID-19: Associations with Changes in Physical Activity, Sleep, Tobacco and Alcohol Use in Australian Adults. *International Journal of Environmental Research and Public Health*, 17(11), 4065. https://doi.org/10.3390/ijerph17114065

- Zhang, N. (2023). *Social Anxiety Dataset — Behavioral, Lifestyle & Psychological Factors*. Kaggle. https://www.kaggle.com/datasets/natezhang123/social-anxiety-dataset

---

---

## Español

### Descripción general

Análisis estadístico completo del **Social Anxiety Dataset** (Kaggle, Australia 2019–2022), que incluye limpieza de datos, estadística inferencial, bondad de ajuste y regresión lineal — simple y no lineal con transformadas. Todos los resultados se comparan contra una referencia externa: **Stanton et al. (2020)**, un estudio publicado en revista indexada con 1,491 adultos australianos usando el instrumento DASS-21 durante la pandemia de COVID-19.

---

### Dataset

| Campo | Detalle |
|---|---|
| **Fuente** | [Kaggle — Social Anxiety Dataset](https://www.kaggle.com/datasets/natezhang123/social-anxiety-dataset/data) |
| **Registros** | +10,000 |
| **Período** | 2019 – 2022 |
| **Cobertura** | Australia |
| **Columnas** | 20 variables |
| **Variables clave** | Age, Gender, Sleep Hours, Anxiety Level, Stress Level, Caffeine Intake, Physical Activity, Heart Rate, entre otras |

### Referencia Externa

**Stanton, R. et al. (2020).** *Depression, Anxiety and Stress during COVID-19: Associations with Changes in Physical Activity, Sleep, Tobacco and Alcohol Use in Australian Adults.* International Journal of Environmental Research and Public Health, 17(11), 4065.

| Métrica | Valor |
|---|---|
| n | 1,491 adultos australianos |
| Instrumento | DASS-21 |
| Media ansiedad | 3.0 (SD = 3.9) |
| Media sueño | 7.1 hrs (SD = 1.3) |
| Varianza sueño | 1.69 |

---

### Estructura del Proyecto

```
📦 social-anxiety-analysis
├── 📓 analysis.ipynb          # Notebook principal con el análisis completo
├── 📊 data/
│   └── social_anxiety.csv     # Dataset original
├── 📈 figures/                # Todas las gráficas generadas
│   ├── ic_media.png
│   ├── ic_varianza.png
│   ├── hipotesis_una.png
│   ├── hipotesis_dos.png
│   ├── bondad_ansiedad.png
│   ├── bondad_stress.png
│   ├── regresion_lineal.png
│   └── regresion_no_lineal.png
└── 📄 README.md
```

---

### Pipeline de Análisis

#### 1. 🧹 Limpieza y Transformación de Variables
- **Birth Year** derivado como `2022 − Age`
- **Anxiety Level** convertido de escala 1–10 a DASS-21 (0–21): `((valor − 1) / 9) × 21`
- **Stress Level** misma conversión lineal con cortes clínicos: Normal ≤7, Mild 8–9, Moderate 10–12, Severe 13–16, Extremely Severe >16
- **Physical Activity** convertida de horas a minutos (×60)

> ⚠️ **Limitación:** la conversión 1–10 → DASS-21 asume proporcionalidad perfecta entre dos instrumentos distintos, lo cual no está clínicamente validado.

---

#### 2. 📐 Intervalos de Confianza — Sleep Hours
Variable: `Sleep Hours` · Muestra: n = 200 · α = 0.05

| Parámetro | Muestral | IC 95% | Ref. Stanton | ¿En IC? |
|---|---|---|---|---|
| Media μ | 6.70 hrs | [6.54, 6.87] | 7.1 hrs | ❌ No |
| Varianza σ² | 1.37 | [1.13, 1.68] | 1.69 | ❌ No |

Ambos valores de referencia quedan fuera de los intervalos — la población del dataset duerme significativamente menos y con menos variabilidad que la población general australiana.

---

#### 3. 🔬 Pruebas de Hipótesis

**Una muestra — Anxiety Level (DASS-21)**

| | Valor |
|---|---|
| H₀ | μ = 3.0 (Stanton et al. 2020) |
| H₁ | μ > 3.0 (cola derecha) |
| x̄ | 7.17 |
| t observado | 11.52 |
| t crítico | 1.65 |
| p-valor | < 0.0001 |
| **Decisión** | **SE RECHAZA H₀** |

**Dos muestras — Ansiedad por Género (t de Welch)**

| | Valor |
|---|---|
| H₀ | μ_mujeres = μ_hombres |
| H₁ | μ_mujeres ≠ μ_hombres (bilateral) |
| t observado | −0.39 |
| t crítico | ±1.97 |
| p-valor | 0.6944 |
| **Decisión** | **NO SE RECHAZA H₀** |

---

#### 4. 📊 Bondad de Ajuste (Chi-cuadrado)

Comparación de la distribución de categorías observadas contra las proporciones de Stanton et al. (2020).

| Variable | χ² observado | χ² crítico | p-valor | Decisión |
|---|---|---|---|---|
| Ansiedad DASS-21 | 20,524.40 | 9.49 | ≈ 0 | ❌ Se rechaza H₀ |
| Estrés DASS-21 | 22,450.71 | 9.49 | ≈ 0 | ❌ Se rechaza H₀ |

El dataset tiene muchos menos casos Normales y muchos más casos Severos/Extremadamente Severos que la población general australiana — explicado por la naturaleza clínica de la muestra y el contexto pandémico de COVID-19.

---

#### 5. 📈 Regresión Lineal — Sleep Hours → Anxiety Level

```
ŷ = 19.69 − 1.98x
```

| Métrica | Valor |
|---|---|
| β₁ | −1.9812 |
| IC 95% β₁ | [−2.05, −1.92] |
| R² | 0.2421 |
| σ | 4.3025 |
| T observado | −59.28 |
| p-valor | ≈ 0 → **SE RECHAZA H₀** |
| Predicción z = 7.1 hrs | ŷ = 5.63 pts DASS-21 (Mild) |

---

#### 6. 📉 Regresión No Lineal — Transformada Recíproca

```
ŷ = −5.9487 + 79.7263 · (1/x)
```

| Métrica | Lineal | No Lineal | Δ |
|---|---|---|---|
| R² | 0.2421 | 0.3018 | +6% ↑ |
| r | −0.4921 | 0.5494 | ↑ |
| σ | 4.3025 | 4.1296 | −0.17 ↓ |

El modelo recíproco captura mejor la realidad clínica: perder una hora de sueño tiene un impacto mucho mayor cuando ya se duerme poco (pasar de 3h a 2h duele más que de 8h a 7h).

---

### Hallazgos Principales

1. **Perfil psicológico más severo** — Tanto los niveles de ansiedad como de estrés son significativamente más altos que en la población general australiana, explicado por la naturaleza clínica de la muestra y el contexto pandémico.

2. **El sueño predice la ansiedad** — Cada hora adicional de sueño reduce la ansiedad en 1.98 puntos DASS-21 en promedio. El sueño explica el 24% de la variabilidad en ansiedad.

3. **Efecto no lineal** — El modelo recíproco (R²=0.30) supera al lineal (R²=0.24) y es clínicamente más realista: el efecto de la pérdida de sueño se acelera exponencialmente en niveles muy bajos de sueño.

4. **Mismo patrón en estrés** — La categoría Extremely Severe de estrés tiene 8 veces más casos de los esperados (2,716 vs 330), confirmando una sobrecarga psicológica global — no un síntoma aislado.

---

### Stack Tecnológico

```python
Python 3.x
├── pandas
├── numpy
├── scipy.stats
├── matplotlib
└── seaborn
```

---

### Cómo Ejecutar

```bash
# Clonar el repositorio
git clone https://github.com/tu-usuario/social-anxiety-analysis.git
cd social-anxiety-analysis

# Instalar dependencias
pip install pandas numpy scipy matplotlib seaborn

# Abrir el notebook
jupyter notebook analysis.ipynb
```

---

### Referencias

- Stanton, R., To, Q. G., Khalesi, S., Williams, S. L., Alley, S. J., Thwaite, T. L., Fenning, A. S., & Vandelanotte, C. (2020). Depression, Anxiety and Stress during COVID-19: Associations with Changes in Physical Activity, Sleep, Tobacco and Alcohol Use in Australian Adults. *International Journal of Environmental Research and Public Health*, 17(11), 4065. https://doi.org/10.3390/ijerph17114065

- Zhang, N. (2023). *Social Anxiety Dataset — Behavioral, Lifestyle & Psychological Factors*. Kaggle. https://www.kaggle.com/datasets/natezhang123/social-anxiety-dataset

---

<div align="center">

Made with 📊 Python · Universidad Industrial de Santander · Estadística II

</div>
