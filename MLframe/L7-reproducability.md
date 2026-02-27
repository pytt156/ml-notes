# Reproducerbarhet

Om vi kör samma kod flera gånger får vi inte exakt samma resultat om vi inte kontrollerar slumpen.

Slump påverkar:
- Initiering av vikter
- Shuffling av data
- Dropout

För reproducerbarhet:
```python
import torch, numpy as np, random

torch.manual_seed(42)
np.random.seed(42)
random.seed(42)
```
Full reproducerbarhet kräver dessutom:
- Samma data
- Samma kodversion (git)
- Samma beroenden
- Samma hårdvaruförutsättningar (GPU/CPU kan ge små skillnader)

---

# DVC - versionshantering av stora filer
Git är inte gjort för stora modellfiler eller dataset.

DVC används tillsammans med git för att:
- Spåra stora filer
- Lagra dem i extern storage (t.ex S3)
- Versionera dem utan att checka in dem i git

Arbetsflödet är:
```bash
dvc add data/raw/dataset.csv
git add dataset.csv.dvc
git commit -m "Track dataset with DVC"
dvc push
```
Efter `git pull` måste man köra `dvc pull` för att hämta själva datan.

DVC ersätter inte Git utan kompletterar det.