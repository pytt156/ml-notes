# Kursanteckningar – Maskininlärningsramverk

## Övergripande bild: vad labbarna tillsammans bygger

Samtliga laborationer ingår i ett sammanhängande ML-flöde:

Miljö → Modell → Integration → Distribution

- Miljö: var och hur koden körs, beroenden, reproducerbarhet  
- Modell: data → träning → utvärdering → prediktion  
- Integration & distribution: API, container, samarbete i team  

Labbarna överlappar konceptuellt, men examineras separat och har olika fokus.

---

## Struktur och progression mellan labbarna

### Lab 1 (L1) – Robust miljöhantering  
**Individuell – Deadline: 13 februari**

**Syfte**  
Att säkerställa att du kan sätta upp en isolerad, reproducerbar ML-miljö som fungerar på din maskin och kan verifieras automatiskt.

**Fokus**
- Miljö, inte modell
- Reproducerbarhet, inte prestanda
- Verifiering, inte funktionalitet

**Kärnidé**  
Skapa en plats där all kod kan köras korrekt, innan vi bryr oss om vad den gör.

**Innehåll**
- Skapa projekt med `uv init`
- Hantera beroenden via `pyproject.toml` och `uv.lock`
- Installera:
  - PyTorch (med korrekt index för hårdvara)
  - Scikit-learn
  - Pandas
  - Jupyter
- Verifiera miljön via `check_env.py`

**Verifieringsskriptet ska**
- Skrivas i Python
- Köras med `uv run check_env.py`
- Kontrollera:
  - Python- och paketversioner
  - Om GPU/accelerator finns tillgänglig
  - Utföra minst en enkel tensoroperation

**Mål**
- Kunna köra tensorberäkningar på GPU om tillgängligt, annars CPU  
  (t.ex. `device = "cuda" if torch.cuda.is_available() else "cpu"`)

---

### Lab 2 (K2) – PyTorch pipelines & modellträning  
**Individuell – Deadline: 27 februari**

**Syfte**  
Att bygga en komplett Deep Learning-pipeline i PyTorch med fokus på struktur, modularitet och reproducerbarhet.

**Fokus**
- Kodstruktur
- Träningspipeline
- Experiment och jämförelser

**Kärnidé**  
Allt som behövs för att träna en modell ska vara tydligt, versionerat och körbart från en startpunkt.

**Tekniska krav**
- PyTorch-struktur med:
  - Egen `Dataset`
  - `DataLoader`
  - `nn.Module`
- Dataset versionshanteras med DVC  
  (rådata ska inte ligga i Git)
- Minst tre experiment med olika hyperparametrar
- Hela flödet startas via `main.py`

**Kodstruktur (exempel)**
- `dataset.py`
- `model.py`
- `train.py`
- `main.py`

**README ska innehålla**
- Tabell: hyperparametrar vs resultat
- Kort analys av skillnader mellan experimenten

---

### Lab 3 (M3) – Integration & distribution  
**Parprojekt – Deadline: 12 mars**

**Syfte**  
Att simulera ett professionellt MLOps-arbetsflöde: samarbete, kodgranskning, integration och containerisering.

**Fokus**
- Samarbete i Git
- Modell → API → container
- Produktionsnära tänk

**Kärnidé**  
En tränad modell ska kunna användas av andra system utan att de behöver veta hur den är byggd.

**Innehåll**
- Välj en fungerande PyTorch-modell (förslagsvis från K2)
- Exportera modellen till:
  - ONNX eller
  - TorchScript
- Skapa API med FastAPI
  - Endpoint: `POST /predict`
  - Tar JSON och returnerar prediktion
- Containerisera med Docker
  - Installera beroenden med `uv`
- Samarbete:
  - Feature branches
  - Code reviews
  - Pull Requests

**README ska innehålla**
- Länkar till minst två Pull Requests
- Dokumenterad kodgranskning

---

## Examination och betyg

### Godkänd (G)
- Alla krav uppfyllda i samtliga labbar
- Teknisk funktion enligt checklistor
- Reproducerbar körning

### Väl godkänd (VG)
Utöver G:
- Allt inlämnat i tid
- Projekt går att köra direkt (`run` utan handpåläggning)
- K2: tydlig modularitet
- M3: genomarbetade code reviews
- Optimering och felhantering
- Utförlig README med:
  - Resonemang
  - Designval
  - Reflektion: vad fungerade, vad kunde gjorts bättre

---

## Användning av LLMs

- LLMs är tillåtna
- Krav:
  - Du ska kunna förklara:
    - hur lösningen fungerar
    - varför den fungerar
    - varför den inte fungerar i vissa fall
- Copy-paste utan förståelse är inte förenligt med VG-nivå

---

## Kursens övergripande mål

Målet är inte att lära sig ett specifikt verktyg perfekt.

Målet är att:
- Kunna lösa nya problem med:
  - befintlig kunskap
  - dokumentation
  - rätt verktyg
- Förstå vad som:
  - måste sitta i huvudet
  - kan slås upp utan att tappa kvalitet
- Bygga en mental verktygslåda som håller när:
  - ramverk ändras
  - API:er byts ut
  - nya verktyg introduceras

Ju bredare och stabilare grund, desto färre problem upplevs som “mystiska”.