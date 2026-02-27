# Intro till Deep Learning

## 1. Vad är Deep Learning?
Deep learning är en metod inom maskinlärning där man använder neurala nätverk med flera lager för att approximera komplexa funktioner.

Måler är fortfarande detsamma som i klassisk ML:
$$f(X) = y$$
Skillnaden är hur funktionen $f$ modelleras.

## 2. Från klassisk ML till neurala nätverk
I traditionell ML:
- Man väljer features $X$
- Modellen hittar ett samband mellan $X$ och $y$
Exempel (linjär regression): $$y = kX +m$$
Det fungerar om sambandet är relativt enkelt.

### Problem
Om sambandet är icke-linjärt och komplext (t.ex bildklassificering) räcker inte en enkel modell.

Exempel:

- Vikt = 5 kg
- Höjd = 30 cm

Det räcker inte för att avgöra om det är:
- Hund
- Katt
- Räv

Relationen mellan input och klass är mer komplex än en enkel linje.

## 3. Neuralt nätverk - struktur
Grundstruktur:
$$Input -> Hidden layers -> Output$$

** Input layer **
- En nod per feature
- Varje rad i datan = en observation

Om vi har:
- vikt
- höjd
- färg

-> 3 input-noder

**Output layer**

Antal noder = antal klasser

Exempel:
5 djurarter -> 5 output-noder

Vid klasifikation används ofta softmax, som ger sannolikheter:
```
[0.80, 0.05, 0.03, 0.07, 0.05]
```
Tolkning:
80% sannolikhet att det är en hund

## 4. Vikter och bias
Varje koppling mellan två noder har en vikt.

Om vi har:
- 2 input-noder
- 5 input-noder

-> 2x5 = 10 vikter

Detta kan skrivas:
$$z = Wx+b$$

Där:
- $W$ = viktmatris
- $x$ = input
- $b$ = bias

Bias är en extra parameter som lärs under träning.

## 5. Forward pass
Forward pass:
1. Ta input
2. Multiplicera med vikter
3. Lägg till bias
4. Applicera aktiveringsfunktion
5. Upprepa genom alla lager

## 6. Backpropogation
Under supervised learning har vi facit.
1. Räkna loss
2. Beräkna gradienter
3. Uppdatera vikter

Detta sker via $gradient$ $descent$

Backpropagation = effektiv metod att beräkna alla gradienter baklänges genom nätverket.

## 7. Hidden layers

Det är hidden layers som gör modellen "deep"

De möjliggör:
- Icke-linjära transformationer
- Representation learning

Det är inte en black box ren matematiskt, men tolkningen är svår.
Antal noder i hidden layers är en hyperparameter som vi väljer.

## 8. Tensorer
En tensor är en flerdimensionell array.

|   Dimension   |   Namn    |
|-------|-----------|
|   0D  |   Skalär  |
|   1D  |   Vektor  |
|   2D  |   Matrix  |
|   3D+ |   Tensor  |

Exempel:
```python
0D: 7
1D: [5,2]
2D: [[2,3], [5,7]]
3D: [[[1,4], [1,2]],[[8,3],[1,2]]]
3D+: [[[[[...]]]]]
```

## 9. Normalisering
Varför normalisera?
- Stabilare träning
- Mindre risk för exploding/vanishing gradients
- Snabbare konvergens

Standardisering: $$Xscaled = \frac{x - μ​}{σ}$$

i PyTorch:
`transforms.Normalize(mean, std)`
Mean och standardavvikelse måste matcha datasetet.

## 10. Overfitting och Regularisering
Om modellen tränas för länge:
- Den anpassar sig för exakt till träningsdatan
- Dålig generalisering

Vanliga lösningar:
- L2-regularisering (weight decay)
- Dropout
- Early stopping

Regularisering betyder att man lägger till ett straff i lossfunktionen: $$Losstotal=Lossdata+λ||W||²$$
Det gör at vikterna hålls mindre = mindre överanpassning.