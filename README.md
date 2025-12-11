# BC-wildfires-analysis



---

## 2️⃣ `bc-wildfires-spatiotemporal-analysis` – README.md

```markdown
# BC Wildfires 2013–2024: Spatio-Temporal Analysis in R

End-to-end R workflow analysing wildfire frequency, burned area, spatial distribution,
and ignition causes in British Columbia using BC Wildfire Service point data (KMZ) and
official yearly summaries (2013–2024). :contentReference[oaicite:9]{index=9}  

---

## 1. Project Overview

Wildfires have become a defining environmental issue for British Columbia. This project
uses **BC Wildfire Service KMZ point files (2013–2024)** plus **official yearly
statistics** to answer:

1. How have wildfire **frequency, size, and spatial distribution** changed from 2013–2024?  
2. How do ignition causes – especially **human vs lightning** – relate to those patterns? :contentReference[oaicite:10]{index=10}  

Developed as **Assignment 2** for **DATA*6200**.

---

## 2. Data & Sources

- **BC Wildfire Service KMZ point files (2013–2024)**
  - One point per fire
  - Rich HTML “Description” field containing attributes: size, cause, year, etc. :contentReference[oaicite:11]{index=11}  
- **Official yearly summaries** scraped from the BCWS statistics page:
  - Total fires
  - Total hectares burned
  - Counts by ignition cause (human, lightning, etc.)

---

## 3. Methods

### 3.1. Cleaning & Wrangling

1. **KMZ → KML → sf points**
   - Unzip KMZs, read `doc.kml` using `sf`, combine all non-empty layers. :contentReference[oaicite:12]{index=12}  

2. **Geometry normalisation**
   - Convert to simple `POINT` geometries in WGS84.
   - Drop Z/M dimensions; extract lon/lat for plotting.
   - Apply a coarse **BC bounding box** to remove stray points. :contentReference[oaicite:13]{index=13}  

3. **Parsing HTML Description**
   - Use `rvest` (`read_html()`, `xml_find_all("//tr")`, `html_text()`) to extract key–value pairs.
   - Convert to tidy columns with `pivot_wider()`.
   - Construct:
     - `FIRE_YEAR`
     - `SIZE_HA`
     - `CAUSE_GENERAL` :contentReference[oaicite:14]{index=14}  

4. **De-duplication**
   - Sort by `FIRE_YEAR`, name, and `SIZE_HA`.
   - Keep the largest record per fire name/year to build a **unique-fire dataset**. :contentReference[oaicite:15]{index=15}  

5. **Cause grouping**
   - Group raw causes into:
     - `Human`
     - `Lightning` (includes “natural”)
     - `Unknown/None` (unknown, under investigation, NA, etc.) :contentReference[oaicite:16]{index=16}  

6. **Validation**
   - Summarise fires by year (counts and area) and compare with scraped BCWS stats.
   - Two-panel plot shows both series track closely, including extreme years like
     **2017–2018 and 2021**, giving confidence in the processed dataset. :contentReference[oaicite:17]{index=17}  

---

## 4. Analysis & Visualisations

### 4.1. Temporal trends

- **Bar chart** of fire counts (2013–2024):
  - Peaks in **2017–2018**, rebound around **2021–2022**, quieter seasons mid-decade. :contentReference[oaicite:18]{index=18}  

- **Wildfire Size Trends (two-panel line plot)**
  - Top: total area burned per year – dominated by a few extreme seasons
    (2017, 2018, 2021, 2024).
  - Bottom: mean fire size with a smoothed line – spikes in the same extreme years. :contentReference[oaicite:19]{index=19}  

### 4.2. Distribution of fire sizes

- **Boxplots of `SIZE_HA` by year (log scale)**:
  - Most fires are **< 1 ha**.
  - A small number of very large fires create long upper tails.
  - Extreme years show higher upper whiskers / more mega-fires, not a huge shift in medians. :contentReference[oaicite:20]{index=20}  

### 4.3. Spatial patterns

- **Static faceted maps** (2013–2015, 2016–2019, 2020–2024):
  - Persistent concentration in **BC’s interior** (central plateau, southern interior).
  - Coastal regions relatively quiet.
  - Slight expansion north/east in later years. :contentReference[oaicite:21]{index=21}  

- **Interactive Leaflet map**
  - Zoom/hover to inspect individual fires: year, size, cause, period bucket.

### 4.4. Human vs Lightning

- **Frequency**
  - ~2,966 **human-caused** vs ~4,616 **lightning-caused** fires among known causes
    (~40% vs 60%). :contentReference[oaicite:22]{index=22}  

- **Size**
  - Medians for both causes are small.
  - Lightning fires have a **heavier upper tail**, with more fires in the 1,000–10,000 ha
    range – lightning is disproportionately responsible for the largest fires. :contentReference[oaicite:23]{index=23}  

- **Spatial**
  - Human fires cluster near populated areas/highways.
  - Lightning fires are more evenly spread across remote interior regions.

---

## 5. Repository Structure

```text
bc-wildfires-spatiotemporal-analysis/
├─ R/
│  ├─ 01_load_kmz_to_sf.R
│  ├─ 02_parse_html_attributes.R
│  ├─ 03_clean_and_deduplicate.R
│  ├─ 04_exploratory_plots.R
│  └─ 05_leaflet_maps.R
├─ data/
│  ├─ kmz_raw/         # Raw KMZ files (not tracked in Git, add to .gitignore)
│  ├─ bcws_stats.csv   # Scraped yearly summary statistics
│  └─ fires_cleaned.rds
├─ figs/
│  ├─ fire_counts_by_year.png
│  ├─ wildfire_size_trends.png
│  ├─ size_boxplots_by_year.png
│  └─ maps/
├─ report/
│  └─ DATA6200_A2_Report.pdf
└─ README.md
