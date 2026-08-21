# Higher Education Institutions in Norway Dataset

## Overview

This repository produces a consolidated dataset of Norwegian higher education institutions (HEIs), which can serve as a target list for a web security measurement campaign. It combines two Excel files stored in the `Source/` folder into a single CSV file and applies a few normalization steps so that institutional attributes can be analyzed consistently.

In the context of the master’s thesis, _An Assessment of Web-Related Security in Norwegian Higher Education Institutions_, this dataset supports the definition of target institutions and services, and enables regional analysis using NUTS codes, as required by the research questions on maturity variation and regional asymmetries.

## Repository structure

- `Source/HEIs/Norway HEIs.xlsx`  
  Enriched list of Norwegian HEIs, including categorisation fields and the main institutional website.

- `Source/NUTS/Norway.xlsx`  
  NUTS 2026 codes and labels used to attach regional identifiers to each institution.

- `Script/main.py`  
  Interactive script that loads both sources, performs normalisation and joins, and exports the final dataset.

- `no-heis-2026.csv`  
  The generated dataset, committed at the repository root. Re-running the script
  recreates it; the default output name in the script prompt is `no-heis-2026.csv`.

- `LICENSE`  
  Licence covering this dataset and the build script.

## How to generate the dataset

Run the script from the repository root:

```bash
python Script/main.py
```

The script prints a small file tree for `Source/HEIs` and `Source/NUTS`, marks Excel files, lets you choose which files to merge, and then prompts for an output name. Press Enter to accept the default.

## Output schema

The generated CSV contains the following columns:

- `ID`
- `Name`
- `Category`
- `Institution_Category_Standardized`
- `Member_of_European_University_alliance`
- `url`
- `NUTS2`
- `NUTS2_Label`
- `NUTS3`
- `NUTS3_Label`

### Column descriptions

#### Core identifiers

- **`ID`**  
  Unique identifier of the institution in the HEIs source.

- **`Name`**  
  Official name of the institution.

- **`url`**  
  Main institutional website, used as the primary measurement target in the thesis scanning pipeline.

#### Normalised institutional attributes

- **`Category`**  
  Normalised legal status, derived from the HEIs source “Legal status” definition.

- **`Institution_Category_Standardized`**  
  Normalised institutional type, derived from the HEIs source “Institution Category” definition.

- **`Member_of_European_University_alliance`**  
  Indicator of whether the institution is part of a European Universities Initiative alliance, based on European Commission sources.

These fields enable the thesis to compare security posture across institution types, and to explore whether specific organisational characteristics correlate with stronger or weaker web security configurations.

#### Regional attributes

- **`NUTS2`**, **`NUTS2_Label`**  
  NUTS level 2 code and label, used to group institutions by macro region.

- **`NUTS3`**, **`NUTS3_Label`**  
  NUTS level 3 code and label, used to group institutions by county style subdivisions, and to support finer-grained regional comparisons.

## Conversions and normalisation rules

The script converts integer codes into descriptive values so the CSV can be analysed directly.

### Category (Legal status)

- `1` → `Public`
- `0` → `Private`

### Institution_Category_Standardized (Institution Category)

- `1` → `University`
- `2` → `Specialised University`
- `3` → `University College/University of Applied Sciences`
- `4` → `University College with accredited study programmes`

### Member_of_European_University_alliance

- `1` → `Member of a European Universities Initiative alliance`
- `0` → `Not found in European Commission sources`

## Script behaviour, and why it matters

The script exists to make the dataset reproducible and auditable. Instead of manually editing spreadsheets, it provides a consistent, repeatable pipeline that:

1. Loads the selected HEIs Excel file and the selected NUTS Excel file.
2. Selects the relevant fields for the output schema.
3. Applies the categorical conversions described above.
4. Merges NUTS codes and labels into the institution rows.
5. Writes a single CSV file that can be ingested by the web security scanning and analysis tooling.

In practical terms, this script is important because the thesis depends on a well-defined target list. The subsequent measurement campaign, for example HTTPS and TLS posture, DNSSEC adoption, and HTTP security header deployment, requires stable inputs such as a canonical domain per institution and stable region labels for aggregation. A scripted build step reduces the risk of accidental inconsistencies, and supports later re-runs when the sources, or the institutional landscape, change.

## Relation to the master’s thesis scope

The thesis focuses on measuring and analysing web-related security for Norwegian HEIs, with emphasis on:

- HTTPS and TLS configuration quality
- DNSSEC adoption
- HTTP response security headers

The dataset produced here is the inventory that connects institutions to their main web domain and to their region. That mapping is necessary to:

- Execute the measurement pipeline consistently across all targets.
- Analyse maturity differences across institution categories.
- Evaluate regional asymmetries through NUTS2 and NUTS3 groupings.
- Support repeatable scoring and longitudinal comparisons.

## Sources and references

### HEIs

- <a href="https://www.regjeringen.no/en/dep/kd/organisation/kunnskapsdepartementets-etater-og-virksomheter/Subordinate-agencies-2/state-run-universities-and-university-co/id434505/">Norwegian Ministry of Education and Research, “Universities and University Colleges”</a>
- <a href="https://eurydice.eacea.ec.europa.eu/eurypedia/norway/types-higher-education-institutions">Eurydice, “Types of higher education institutions” (Norway)</a>
- <a href="https://www.nokut.no/en/higher-education/higher-education-institutions/">NOKUT, “Higher education institutions”</a>

### European Universities Initiative (European Commission)

- <a href="https://education.ec.europa.eu/education-levels/higher-education/european-universities-initiative/map">European Universities Initiative map</a>

### NUTS

- <a href="https://www.ssb.no/en/klass/klassifikasjoner/508">Statistics Norway KLASS, NUTS code list for Norway</a>
