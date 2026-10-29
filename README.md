<div align="center">

# Moroccan Medicines API

A REST API that serves Morocco's national reference list of medicines (CNOPS) from MongoDB: names, active ingredients, dosage, packaging, public price and reimbursement rate.

![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.4-6DB33F?style=flat-square&logo=springboot&logoColor=white)
![Java](https://img.shields.io/badge/Java-17-ED8B00?style=flat-square&logo=openjdk&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=flat-square&logo=mongodb&logoColor=white)
![Apache POI](https://img.shields.io/badge/Apache_POI-D22128?style=flat-square)

<sub>Personal project · 2024 · All rights reserved</sub>

[Endpoints](#endpoints) &nbsp;·&nbsp; [Data](#data) &nbsp;·&nbsp; [Getting started](#getting-started)

</div>

## Overview

The public reference list of medicines is published as a spreadsheet, which is hard to use from other software. On startup, a background thread downloads that spreadsheet from the Moroccan open data portal (data.gov.ma), converts it to JSON with Apache POI and Jackson and loads the 5,917 entries into MongoDB. It repeats every seven days, so the API stays in sync with the published list. Simple REST endpoints then let a pharmacy or health app look medicines up by name or by product code.

## Endpoints

| Method | Path | Returns |
|---|---|---|
| `GET` | `/medicines` | Every medicine in the list |
| `GET` | `/medicines/name/{nom}` | Medicines with exactly this name |
| `GET` | `/medicines/code/{code}` | One medicine by its product code (EAN) |

Each endpoint answers `404 Not Found` when nothing matches.

```http
GET /medicines/code/6118001230068
```

```json
{
  "CODE": "6118001230068",
  "NOM": "URO / EAU POUR IRRIGATION",
  "DCI1": "EAU POUR PREPARATION INJECTABLE",
  "DOSAGE1": "3000",
  "UNITE_DOSAGE1": "ML",
  "FORME": "SOLUTION POUR IRRIGATION",
  "PRESENTATION": "1 POCHE 3 L",
  "PPV": 95.0,
  "PH": 0.0,
  "PRIX_BR": 95.0,
  "PRINCEPS_GENERIQUE": "P",
  "TAUX_REMBOURSEMENT": "0%"
}
```

The response also includes the document's MongoDB `id`.

## Data

The source is the CNOPS reference list of medicines (2014 edition) published on [data.gov.ma](https://data.gov.ma). A copy is kept in the repository as both `.xlsx` and converted `.json`.

| Field | Meaning |
|---|---|
| `CODE` | Product barcode (EAN-13) |
| `NOM` | Brand name |
| `DCI1`, `DOSAGE1`, `UNITE_DOSAGE1` | Active ingredient (INN), strength and unit |
| `FORME`, `PRESENTATION` | Pharmaceutical form and packaging |
| `PPV`, `PH`, `PRIX_BR` | Public price, hospital price and reimbursement base price (MAD) |
| `PRINCEPS_GENERIQUE` | `P` for the original brand, `G` for a generic |
| `TAUX_REMBOURSEMENT` | Reimbursement rate |

## Project structure

```
API_MED/medicals/
├── pom.xml
└── src/main/java/com/medicals/api/
    ├── MedicalsApplication.java
    ├── core/                 # Medicine document, repository, service and REST controller
    └── threads/Handler/      # Downloads the spreadsheet and converts it to JSON
        ├── xlsx/             # Source spreadsheet
        └── json/             # Converted data
```

## Getting started

**Requirements**: Java 17, Maven, and MongoDB running locally.

```bash
cd API_MED/medicals
./mvnw spring-boot:run      # connects to mongodb://localhost:27017/Pharmacy
curl http://localhost:8080/medicines/name/DOLIPRANE
```

The download and conversion paths in `MedicalsApplication.java` are absolute paths from the development machine; change them to your own location before running.

## License

Copyright 2024 Yassine Aitaouicha. All rights reserved. See [LICENSE](LICENSE).

---

<div align="center"><sub>Part of <a href="https://github.com/creepout777">Yassine Aitaouicha's projects</a></sub></div>
