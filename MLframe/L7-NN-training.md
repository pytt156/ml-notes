# Träning av neurala nätverk, optimering och utvärdering

## Neuralt nätverk: lager, noder och kopplingar
### Input lager
- Input lagret har lika många noder som antal _features_ (kolumner) i datan
- En rad i datan (en observation) matas in som en vektor: $$x \in \mathbb{R}^{\text{antal\_features}}$$
### Output lager
- **Klassifikation**: output-lagret har ofta lika många noder som antal klasser. Ex: 3 klasser -> 3 output-noder
- **Regression**: output-lagret har ofta 1 nod (ett numeriskt värde)

### Noder och sannolikgeter (klassifikation)
- En nod innehåller ett **värde** (aktivering/logit beroende på var vi är i nätet)
- Vid klassifikation vill man ofta kunna tolka output som sannolikheter. Då används vanligtvis **softmax** på sista lagret (antingen explicit eller inbyggt i loss):
    - Softmax gör att summan av output blir 1(100%):$$\sum_{k=1}^{K} p_k = 1$$

- I PyTorch används ofta `nn.CrossEntropyLoss`, som tar logits och göt softmax internt. Då ska man normalt **inte** lägga softmax själv under träning.

### Kopplingar (edges) och vikter
- I ett fullt kopplat (dense) nät går det en koppling från varje nod i ett laget till varje nod i nästa lager
- Exempel:
    - 4 noder i lager 1
    - 3 noder i lager 2
    - antal kopplingar = 4 × 3 = 12
- Varje koppling har en **vikt**. Vikterna är det som lärs under träningen.

## Forward pass vs. backprop
### Forward pass
1. Man tar input till lagret (en vektor)
2. Multiplicerar med vikter (matrismultiplikation)
3. Lägger till bias
4. Applicerar aktiveringsfunktion (t.ex ReLU)
5. Skickar vidare till nästa lager

Ett typiskt lager (dense) kan skrivas: $$z = xW^T + b$$ $$a = \phi(z)$$

Där:
- $x$ = input
- $W$ = viktmatris
- $b$ = bias
- $\phi$ = aktiveringsfunktion
- $a$ = output/aktivering från lagret

### Backprop
Efter forward pass har vi en output. Då:
1. Vi jämför modellens output med facit (targets) och räknas **loss**
2. Vi räknar ut hur varje vikt bidrog till felet genom derivator
3. Vi uppdaterar vikterna så att loss blir mindre nästa gång

Backprop kan man tänka sig som en "blame"-beräkning:
- Vilka vikter gjorde mest "fel"?
- Åt vilket håll ska de justeras för att minska loss mest?

Backprop är själva **beräkningen** av gradienterna, det är optimizer som sen använder gradienterna för att faktiskt ändra vikterna.

## Batch, epochs

### Batch
En **batch** är en delmängd av datasetet som skickas in till modellen åt gången.
- Batch size = antal datapunkter per batch
- Varje batch ger normalt en viktuppdelning (en "step")

### Epoch
En **epoch** = modellen har sett hela träningsdatasetet en gång.

### Antal uppdelningar per epoch
Om datasetet har $N$ datapunkter: $$ \text{steps per epoch} = \frac{N}{\text{batch\_size}} $$

Exempel: $N = 1000$
- Batch size = 10
    -> 10 000/10 = 1000 steps per epoch
    -> 10 epochs = 10 000 uppdateringar
- Batch size = 100
    -> 10 000/100 = 100 steps per epoch
    -> 10 epochs = 1000 uppdateringar

### Varför spelar batch size roll?
- Mindre batch -> fler uppdateringar, mer "brus" i gradienterna, kan ibland generalisera bättre men kan vara instabilt
- Större batch -> färre uppdateringar, stabilare gradient men kan kräva annan LR och kan ge sämre generalisering i vissa fall
