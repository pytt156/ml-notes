# Litet neuralt nätverk

## Linear layer - vad den gör
`nn.Linear(4,10)` skapar:
- Viktmatris med shape (10,4)
- Bias med shape (10,)

Den gör beräkningen: $y = xWᵀ + b$

För varje datapunkt:
- 4 tal in
- 10 nya tal ut

Varje output är en viktad summa av alla input-värden + bias.

## ReLU
`nn.ReLU()` Funktion: `ReLU(x) = max(0, x)`

Den gör modellen icke-linjär. Utan icke-linjäritet skulle flera Linear-lager i rad vara matematiskt ekvivalenta med ett enda Linear-lager.

## Sista lagret och logits
`nn.Linear(10,3)`
- 10 tal in 
- 3 tal ut

Det tre talen kallas **logits** (output)

## CrossEntropyLoss
`criterion = nn.CrossEntropyLoss`

Den:
1. Applicerar `Softmax` internt
2. Beräknar negativ log-likelihood
3. Jämför med rätt klass (heltalsindex)

Viktigt:
- Targets måste vara dtype=torch.long
- Man ska **inte** själv lägga på Softmax före loss

## Datatyper
```python
x = torch.tensor(X, dtype=torch.float32)
y = torch.tensor(y, dtype=torch.long)
```
Varför?
- Nätverket använder float
- CrossEntropyLoss kräver heltalsklasser (long)

## Optimering
`optimizer = optim.Adam(model.parameters(), lr 0.01)`

- `model.parameters()` = alla vikter och bias
- `lr` = steglängd
- **Adam** = adaptiv gradientbaserad metod

## Träningsloopen - vad som händer

```python
for _ in range(n_epochs)

    optimizer.zero_grad()
    outputs = model(X)
    loss = criterion(outputs, y)
    loss.backward()
    optimizer.step()
```

### 1. optimizer.zero_grad()
Varje parameter i modellen har:
- `param.data` = själva vikten
- `param.grad` = gradienten från föregående backprop

Pytorch **adderar gradienter** varje gång man kör `.backward()`, så utan `zero_grad()` skulle gradienterna ackumuleras.

Matematiskt: $gtotal​=g1​+g2​+g3​+...$

Ibland är det avsiktligt (t.ex gradient accumulation), men inte i standardträning.

### 2. Forward pass
```python
outputs = model(X)
```
Här sker:
1. Data passerar genom första linear: $$z_1​​=XW_1^T​+b_1​$$
2. ReLU: $$a_1 = max(0, x_1)$$
3. Sista linear: $$logits = a_1W_1^T + b_2$$

Pytorch bygger samtidigt en beräkningsgraf (**computational graph**).

Det betyder att varje operation sparar:
- Vilka tensorer som används
- Hur de används
- Hur gradienten ska beräknas bakåt

Det är grunden för **automatisk differentiering**.

### 3. Loss-beräkning
```python
loss = criterion(outputs, y)
```
För klassificering med CrossEntropyLoss:
Intern sker: $Softmax(logits)$ och sen: $- log(p_{correct})$ för **varje** datapunkt.

Lossen är ett enda skalärt värde: `loss.shape == torch.Size([])`. Det är viktigt: Backdrop startar alltid från en skalär.

### 4. Backpropagation
```python
loss.backward()
```
Detta är den centrala mekanismen.

PyTorch gör: $$\frac{∂Loss}{∂w}$$ för varje parameter w.

**Hur?**

Kedjeregeln (chain rule): $$\frac{∂L}{∂W_1}⋅\frac{∂L}{∂logits}⋅\frac{∂logits}{∂a_1}⋅\frac{∂a_1}{∂z_1}⋅\frac{∂z}{∂W_1}$$

Det sker bakåt genom grafen. Resultatet lagras i `param.grad`.

Ingen vikt uppdateras här, endast gradienterna beräknas.

### 5. optimizer.step()
Nu uppdateras vikterna.

En förenklas gradient descent: $$w := w - lr ⋅ \frac{∂L}{∂w}$$
Adam gör mer:
- håller rullande medelvärde av gradienter
- håller rullande medelvärde av kvadrerade gradienter
- normaliserar stegen

Men principen är samma - vi rör oss i den riktningen som minskar loss.

## Vad händer över flera epochs?

En epoch betyder: Hela datasetet passerar genom modellen en gång.

Efter varje epoch:
- Vikterna är lite bättre anpassade.
- Loss bör minska (om learning rate är rimlig)

## Vad betyder gradienten (intuitivt)?
Om: $$\frac{∂L}{∂w}= 5$$
Betyder det:
- Om vi ökar vikten med 1, så ökar lossen med ungefär 5.
- Alltså vill vi minska vikten.

Om: $$\frac{∂L}{∂w} = -2$$
Betyder det:
- Om vi ökar vikten med 1, så minskar lossen med 2.
- Alltså vill vi öka vikten.

Gradienten anger alltså:
- Riktning
- Känslighet

## Accuracy
```python
preds = torch.argmax(outputs, dim=1)
```
Detta väljer: $$\arg\max_{j}\,\mathrm{logits}_j$$
Alltså klassen med högst råpoäng.

Accuracy: $$\frac{\text{antal korrekta}}{\text{antal datapunkter}}$$
**MEN** - Accuracy säger inget om:
- hur säker modellen var
- klassobalans
- kalibrering

## Systemförståelse (ramverksnivå)

Det som gör PyTorch till ett ML-ramverk är:
1. Tensorberäkningar
2. Automatisk differentiering
3. Modulärt lager-API (nn.Module)
4. Optimeringsalgoritmer
5. Beräkningsgraf

Träningsloopen är egentligen bara:
- Bygg graf
- Beräkna loss
- Differentiera graf
- Uppdatera parametrar
- Upprepa

## Vanliga fel i träningsloopar

1. Glömma `zero_grad()`
2. Lägga Softmax före CrossEntropyLoss
3. Fel dtype på targets
4. För hög learning rate
5. Inte använda `model.train()` / `model.eval()` vid dropout/batchnorm

## Sammanfattning träningsloop

Varje epoch gör:
1. Approximerar gradienterna av förlustytan
2. Tar ett litet steg i negativ gradientriktning
3. Upprepar tills vi når:
    - lokal minimipunkt
    - sadelpunkt
    - eller fastnar
