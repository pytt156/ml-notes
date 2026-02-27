# Tensorer, reshape, broadcasting

## Tensorer

En **tensor** är en flerdimensionell datastruktur.

En:
- Skalar = 0D tensor
- Vektor = 1D tensor
- Matris = 2D tensor
- Högre dimensioner = 3D, 4D, nD tensorer

Man kan tänka på dem som generaliserade matriser.
I PyTorch används tensorer eftersom de:
- Kan köras effektivt på CPU och GPU
- Stöder automatisk differentiering (autograd)
- Är grundtypen för alla neurala nätverk

## Shape och skapande av tensorer
Exempel:
```python
x = torch.randn(2,3)
```
Shape = `(2,3)`
Det betyder:
- 2 rader
- 3 kolumner
- total 6 element
Antal element = produkten av dimensionerna

## Reshape

Man får endast reshapa om antalet element är oförändrat.
Exempel:
```python
x = torch.rand(2,3) # 6 element
y = x.reshape(3,2) # också 6 element
```
Det fungerar eftersom:
2 x 3 = 3 x 2 = 6

Reshape innebär **inte** att nya värden skapas. Det är bara en omorganisering av samma data i minnet.

Exempel:
```python
(2,3)
[[1,2,3],
[4,5,6]]
```
Kan bli:
```python
(3,2)
[[1,2],
[3,4],
[5,6]]
```
Men:
```python
x.reshape(4,2)
```
Ger fel eftersom 4 x 2 = 8 ≠ 6

## Specialfallet -1

Man kan låta PyTorch räkna ut en dimension:
```python
x.reshape(-1, 2)
```
Det betyder:
- Jag vill ha 2 kolumner
- Räkna själv ut hur många rader som krävs

Om x har 6 element blir resultatet `(3, 2)`

**Viktigt**: Endast **en** dimension får vara -1

## Flerdimensionella tensorer
Exempel:
```python
torch.randn(3,4,10)
```
Betyder:
- 3 block
- varje block har 4 rader
- varje rad har 10 tal

Totalt antal element:
3 x 4 x 10 = 120

Tensorer fungerar matematiskt likadant oavsett antal dimensioner.

## Varför reshape behövs i neurala nätverk
En `nn.Linear` förväntar sig input i formen: `(batch_size, features)`, alltså 2D. Men bilddata är ofta `(batch_size, channels, height, width)`, alltså 4D. Då behöver man "platta ut" alla dimensioner utom batch.

Exempel:
```python
(3,4,10) -> (3,40)
```
Vi behåller endast antalet datapunkter (3), men gör om 4x10 -> 40 features. Det kallas för **flattening**.