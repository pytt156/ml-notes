## Grundstruktur ML

### 1. Problemformulering
I övervakad maskininlärning (supervised learning) försöker man lära en funktion (f:X→y), det betyder: givet indata (x), förutsäg målvariablen y.

### 2. Datarepresentation

**X-features**
- Representeras som en matris
- Dimension: (n_samples, n_features)
- Varje rad = en datapunkt (observation)
- Varje kolumn = en feature (variabel)

Exempel:
```
X = [[5.1, 3.5, 1.4, 0.2], [4.9, 3.0, 1.4, 0.2] ... ]
```

**y-target (label)**
- Representeras som en vektor
- Dimension: (n_samples)
- En output per rad i X

Exempel:
```
y = [0, 0, 2, 1, ...]
```

### 3. Vad gör modellen?

Vid träning (`fit`):
> "Anpassa dina interna parametrar så att du kan generalisera till ny data."

Det betyder matematiskt:
- Hitta parametrar θ
- Minimera en loss-funktion

Exempel: linjär regression (y=kX+m)
Här är:
- k = lutning
- m = intercept
- Modellen lär sig dessa från data

### 4. Prediktion
Vid `predict`:
> "Använd de lärda parametrarna för att ge output för ny data"

Det är bara: ŷ​=f(Xnew).
Ingen mer inlärning sker.

### 5. Vad betyder en rad?
En rad i X representerar:
- I tabulär data -> en individ / observation
- I bilddata -> en bild (ofta flattened till en lång vektor av pixlar)

Exempel för bild:
En 28x28-bild -> flatten -> 784 features -> en rad i X.


### Slicing i NumPy (tabulär data, sidenote)

En dimension (vektor)
```python
arr[2:]     # från index 2 till slutet
app[:1]     # fram till (men exklusive) index 1
```
Två dimensioner (matris)
```python
array[rader, kolumner]

# Exempel:
X[:, 0]     # alla rader, första kolumnen
X[:5, 1]    # rader 0-4, kolumn 1
X[3:8, :]   # rader 3-7, alla kolumner
```

**Grundregel:**
Före komma = rader
Efter komma = kolumner