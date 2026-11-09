# El Niño 2026's Potential Impact Map on Children in Southeast Asia
### (an ongoing analysis to be continued until the phasing out of the event in 2027)

Author: Atiq Kainan Ahmed. Email: atiqka@yahoo.com




## Output 
The output map is shown here: https://github.com/atiqka/Elnino2026_child-impact/blob/main/ElNino_Child_Impact_Map_SEA_2025_26_20261109_045101.png

## Overview

This repository  contains an R programming language workflow exercise and script developed by Atiq Kainan Ahmed to model and map the **potential impact of the emerging 2026 El Niño event on children** across Southeast Asia. This is an ongoing analysis to be continued until the phasing out of the El Niño event in 2027.

El Niño events typically trigger hotter temperatures and disrupted rainfall patterns (including severe droughts) across the ASEAN region, disproportionately affecting children's health, nutrition, water security, and education. To identify high-risk hotspots, this project integrates **climate hazard anomalies** (drought and heat stress) with **child population exposure** and **national-level child vulnerability** using a spatial Multi-Criteria Evaluation (MCE) framework.

The analysis covers 11 Southeast Asian countries: **Brunei, Cambodia, Indonesia, Laos, Malaysia, Myanmar, Philippines, Singapore, Thailand, Timor-Leste, and Vietnam** (within the bounding box `92°E – 141°E`, `11.5°S – 28.5°N`), producing both a harmonized GeoTIFF raster and a publication-ready 300 DPI map.

---

## Methodology

### 1. Study Area & Spatial Harmonization
* **Boundaries:** Country polygons are loaded from Natural Earth (`rnaturalearth`, medium scale) in `EPSG:4326` (WGS 84) and validated.
* **Grid Alignment:** The ERA5-Land SPI raster serves as the spatial template. All other layers are cropped and masked strictly to the ASEAN land boundaries:
  * **Temperature Anomaly** and **WorldPop Under-18 Population** rasters are resampled to the template using **bilinear interpolation**.
  * **UNICEF Vulnerability** polygons are rasterized onto the template and resampled using **nearest-neighbor** (`near`) interpolation.

### 2. Climate Hazard Indicators (ERA5-Land)
Climate anomalies compare the Evaluation Period (2026) against a **6-year Climatology Baseline (2020 – 2025)** using monthly averaged reanalysis data:
* **Temperature Anomaly (Heat Stress):**
  $$\Delta T = \bar{T}_{\text{eval}} - \bar{T}_{\text{clim}}$$
  Both periods are converted from Kelvin to degrees Celsius ($K - 273.15$). Positive values indicate warmer-than-average conditions.
* **SPI Drought Proxy (Z-Score):**
  $$\text{SPI}_{\text{proxy}} = \frac{\bar{P}_{\text{eval}} - \bar{P}_{\text{clim}}}{\sigma_{\text{clim}} + 10^{-6}}$$
  Where $\bar{P}_{\text{eval}}$ is mean evaluation precipitation, $\bar{P}_{\text{clim}}$ is mean baseline precipitation, and $\sigma_{\text{clim}}$ is the baseline standard deviation. Negative values indicate drier-than-normal conditions.

### 3. Normalization & Power Transformation
To combine layers with different units onto a common **1 to 5** scale, each layer undergoes min-max normalization followed by a non-linear power transformation ($\text{power} = 0.4$) to reduce the skewing effect of extreme outliers and improve visual differentiation:

$$\text{Score} = \left( \frac{x - x_{\min}}{x_{\max} - x_{\min}} \right)^{0.4} \times 4 + 1$$

* **SPI Inversion:** The SPI layer is inverted ($1 - \text{scaled}$) before the power transformation so that **negative anomalies (drought)** receive higher hazard scores.

### 4. Multi-Criteria Evaluation (MCE) Weighting
The final **Potential Child Impact Score** (clamped between `1` and `5`) is calculated as a weighted linear combination:

a) **Hazard Composite (60% of total):**
   $$\text{Hazard} = (0.50 \times \text{SPI}_{\text{norm}}) + (0.50 \times \text{Heat}_{\text{norm}})$$
b) **Overall Impact Score:**
   $$\text{Impact} = (0.60 \times \text{Hazard}) + (0.25 \times \text{Pop}_{\text{norm}}) + (0.15 \times \text{Vulnerability}_{\text{norm}})$$

* **Classification Scale:** `1` (Lowest), `2` (Low), `3` (Moderate), `4` (High), `5` (Critical). Unpopulated or zero-population internal cells are preserved as `NA` and shaded neutral grey (`#EBEBEB`) on the output map.

---

## Data Sources and Acknowledgment

| Dataset | Variable / Indicator | Resolution / Scope | Source |
| :--- | :--- | :--- | :--- |
| **ERA5-Land Monthly Means** | `2m_temperature`, `total_precipitation` (2020–2026) | ~9 km (0.1°), Monthly | [Copernicus Climate Data Store (CDS)](https://cds.climate.copernicus.eu/) |
| **WorldPop Under-18** | `global_t_Under_18_2026_CN_1km_R2025A_UA_v1.tif` | 1 km, 2026 | [WorldPop](https://www.worldpop.org/) |
| **UNICEF CCRR** | `Child Vulnerability Score` (`ccrr_2026.xlsx`, Sheet: `Vulnerability`) | National (Country-level) | UNICEF Children's Climate Risk & Resilience (CCRR), 2026 (https://www.unicef.org/reports/childrens-climate-risk-report-2026) |
| **Admin Boundaries** | ASEAN country polygons | Medium scale, EPSG:4326 | [Natural Earth](https://www.naturalearthdata.com/) via `rnaturalearth` |

---


## License

Copyright (c) 2025 Atiq Kainan Ahmed.

Codes and files in this repository are licensed under the **MIT License**.  

*Note: Underlying datasets (Copernicus ERA5-Land, WorldPop, UNICEF CCRR, and Natural Earth) are subject to their respective original data licenses and terms of use.*

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

