# Aviation Accident Analysis

A data science consulting project analyzing FAA aviation accident records (1983–2022) to identify the safest aircraft makes and models for an airline/airplane insurer.

---

## Project Overview

The client wants to know which aircraft makes and models exhibit:
- **Low rates of total destruction** in accidents
- **Low likelihood of fatal or serious passenger injuries**

Recommendations are provided separately for **small aircraft** (< 20 passengers) and **large aircraft** (≥ 20 passengers).

---

## Notebooks

| Notebook | Purpose |
|---|---|
| [Aviation_Accidents_Cleaning.ipynb](Aviation_Accidents_Cleaning.ipynb) | Data loading, inspection, and cleaning pipeline |
| [Aviation_Accidents_Data_Analysis.ipynb](Aviation_Accidents_Data_Analysis.ipynb) | EDA, safety metric analysis, and recommendations |

**Data:** [data/AviationData.csv](data/AviationData.csv) → cleaned to [data/AviationData_Cleaned.csv](data/AviationData_Cleaned.csv)

---

## Data Cleaning Summary

Starting from 88,889 raw records (1948–2022), the cleaning pipeline:

1. **Filtered** to 1983+ (40-year max aircraft lifetime), professional builds only (`Amateur.Built == No`), and accident records only — yielding ~75,000 records
2. **Created key metrics:**
   - `Injury.Rate` = (Fatal + Serious injuries) / Total Passengers
   - `Is.Destroyed` = 1 if aircraft was fully destroyed, 0 otherwise
   - `Total.Passengers` = sum of all injury/uninjured counts per accident
   - `Make.Model` = composite unique identifier for each plane type
3. **Standardized** `Make` (title case to merge `CESSNA`/`Cessna`), `Model` (uppercase), and inconsistent unknown labels across categorical columns
4. **Dropped** columns with > 50% missing values (`FAR.Description`, `Air.carrier`, `Schedule`, `Aircraft.Category`, `Latitude`, `Longitude`) plus non-analytical admin columns

---

## Analysis Summary

### Small vs. Large Aircraft Split

Records were split on a **20-passenger threshold**:
- **Small aircraft** (< 20 passengers): the large majority of records, dominated by general aviation makes like Cessna, Piper, and Beechcraft
- **Large aircraft** (≥ 20 passengers): smaller record count, dominated by commercial jet manufacturers

---

### Make-Level Findings

**Mean Injury Rate by Make — Small and Large Aircraft**

Side-by-side horizontal bar charts rank the 15 makes with the lowest mean injury rates for each group. The best small-aircraft makes cluster near zero, while large-aircraft makes show even lower rates due to the survivability advantages of larger, more structurally robust airframes.

**Injury Rate Distributions by Make**

- *Small aircraft (violin plot):* The top 10 makes all show distributions heavily weighted toward zero, with long right tails — most accidents produce no serious injuries, but extreme events do occur.
- *Large aircraft (strip plot):* Individual accident outcomes are visible. Most events cluster near zero injury rate; outlier accidents stand out clearly at the individual point level.

**Destruction Rate by Make**

The 15 makes with the lowest mean destruction rates are plotted for both groups. Makes that rank highly on **both** injury rate and destruction rate are the strongest candidates for recommendation.

---

### Model-Level Findings

**Large Aircraft Models**

Filtered to `Make.Model` groups with ≥ 10 accident records, then ranked by mean injury rate. A bar chart of the top 15 models is paired with a strip plot showing the spread of individual accident outcomes. Commercial jet models (Boeing 737 variants, Airbus A320 family) consistently appear near the top due to their structural redundancy and highly regulated operations.

**Small Aircraft Models**

Filtered within the top-10 safest makes, then to models with ≥ 10 records. A bar chart and violin plot show that the safest small models are predominantly 4-seat single-engine fixed-wing aircraft (Cessna 172 series, Piper PA-28 series, Beechcraft Bonanza variants) — well-tested, high-volume designs with decades of safety refinement.

---

### Contributing Factors

**Factor 1: Weather Condition**

| Condition | Mean Injury Rate | Mean Destruction Rate |
|---|---|---|
| IMC (Instrument) | Higher | Higher |
| VMC (Visual) | Lower | Lower |
| Unknown | Intermediate | Intermediate |

Accidents in **IMC** (low visibility / instrument conditions) produce meaningfully higher injury and destruction rates than those in **VMC** (clear visual conditions). This is consistent with controlled flight into terrain (CFIT) being a disproportionately fatal accident type. Insurers should price IMC-exposed operations at a higher risk premium.

**Factor 2: Engine Type**

| Engine Type | Profile |
|---|---|
| Turbo Fan | Low injury rate, moderate destruction rate — commercial jets are survivable but often written off structurally |
| Turbo Prop | Moderate injury and destruction rates |
| Reciprocating | Moderate rates — broad range of GA accidents |
| Turbo Shaft (helicopters) | Elevated injury rates — rotary-wing accidents tend to be more severe |

Engine type is a strong proxy for aircraft category and operational context. The clear separation between engine types supports its use as a risk-segmentation variable in insurance pricing.

---

## Key Recommendations

### Small Aircraft
Manufacturers and models consistently ranking in the **top 15 for both lowest injury rate and lowest destruction rate** — with sufficient sample sizes (≥ 50 make-level, ≥ 10 model-level records) — are the most defensible recommendations. Cessna, Piper, and Beechcraft dominate this category.

### Large Aircraft
Boeing and Airbus models with high accident record counts show the lowest and most stable mean injury rates. The limited large-aircraft accident record count means model-level estimates are noisier; make-level recommendations are more robust here.

### Risk Factors for the Insurer
1. **IMC operations** significantly increase both injury and destruction risk — flag policies for aircraft frequently operating in IFR conditions
2. **Engine type** separates risk profiles cleanly — rotary-wing and piston-engine GA carry different risk profiles than turbofan commercial operations

---

## How to Run

1. Run [Aviation_Accidents_Cleaning.ipynb](Aviation_Accidents_Cleaning.ipynb) top-to-bottom to generate `data/AviationData_Cleaned.csv`
2. Run [Aviation_Accidents_Data_Analysis.ipynb](Aviation_Accidents_Data_Analysis.ipynb) top-to-bottom to reproduce all analysis and visualizations
