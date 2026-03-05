# JAX

När man ska spara och köra en tränad modell finns det flera nivåer av optimering. Det handlar dels om hur modellen representeras (modellformat), dels om hur beräkningen exekveras (ramverk och runtime). ONNX och JAX har liknande intuitiva processer - där de kör en forwardpass och optimerar utifrån det, men det är viktigt att skilja på exempelvis ONNX, som är ett modellformat för interoperabilitet, och JAX som är ett beräkningsramverk för träning och numerisk beräkning.

## ONNX - modellrepresentation och portabilitet

När en modell exporteras till `ONNX` sparas den som ett beräkningsdiagram (computational graph). Diagrammet består av:
- **Operatorer** (t.ex, matris-multiplikation, konvolutioner, aktiveringsfunktioner)
- **Parametrar** (vikter och bias)

Poängen är inte att alla beräkningar är gjorda i förväg. Vi inferens måste modellen fortfarande utföra sina matematiska operationer. Det som gör **ONNX** användbart är i stället att en runtime kan optimera hur grafen körs. Exempel på sådana optimeringar är:
- **Operator fusion** (flera operationer slås ihop till en effektivare kernel)
- **Bättre minneshantering**
- **Hårdvaruacceleration** (GPU, specialiserande inference-motorer)

Det innebär att `ONNX` främst används när man vill:
- Köra modeller i olika miljöer
- Göra inferens mer portabel
- Separera träningsramverk från inferensmiljö

## JAX - ett ramverk för numerisk beräkning och träning

**JAX** fyller en helt annan roll. Det är ett Pythonbibliotek för numerisk beräkning och maskininlärning som är designat för att göra gradientbaserad optimering effektiv.

En central idé i JAX är att man skriver kod som funktioner, ofta i en stil som liknar matematiska funktioner.

Exempel:
```math
f(x) = kx + m
```

I vanlig Python kan funktioner innehålla tillstånd, sidoprocesser och andra effekter. I JAX försöker man istället skriva rena funktioner, där resultatet endast beror på inputen.

Det gör det möjligt för JAX att applicera olika programtransformationer på funktioner.

## Viktiga mekanismer i JAX
### Automatisk differenstiering

JAX kan automatiskt beräkna derivator av funktioner.<br>
Det görs via funktioner som: `jax.grad()`

Detta används i praktiken för att beräkna gradienter i backprop.

### JIT-kompliering
En annan central mekanism är Just-In-Time-kompliering: `jax.jit()`

När `jit` används händer följande:
1. JAX tracar funktionen och analyserar vilka operationer som används.
2. Ett beräkningsdiagram byggs.
3. Diagrammet komplieras via XLA till optimerad maskinkod.
4. Den komplierade versionen cacheas.

Det innebär att första körningen ofta är långsammare (kompliering sker), men senare körningar kan bli betydligt snabbare.

### Varför blir JAX snabbare?
Det är viktigt att förstå att JAX **inte** förberäknar alla resultat.

Forward pass måste fortfarande köras varje gång.

Det som förbättras är istället **hur beräkningen exekveras**.

Prestandavinster kommer främst ifrån:
- Minskad **Python-overhead**
- Operatorfusion och grafoptimeringar
- Bättre **minnesplanering**
- Effektiv **GPU/accelerator-exekvering**

### JAX i träningsloopar
I ett typiskt träningssteg sker följande:
```text
forward pass
-> loss-beräkning
-> gradientberäkning
-> parameteruppdatering
```

I JAX lam hela detta steg komplieras till ett enda optimerat beräkningsblock.

Det betyder **inte** att vikterna räknas ut en gång, vikterna uppdateras fortfarande interaktivt varje träningssteg.

Det som optimeras är exekveringen av träningssteget.

### Flexibilitet och användningsområden
JAX är mycket flexibelt eftersom:
- Funktioner är första klassens object
- Programtransformationer kan kombineras (`grad`, `jit`, `vmap`, etc)

Det gör att JAX ofta används i forskningsmiljöer, där man vill experimentera med nya arkitekturer eller optimeringsmetoder.

### Kombinera ramverk efter vad de är bra på

I praktiken kan man ibland kombinera olika ramverk i olika delar av en ML-pipeline. Anledningen är att olika ekosystem är starka på olika saker.

Till exempel:
- **scikit-learn** är mycket bra för klassisk preprocessing (t.ex scaling, feature transforms, train/test split, pipelines)
- **PyTorch eller TensorFlow** är starka ekosystem för att definiera modeller, använda färdiga lager och integrera med dataloaders och träningsverktyg.
- **JAX** kan vara mycket effektivt när man vill optimera själva träningssteget, eftersom funktioner kan komplieras med `jit` och köras som optimerade beräkningsgrafer.
- **ONNX** används ofta för export och inferens, eftersom det finns många runtimes och optimerade motorer för att köra ONNX-modeller.

### Exempel på ett möjligt hybridflöde

Ett möjligt arbetsflöde kan därför se ut så här:

```
preprocessing / data split
(scikit-learn)
    ↓
modellarkitektur
(PyTorch)
    ↓
träningsloop
(JAX + jit)
    ↓
export
(ONNX)
    ↓
inferens
(ONNX Runtime / annan runtime)
```

Poängen i ett sådant upplägg är att använda varje verktyg för det det är bäst på:
- Klassisk datahantering i scikit-learn
- Modellstruktur i ett DL-ramverk
- Snabb träningsloop via JAX-kompliering
- Portabel och optimerad inferens via ONNX

### Viktig nyansering
Det här är möjligt eftersom alla dessa verktyg i grunden arbetar med:
- tensorer / numeriska arrayer
- beräkningsgrafer
- gradientbaserad optimering

Men i praktiken innebär ett sådant upplägg också extra intefrationsarbete, till exempel:
- konvertering mellan tensorformat
- checkpoint-hantering
- kompatibilitet mellan ramverk

Därför väljer många produktionsteam ett mer enhetligt flöde, t.ex:
```
PyTorch → TorchScript / ONNX → inference
```
eller
```
JAX → XLA runtime
```

Hybridlösningar förekommer främst när man vill maximera prestanda eller flexibilitet i forskning eller experimentella pipelines.