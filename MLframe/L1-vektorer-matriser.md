# Vektorer och matriser

## Vektorer i maskininlärning

En vektor kan matematiskt ses som en punkt i ett n-dimensionellt rum. Det är viktigt att förstå att "n-dimensionellt" inte betyder att vi kan visualisera det - det är en abstrakt struktur.

I maskininlärning får rummet ofta mer en **semantisk tolkning**.

Exempel:
- I NLP representeras ord som vektorer (embeddings).
- Två ord som betyder ungefär samma sak får vektorer som ligger nära varandra i rummet
- "Katt" och "hund" kan ligga nära
- "Katt" och "motorväg" ligger långt ifrån

Det betyder att avstånd och vinklar i rummet representerar betydelse.

En vektor är alltså:
- Matematiskt: en 1D-struktur
- I PyTorch: en 1D-tensor
- I ML: ofta en representation av något (ord, bild, datapunkt)

## Skalärprodukt (dot product)
Skalärprodukten är en av de mest centrala operationerna i linjär algebra.

Definition: $$a · b = a_1b_1 + a_2b_2 + ... + a_nb_n$$

Krav:
- Vektorerna måste ha samma dimension.

Vad betyder det egentligen?

När vi multiplicerar elementvis och summerar:
- Om två vektorer pekar i samma riktning -> stort positivt värde.
- Om de pekar i motsatt riktning -> negativt värde.
- Om de är ortogonala -> 0.

Skalärprodukten blandar:
- Riktning
- Magnitud

Det är därför den inte är ett rent likhetsmått, men den är byggstenen för många andra mått.

> **En Linear layer i ett neuralt nätverk är i praktiken en uppsättning skalärprodukter**

## L2-norm

L2-normen mäter hur lång en vektor är:
$$||a|| = \sqrt{a²_1+a²_2+...+a²_n}$$
Det är avståndet från origo.

Storleken spelar roll i många beräkningar:
- Om en vektor är 100 gånger större än en annan kommer den att dominera dot product
- I träning kan stora vikter leda till instabilitet
- I regularisering straffar man ofta stora vikter (L2-regularisering).

Normen gör det möjligt att:
- Jämföra storlekar
- Normalisera vektorer
- Stabilisera träning

## Cosinuslikhet

Cosinuslikhet kombinerar skalärprodukt och norm:
$$\frac{a·b}{||a||||b||}$$
Vi dividerar bort längden -> kvar blir endast riktningen.

Resultat:
- 1 -> exakt samma riktning
- 0 -> ortogonala
- -1 -> motsatt riktning

Det är därför man använder det i:
- Embeddings
- Söksystem
- Rekommendationssystem
- Dokumentlikhet

## Matriser och matrisvektor-multiplikation
En matris med dimension m x n betyder:
- m rader
- n kolumner

När vi multiplicerar: $(m × n)·(n)$ måste antalet kolumner matcha vektorns dimension. Resultatet blir en vektor med m element.

- Varje rad i matrisen gör en dot product med vektorn
- Resultatet staplas till en ny vektor

Det är exakt vad en Linear Layer gör.