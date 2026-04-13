# LLM-grunder

## Text måste bli siffor

Det allra första man behöver förstå om språkmodeller är att en dator inte kan arbeta med text direkt. Inte på något meningsfullt sätt, iallafall. All text - varje bokstav, varje ord, varje mening - måste omvandlas till numeriska representationer innan modellen kan göra något alls med den.

Den fundamentala frågan kring hela fältet kretsar kring är: hur representerar man mänskligt språk som tal, utan att förlora det som gör språket meningsfullt?

Kvaliteten på representationen sätter ett tak för allt som händer senare. En fantastisk modellarkitektur med dåliga numeriska representationer som input ger fortfarande dåliga resultat. Det är lite som att ha en extremt skicklig kock men ge personen ett recept skrivet på ett språk hen inte förstår.

> **Kärninsikt**: Hela kedjan - *från raw text till modellens svar* - börjar med ett enda problen: hur kodar vi språklig betydelse som matematik?

## One-hot-encoding - och varför det inte räcker

Den enklaste tänkbara lösningen kallas one-hot-encoding. Idén är rak: ge varje ord i ordförrådet ett unikt index, och representera sedan varje ord som en lång vektor med ettor och nollor - en etta på ordets position, nollor överallt annars.

Om ordförrådet är 50 000 ord representeras till exempel "hund" som en vektor av längden 50 000 med en enda etta på position 1337 och nollor resten av vägen.

```python
import numpy as np

vocabulary = ["hund", "katt", "bil", "hus", "mat"]

def one_hot(word, vocab):
    vec = np.zeros(len(vocab))
    vec[vocab.index(word)] = 1
    return vec

print(one_hot("hund", vocabulary))  # [1. 0. 0. 0. 0.]
print(one_hot("katt", vocabulary))  # [0. 1. 0. 0. 0.]
print(one_hot("bil",  vocabulary))  # [0. 0. 1. 0. 0.]
```

Det fungerar tekniskt. Men det är konceptuellt ganska uselt. Titta på resultaten - de tre vektorerna är lika "långt ifrån" varandra. Dotprodukten (ett vanligt mått på likhet) är noll för varje par. Modellen har ingen som helst information om att "hund" och "katt" är mer lika varandra än "hund" och "bil".

Det finns två konkreta problem:

**Ineffektivitet**: Med ett ordförråd på 50 000 ord är varje vektor 50 000 element lång. Av dessa är 49 999 alltiod noll - ett enormt slöseri med minne och beräkning.

**Ingen semantik**: "Kung" och "drottning" är inte alls mer lika än "kung" och "cykel" - trots att vi intuitivt vet att de delar massor av egenskaper. All information om relationer, synonymer, kategorier och kontext är helt borta.

Det är de problemen som driver utvecklingen mot embeddings.

## Embeddings - språklig struktur

Embeddings löser problemet genom att representera ord som täta vektorer i ett kontinuerligt rum - vanligtvis med 100-1500 dimensioner - där avstånd och riktning faktiskt bär semantisk information.

Istället för "hund är position 1337" får vi "hund är ungefär punkten (0.42, -0.18, 0.71, ...)". De koordinaterna lärs automatiskt från data på ett sätt som gör att liknande ord hamnar nära varandra i rummet.

Likhet mäts vanligen med cosine similarity - vinkeln mellan två vektorer snarare än det euklidiska avståndet:
$$cos(θ) = (A · B) / (||A|| × ||B||)$$
Två vektorer som pekar i ungefär samma rikting har hög cosine similarity oavsett hur långa de är.

```python
from sklearn.metrics.pairwise import cosine_similarity
import numpy as np

# Påhittade embedding-vektorer (i verkligheten hundratals dimensioner)
hund = np.array([[0.8,  0.6, 0.1, -0.3]])
katt = np.array([[0.7,  0.7, 0.2, -0.2]])
bil  = np.array([[-0.2, 0.1, 0.9,  0.8]])

print("hund – katt:", cosine_similarity(hund, katt)[0][0])  # ~0.98
print("hund – bil: ", cosine_similarity(hund, bil)[0][0])   # ~0.12
```
det fascinerande är att dimensionerna *inte* är manuellt definerade. Modellen lär sig dem från data - vad varje dimension "betyder" är i princip okänt, men resultaten är ändå intuitiva.

### Word2Vec och vektoraritmetik

En klassisk demonstration av embedding-krafterna är att man kan göra meningsfull *matematik på ord* med vältränade embeddings (som Word2Vec från Google, 2013):

```
vektor("kung") - vektor("man") + vektor("kvinna") ≈ vektor("drottning")
```
Det visar att modellen lärt sig representera kön och royalitet som separata riktningar i rummet - inte för att någon programmerat det, utan för att dessa samband återspeglas i hur orden används i text.

> **Kom ihåg:** Embeddings-konceptet återkommer överallt i LLMOps - semantisk sökning, RAG och mycket mer bygger på exakt samma princip: mäta semantisk likhet via avstånd i vektorrymden.

## Tokens - modellen tänker inte i ord

Även om vi pratar om "ord" i vardagssammanhang så arbetar moderna språkmodeller egentligen med **tokens**. En Token är inte nödvädnigtvis ett ord:
| Typ | Exempel | Tokens |
|-----|---------|--------|
| Hela ord | "katt" | 1 token |
| Delord (subword) | "oväntat" | 2–3 tokens: "o", "väntat" |
| Skiljetecken | "!" | 1 token |
| Mellanslag + ord | " hund" | 1 token (mellanslaget inkluderat) |
| Sällsynta ord | "antidisestablishmentarianism" | 5–8 tokens |

Tekniken heter **Byte Pair Encoding (BPE)**. Vanliga ord och stavelser kodas som enstaka tokens, medan ovanliga ord delas upp i delar. Det gör ordförrådet hanterbart (30 000 - 100 000 tokens) utan att modellen blir hjälplös inför nya ord.

```python
import tiktoken

enc = tiktoken.encoding_for_model("gpt-4")

text = "Språkmodeller är fascinerande."
tokens = enc.encode(text)

print("Text:         ", text)
print("Token-IDs:    ", tokens)
print("Antal tokens: ", len(tokens))  # Inte samma som antal ord!

# Dekoda tillbaka för att se tokenuppdelningen
for t in tokens:
    print(repr(enc.decode([t])), end=" | ")
```
---
> **Praktisk konsekvens**: Kostnad för API-anrop mäts i tokens, inte ord. En enkel tumregel: ungefär 1.3-1.5 tokens per ord på engelska, lite mer på svenska (längre ord -> flera subword-tokens). Systemprompten räknas med i varje anrop. Långa konversationer blir snabbt dyra.
---

## Transformers-arkitekturen

År 2017 publicerade Google-forskaren artikeln *"Attention is all you need"* - och det är inte en överdrift att säga att den förändrade fältet permanent. Transformer-arkitekturen som introducerades där är grunden för i princip alla moderna stora språkmodeller: GPT-serien, Claude, Gemini och alla andra.

Föregångarna - framförallt RNN (Recurrent Neural Networks) och LSTM - behandlade text sekventiellt. De läste ett ord i taget och försökte bär informationen framåt i ett "tillstånd". Det fungerade hyfsat för korta meningar, men hade enorma problem med långa beroenden: vad "hon" syftar på i slutet av en långst stycke var svårt att hålla reda på om referensen kom 200 ord tidigare.

```
RNN (sekventiellt):
w₁ → w₂ → w₃ → ... → wₙ
      ↑ svårt att komma ihåg w₁ när man är vid wₙ

Transformer (parallellt):
        [ Attention ]
       ↗  ↗  ↗  ↗
w₁  w₂  w₃  w₄      ← alla tokens ser varandra direkt
```

Transformers genombrott var att **sluta bearbeta text sekventiellt**. Istället låter den alla tokens i sekvensen "titta" på alla andra tokens direkt, parallellt, via attention-mekanismen. Det löser problemet med lång beroenden och gör det dessutom möjligt att tränga mycket snabbare (parallellisering på GPU).

En transformer-modell är uppbyggd som ett antal staplade *lager*. Varje lager består (förenklat) av:
- **Multi-head self-attention** - låter varje token vikta alla andra tokens
- **Feed-forward nätverk** - appliceras på varje token separat
- **Layer normalization och residual connections** - träningsstabilitet

GPT-4 gar rapporterats ha ~97 lager och ~1.8 biljoner parametrar. Det är extremt många laget av just den typen av bearbetning.

## Attention - så tolkar modellen kontext

Attention är kärnan i del hela, och det är värt att förstå intuitivt hur den fungerar. Tanken är enkel men kraftfull: **ett ords betydelse beror på dess kontext**.

Ta ordet "cool". I "han är en riktigt cool kille" betyder det ungefär "häftig". I "luften var cool och frisk" betyder det kallt. Samma token, helt olika innebörd. Attention löser detta.

Tekniskt fungerar det så att varje token genererar tre vektorer:
- Query (Q): vad letar jag efter?
- Key (K): vad erbjuder jag?
- Value (V): vad är mitt faktiska innehåll?

Vikterna beräknas som dotprodukter mellan query och keys, normaliseras med softmax, och används för att beräkna ett viktat medelvärde av values.

```python
import torch
import torch.nn.functional as F
import math

def scaled_dot_product_attention(Q, K, V):
    # Q, K, V: [seq_len, d_k]
    d_k = Q.shape[-1]

    # Beräkna attention-scores: varje token mot alla andra
    scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(d_k)

    # Normalisera till sannolikheter (summa = 1 per rad)
    weights = F.softmax(scores, dim=-1)

    # Viktat medelvärde av value-vektorer
    output = torch.matmul(weights, V)
    return output, weights

# I praktiken: flera "huvuden" (multi-head) körs parallellt
# och concateneras. Varje huvud kan lära sig olika typer av relationer.
```
Exempelvis: när modellen processar "satt" i "katten satt på mattan" lär den sig att lägga hög vikt (t.ex 0.72) på "katten" - eftersom subjektet är det mest relevanta för att förstå vad som satt.

En viktig poäng: modellen "förstår" inte kontext i mänsklig mening. Den lär sig statistiska mönster - vilka kombinationer av ord som brukar förekomma i liknande sammanhang. Men resultatet är ändå imponerande kontextkänsligt.

## Nästa token-prediktion
Trots all komplexitet i arkitekturen är grunduppgiften under träningen förvånadsvärt enkel: **givet alla tidigare tokens, förutsäg nästa token**.

Det kallas *self-supervised-learning* - och det är genialt ur ett dataperspektiv. All text på internet är i princip träningsdata, utan att man behöver manuellt labela något. Varje mening är automatiskt ett träningsexempel: indata är alla ord utom det sista, utdata är det sista ordet.

```
Input:  "Solen lyser starkt"  →  Modell  →  P(nästa token):
                                              "mot"  0.34
                                              "och"  0.29
                                              "idag" 0.22
                                              "på"   0.09
                                              ...

→ Sampla "mot", lägg till sekvensen, upprepa.
```

Det är viktigt att förstå att modellen inte genererar hela svaret på en gång. Den genererar *en token i taget*, lägger till det i sekvensen, och anropar sig själv igen. Processen kallas *autoregressive generation*. Det är därför streaming i chat-gränssnitt visar text som dyker upp ord för ord - det är bokstavligen hur modellen arbetar.

## GPT och förträning

GPT - Generative Pre-trained Transformer - är Transformern i en specifik konfiguration: en *decoder-only* transformer tränad med nästa-token-prediktion på enorma mängder text.

"Pre-trained" syftar på det första stora träningssetet: modellen tränas på hundratals gigabyte till terabyte text - böcker, Wikipedia, webbsidor, kod, vetenskapliga artiklas - och lär sig statistiska mönster i mänskligt skrivande.

Något intressant händer när man skalar upp den processen. Modellen som träns på tillräckligt mycket data och med tillräckligt många parametrar börjar uppvisa förmågor som ingen explicit programmerat in. Det kallar **emergenta förmågor** (emergent capabilities).

> **Exempel på emergens**: GPT-2 (2019) hade svårt att summera texter. GPT-3 (2020) med 100x fler parametrar kunde plötsligt summera, översätta och skriva kod - utan att ha tränats specifikt på de uppgifterna. Samma arkitektur, bara större.

Varför händer det? Det är ett aktivt forskningsområde, men en vanlig förklaring är att tillräckligt komplex förståelse av ett fenomen (som text) kräver komplex förståelse av relaterade fenomen. En modell som verkligen förstår text på djupet måste förstå avsikter, orsak och verkan, logisk slutledning - för det är vad text handlar om.

## Temperature

Varje gång modellen ska generera ett token beräknar den en sannolikhetsfördelning över alla möjliga nästa tokens. Temperature låter oss justera hur "spetsig" eller "platt" den fördelningen är.

```
Temperature = 0.2 (låg):         Temperature = 1.5 (hög):
hund  ████████████████  0.82      hund  ████████  0.34
katt  ███  0.12                   katt  ███████  0.29
mus   █    0.04                   mus   █████    0.22
fisk  ·    0.02                   fisk  ████     0.15

→ nästan alltid "hund"            → varierat, kan välja vad som helst
  förutsägbart, stabilt             kreativt, oförutsägbart
```
Temperature påverkar *hur* modellen väljer bland möjliga svar - inte *vad* den vet. En hög temperature gör inte modellen smartare, bara mer varierad (och ibland mer "hallucinerande").

Tumregel i praktiken:
- **Temperature 0** för deterministiska, faktabaserade uppgifter (kod, analys, extraktion)
- **Högre temperature (0.7-1.2)** för kreativt skrivande
- De flesta produktions-API:er kör med temperature runt 0-0.3

## RLHF - från språkmodell till chattbot

En rå GPT-modell är inte automatiskt bra på att hjälpa människor. Den är tränad för att *förutsäga text*, inte att vara hjälpsam. Om man ber den "förklara kvantfysig för ett barn" kan den lika gärna fortsätta med en vetenskaplig avhandling som med en enkel förklaring - det är vad som förekommer mest i träningsdatan.

Det är här RLHF (Reinforcement Learning from Human Feedback) kommer in. Det är det träningssteg som förvandlar en rå språkmodell till en chattbot som faktiskt lyssnar på instruktioner.

```
1. Förträning          2. Supervised FT       3. Reward model        4. RL-steg
┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐
│  GPT på stor    │ → │ Träna på        │ → │ Människor       │ → │ PPO-optimering  │
│  textkorpus     │   │ demonstrationer │   │ rangordnar      │   │ mot reward      │
│                 │   │                 │   │ svar A vs B     │   │                 │
└─────────────────┘   └─────────────────┘   └─────────────────┘   └────────┬────────┘
                                                      ↑                     │
                                                      └─────────────────────┘
                                                         upprepa
```

Kortfattat: mänskliga labelers tittar på par av modellsvar och väljer vilket som är bäst. De preferenseran tränas in i en separat *reward model*. Sen används reinforcement learning (typiskt PPO - Proximal Policy Optimization) för att optimera språkmodellen att generera svar med hög reward.

Det är det steg som gör att moderna chattmodeller är bra på att:
- Följa instruktioner ("svara på svenska", "skriv en lista")
- Undvika skadligt innehåll
- Be om förtydliganden när frågan är oklar
- Svara mer strukturerat och användbart

*RLHF är inte perfekt utan introducerar egna problem - "sycophancy" (att modellen lär sig att smickra snarare än att vara korrekt) är ett välkänt och dokumenterat exempel. Aktivt forskningsområde.*

## Begränsningar och verktyg

LLM:er har strukturella begränsningar som är viktiga att förstå - inte som svagheter att undvika, utan som egenskaper att designa kring.

| Begränsning | Varför | Lösning i praktiken |
|---|---|---|
| Dålig matematik | Aritmetik är inte ett statistiskt mönster - det kräver exakt symbolisk beräkning | Anropa en kalkylator som verktyg (tool calling) |
| Föråldrad kunskap | Träningsdatan har ett cutoff-datum | Web-sökning, aktuella databaser (RAG) |
| Hallucinationer | Modellen genererar sannolika tokens, inte nödvändigtvis sanna påståenden | Grounding mot källor, verifiering, låg temperature |
| Begränsad kontextlängd | Attention är O(n²) i sekvensens längd - dyrt för långa texter | RAG, chunking, summarization |

Det viktigaste konceptuella skiftet: ett produktionssystem är *aldrig* bara en modell. Det är alltid modell + verktyg + kontext.

`praktiskt system = modell + verktyg + kontext`

RAG (Retrieval-Augmented Generation) är ett av de vanligaste mönstren: istället för att modellen ska "komma ihåg" fakta så hämtar man relevant information från en databas och lägger in den i prompten. Modellen behöver inte veta allt - den behöver bara förstå det den får presenterat.