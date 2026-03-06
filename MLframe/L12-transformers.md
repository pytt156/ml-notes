
# Transformers och textmodeller

Transformers är en arkitektur för neurala nätverk som introducerades i artikeln **"Attention is All You Need"** _(Vaswani et. al., 2017)_. Arkitekturen utvecklades ursprungligen för maskinöversättning, men används idag i de flesta moderna språkmodeller (LLM:er) och även i många bildmodeller.

Grundidén är att modellen ska kunna **förstå relationer mellan ord i en sekvens** utan att behöva behandla texten strikt sekventiellt, vilket tidigare arkitekturer som RNN och LSTM gjorde.

## Tokens och representation av text

Datorer kan inte arbeta direkt med text. Därför måste text först **tokeniseras**.

En **token** är en liten textenhet, till exempel:
- ett ord
- en del av ett ord
- ibland ett tecken

Exempel:
> "I have a dog"

kan tekniseras till exempel

> ["I", "have", "a", "dog"]

Varje token konverteras sen till ett **numeriskt ID** i modellens vokabulär.

Det är dock inte dessa ID:n som modellen arbetar med. Token-ID:n omvandlas istället till **embeddings**.

## Embeddings

En **embedding** är en vektor av flyttalstal (t.ex. 768 eller 4096 dimensioner beroende på modell). Embeddings fungerar som en numerisk representation av tokenens betydelse.

Varje token projiceras till en punkt i ett **högdimensionellt vektorrum**.

I detta rum kan relationer mellan begrepp representeras geometriskt. Ord med liknande betydelse hamnar nära varandra i rummet.

Det gör att vissa semantiska relationer kan approximeras med vektoroperationer. Ett klassiskt exempel från embeddingsmodeller är:
>king - man + woman ≈ queen

Det betyder inte att modellen "förstår" språk i mänsklig mening, utan att statistiska relationer i träningsdatan har organiserats i vektorrumet.

Embeddings gör det alltså möjligt att behandla text **matematiskt**.

## Transformerararkutekturen

Den klassiska transformerararkitekturen består av två huvuddelar:
- Encoder
- Decoder

De är uppbyggda av stablade lager som använder **self-attention** och feed-forward-nätverk.

### Encoder

Encoderns uppgift är att ta en sekvens av tokens och skapa en **kontekstuell representation** av varje token.

Det innebär att varje ord representeras baserat på hela meningens kontext.

Till exempel i meningen: `"I have a dog"` kommer representationen av ordet dog att påverkas av de andra orden i meningen.

Detta sker via self-attention, där modellen lär sig vilka andra tokens i sekvensen som är viktiga för varje token.

Resultatet av encodern är alltså inte en enda meningspresentation utan en uppsättning kontextuella vektorer.

### Decoder

Decoderns uppgift är att generera en ny sekvens av tokens.

I maskinöversättning kan processen se ut såhär:
```
Encoder:  "I have a dog"
↓
Latent representation
↓
Decoder:  "Jag har en hund"
```
Decodern använder både:
- representationen från encodern
- tidigare genererade tokens

för att prediktera **nästa token i sekvensen**.

## Encoder-decoder som system
I uppgifter som maskinöversättning används hela systemet:
```
input text → encoder → representation → decoder → output text
```
Detta är den klassiska seq2seq-transformern.

## Decoder-only-modeller (LLM:er)
De flesta moderna stora språkmodeller, inklusive GPT-modeller, använder decoder-only-transformers.

I dessa modeller finns ingen separat encoder. Istället används en transformerstack som:
1. läser tidigare tokens
2. prediktar nästa token

Modellen tränas alltså på uppgiften: `givet en sekvens tokens -> predicta nästa token`

Exempel:
> "I have a" - > "dog"

Det kallas autoregressiv språkmodellering.

## GPT - Generative Pre-trained Transformer

- Generative - modellen kan generera text
- Pre-trained - modellen tränas först på mycket stora textkorpusar
- Transformer - arkitekturen baseras på transformer-modellen

Efter pretraining kan modellen ibland **finjusteras** eller styras via instruktionsträning.

## Temperaturer i textgenerering

När en språkmodell genererar text producerar den en **sannolikhetsfördelning** över möjliga nästa tokens.

Exempel:

| token | sannolikhet |
|-------|-------------|
| dog | 0.55 |
| cat | 0.2 |
| friend | 0.05 |
| banana | 0.001 |

Temeraturen är en parameter som styr hur denna **sannolikgetsfördelning används vid sampling**.
- **Låg temperatur (t.ex 0.2-0.5)**
    - modellen väljer nästan alltid de mest sannolika tokens
    - mer deterministiska och repetativa svar
- **Hög temperatur (t.ex 0.8-1.2)**
    - fler mindre sannolika tokens kan väljas
    - mer variation och kreativitet

Temperatur förändrar alltså fördelningens skärpa, inte modellens kunskap.

## Embeddings och semantisk likhet

Eftersom tokens representeras som vektorer kan man mäta semantisk likhet mellan texter genom avstång i vektorrummet, till exempel med:
    - cosine similarity
    - dot produkt

Detta används i många praktiska system, till exempel:
- semantisk sökning
- rekommendationssystem
- RAG

I dessa system lagras embeddings i en vektordatabas och kan jämföras matematiskt.

## Transformers i andra domäner

Transformers utvecklades för text, men används idag även inom andra områden.

Exempel: <br>
**Vision Transformers (ViT)** används för bildklassificering. Här delas en bild upp i mindre patchar som behandlas som tokens.

Transformers används också i:
- multimodala modeller (text + bild)
- taligenkänning
- proteinstrukturmodellerings