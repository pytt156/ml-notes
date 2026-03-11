---
title: Understanding LLM
content: Sammanfattning av föreläsningsvideo av Karpathy på youtube
author: Daniela Algerydh
---

# Vad är en LLM?

En large language model är: **Ett neuralt nätverk tränat för att förutsäga nästa token i en textsekvens**.

Under träning får modellen enorma mängder text och lär sig statistiska mönster i språket. Den optimerar för att minimera fel i nästa-token-prediktion.

### Varför modellen verkar "förstå"
Trots att uppgiften bara är nästa-token-prediktion kan modellen verka intelligent. Orsaken är att:
- språk innehåller världskunskap
- resonemang och fakta uttrycks i text
- när modellen lär sig språkets struktur så lär den sig indirekt kunskap om världen

Det är därför en LLM kan:
- förklara koncept
- skriva kod
- resonera i flera steg

### Kärnpoängen
En LLM är inte:
- en databas
- ett söksystem
- ett medvetet system

Den är en probalistisk språkmodell som fortsätter textsekvenser baserat på träningsdata.

Detta enkla mål, kombinerat med mycket data och stora neurala nätverk, leder till de avancerade beteenden vi ser i moderna AI-system.

## Inlärning

### Internet som råmaterial
För att träna en språkmodell behövs enorma mängder text. Den vanligaste källan är webben. Ett centralt dataset är common crawl, som är en kontinuerlig genomsökning av stora delar av internet.

Det innebär att träningsdata kan innehålla:
- artiklar
- forum
- bloggar
- dokumentation
- kod
- böcker
- sociala medier

Det är alltså inte ett noggrant kurerat bibliotek, utan mer som en stor dump av internet.

### Rådata är inte användbart direkt

Webbdata innehåller myckey brus. Exempel:
- spam
- duplicerade texter
- maskinöversatt innehåll
- SEO-sidor
- skräpdata
- kodfragment utan kontext

Om man tränar direkt på detta får man en sämre modell. Därför sker datapreprocessing och filtering innan träning.

### Datarening (dataset curation)
Före träning filtreras datasetet i flera steg.

Vanliga metoder: <br>
**Deduplicering** <br>
Tar bort identiska eller nästan identiska texter.

**Språkfiltrering** <br>
Behåller texter på de språk modellen ska kunna.

**Kvalitetsfiltrering** <br>
Maskininlärningsmodeller används ibland för att avgöra om den text verkar vara "bra skriven".

**Spamfiltering** <br>
SEO-sidor och reklam försöks ta bort.

Resultatet är ett mycket mindre men mycket mer användbart dataset.

### Dataseten som används i praktiken
Efter filtering skapas dataset som kan användas för träning. Exempel som Karpathy nämner är dataset som:
- FineWeb
- The Pile
- andra kuraterade webbdatamängder

De kan innehålla hundratals miljarder till biljoner tokens.

### Varför datakvalitet är avgörande
En språkmodell lär sig mönster från datan.

Om data innehåller:
- fel
- bias
- låg kvalitet

så lär sig modellen samma mönster

Därför är datasetbyggande ofta en av de mest kritiska delarna i hela LLM-pipelinen.

## Tokenisering: text blir siffor

### Varför tokenisering behövs
Neurala nätverk kan inte läsa text direkt. Text måste därför först omvandlas till numeriska representationer. Det gör man genom tokenisering.

### Vad en token är
En token är en bit av text som modellen arbetar med.

Det kan vara
- ett helt ord
- en del av ett ord
- ett tecken
- skiljetecken

Exempel:
```
"unbelievable"
```
kan bli tokens som:
```
["un", "believ", "able"]
```
Det gör att modellen kan hantera:
- nya ord
- olika språk
- ovanliga uttryck

### Varför man inte använder hela ord
Om varje ord skulle ha varit en token skulle vokabulären bli enorm.
Istället använder man subword-tokenisering (t.ex BPE eller SentencePiece).

Fördelar:
- mindre vokabulär
- bättre generalisering
- modellen kan förstå okända ord genom delar

### Tokens blir siffor
Efter tokenisiering översätt varje token till ett ID i en vokabulär.

Exempel:
```
["The", "cat", "sat"]
```
kan bli:
```
[532, 1042, 781]
```
Det är de talen som skickas in i modellen.

### Varför tokens är viktiga i praktiken
Tokenisering påverkar:
- context window (max antal tokens modellen kan läsa)
- kostnad i API:er (debiteras ofta per token)
- prestanda på olika språk

Olika språk kan kräva olika många tokens för samma text.

## Transformers-arkitekturen

### Varför transformers används
Tidigare språkmodeller (t.ex RNN och LSTM) hade svårt att hantera långa textsekvenser. Transformers introducerades 2017 i artikeln "Attention is All You Need" och blev snabbt standardarkitekturen för LLM.

De löser två centrala problem:
- bättre hantering av långa beroenden i text
- massiv paralisering under träning

### Grundidén: attention
Transformer-modellen bygger på mekanismen self-attention.

Self attention innebär att varje token i en sekvens kan:
- "titta på" andra tokens
- avgöra vilka som är relevanta
- väga dem olika mycket

Exempel:
```
"The animal didn't cross the street because it was too tired"
```
Token "it" behöver förstå att den refererar till "animal". Attention-mekanismen hjälper modellen att koppla de orden.

### Hur ett transformerblock fungerar (enkelt)
Ett transformerblock innehåller i princip två delar:

Self-attention-lager
- beräknar relationer mellan tokens

Feed-forward-nätverk
- transformerar representationerna vidare

Många sådana block staplas ovanpå varandra (ofta tiotals till hundratals lager)

### Representationer (embeddings)
När tokens maras in i modellen konverteras de först till vektorer.
De vektorerna kallas embeddings och representerar:
- semantik
- relationer mellan ord
- kontext

Under modellens lager transformerar dessa representationer stegvis.

### Vad modellen faktiskt lär sig
Under träningen justeras modellens parametrar så att den blir bättre på att:
- förutsäga nästa token i en sekvens

Men eftersom attention kan koppla samman informationen över hela texten kan modellen också:
- lära sig grammatik
- lära sig semantik
- upptäcka strukturer i text

## Pre-training

### Självövervakad träning
Pretraining är den fas där modellen lär sig språkets struktur.
Träningen sker på enorma textdataset (webb, böcker, kod, artiklar).

Träningsuppgiften är enkel:
- förutsäg nästa token i texten

Exempel:
```
The capital of france is ___
```
Modellen justerar sina parametrar så att sannolikheten för "Paris" blir hög.

### Gradient descent
Under träningen:
1. modellen gissar nästa token
2. svaret jämförs med rätt token
3. felet beräknas (loss)
4. modellens vikter justeras

Den processen upprepas miljarder gånger och resultatet blir att modellen gradvis lär sig:
- grammatik
- semantik
- faktamönster
- kodstrukturer

### Skalan är avgörande
Moderna LLM:s tränas ofta på:
- hundratals miljarder till biljoner tokens
- miljarder parametrar

Träningen sker på stora GPU-kluster under veckor eller månader.

Skalan i:
- data
- modellstorlek
- bekräkningskraft

är en central anledning till att moderna modeller fungerar så bra.

### Vad modellen lär sig efter pre-training
Efter pre-training har modellen blivit bra på:
- att fortsätta text
- att imitera olika skrivstilar
- att reproducera kunskap från träningsdata

Men den är fortfarande inte optimerad för dialig eller assistent-beteende, det sker i nästa steg.

## Post-training
### Basmodellen räcker inte
Efter pre-training kan modellen forsätta text bra, men den är inte särskilt bra på att följa instruktioner.

Om man frågar något kan basmodellen t.ex:
- fortsätta texten istället för att svara
- ge flera möjliga fortsättningar
- skriva något helt annat än vad användaren vill

Därför krävs ett extra steg: **post-training**

### Instruction tuning
Först tränas modellen på dataset där instruktioner och svar finns i par.

Exempel:
```
Instruction: Explain what a neural network is.
Response: A neural network is a machine learning model...
```
Målet är att modellen ska lära sig:
- tolka instruktioner
- ge strukturerade svar
- bete sig mer som en assistent

### Human feedback (RLHF)
Nästa steg är ofta **Reiforcement Learning from Human Feedback (RLHF).**

Processen ser förenklat ut såhär:
1. människor jämför två modellsvar
2. de väljer vilket som är bättre
3. en reward-modell tränas på bedömningarna
4. huvudmodellen optimeras för att maximera reward

Det gör att modellen lär sig produera svar som människor uppfattar som:
- hjälpsamma
- tydliga
- säkra

### Alignment
Post-training används också för alignment, alltså att styra modellens beteende.

Det kan innebära att modellen lär sig att:
- vägra vissa typer av frågor
- formulera sig försiktigt kring osäker information
- undvika skadligt innehåll

## Inference

### Vad inference betyder
Inference är fasen där en tränad modell används för att generera text.
Processen är densamma som under träningens mål: **förutsäg nästa token**.
Skillnaden är att nu används modellen för att producera text istället för att lära sig.

### Token-för-token-generering
När du skickar en prompt händet det här:
1. prompten tokeniseras
2. modellen beräknar sannolikheter för nästa token
3. en token väljs
4. token läggs till i texten
5. processen upprepas

Det sker mycket snabbt och kan upprepas hundratalsgånger per svar.

### Sampling
Modellen väljer inte alltid den mest sannolika token.

Istället används ofta sampling, vilket innebär att nästa token dras från en sannolikhetsfördelning.

Det gör att texten:
- blir mer naturlig
- inte alltid identisk

### Temperature
Temperature styr hur slumpmässigt valet är.

Låg temperature:
- mer deterministiska svar
- mer repetativa

Hög temperature:
- mer variation
- större risk för fel

### Top-p / nucleus sampling
En annan metod är top-p sampling.

Här begränsas valet till de tokens som tillsammans står för en viss del av sannolikheten (t.ex 90%).

Det hjälper modellen att:
- undvika osannolika tokens
- fortfarande behålla variationen

## Varför LLM:er kan resonera
### Skalning ger nya förmågor
När modellen blir större (fler parametrar och med data), uppstår ibland nya beteenden som inte finns i mindre modeller. Det är det som kallas för _emergent abilities_.

Exempel på sånt som förbättras kraftigt med skala:
- logiskt resonemang
- kodgenerering
- komplexa instruktioner
- matematiska problem

Poängen är att de förmågorna inte alltid ökar gradvis utan ibland dyker de upp först efter en viss modellstorlek.

### Kontext spelar stor roll
Modellen arbetar alltid ibnom ett context-window, alltså den text den ser just nu. Allt den kan använda för att generera nästa token finns i:
- prompten
- tidigare genererade tokens

Det betyder att hur man formulerar prompten påverkar modellens prestation mycket.

### Chain-of-thought
En viktig observation är att modeller ofta presterar bättre om de uppmuntras att resonera steg-för-steg.

**Exempel:** <br>
Istället för att fråga:
```
What is 45 * sqrt(63/6)?
```
kan man skriva:
```
Solve step by step
```
När modellen skriver mellanled får den mer "arbetsyta" i kontexten.

Det förbättrar ofta:
- matematik
- logik
- planering

### Varför det fungerar
Eftersom modellen genererar text token för token, kan mellanled hjälpa modellen att:
- hålla fler resonemang i kontexten
- göra fler beräkningssteg innan slutsvaret

Det är därför tekniker som:
- chain-of-though prompting
- step-by-step reasoning

kan förbättra resultatet

## Hallucinationer och begränsningar
### Modellen optimerar sannolikhet, inte sanning
En LLM är tränad för att förutsäga nästa token, inte för att verifiera fakta. Det innebär att modellen försöker producera text som låter sannolik, inte nödvändigtvis text som är korrekt.

Om träningsdatan är oklar eller saknar information kan modellen därför fylla i luckor med något som verkar rimligt.

### Varför hallucinationer uppstår
Hallucinationer uppstår ofta när modellen:
- saknar tillräcklig kunskap om ett ämne
- försöker svara trots osäkerhet
- genererar text som statistiskt passa kontexten

Eftersom modellen är optimerad för flytande soråk kan svaret låta **mycket** övertygande även när det är **fel**.

### Kunskap är implicit, inte lagrad
Modellen innehåller inte en databas med fakta. Istället finns kunskapen i mönster i modellens parametrar.

Det gör att modellen:
- kan generalisera
- kan formulera nya svar

.. men också att exakta fakta ibland blir fel.

### Begränsningar i förståelse
En LLM har inte:
- medvetande
- verklig förståelse
- tillgång till extern verifiering

Den arbetar bara med den text som finns i den aktuella kontexten.

Därför kan modellen ibland:
- missa enkla saker
- göra logiska misstag
- ge självsäkra men felaktiga svar

## LLM:er som byggblock i riktiga AI-system
### En LLM räcker sällan ensam
En rå språkmodell är i grunden bara en textgenerator. För att bygga praktiska system måste man ofta kombinera modellen med andra komponenter.

Vanliga tillägg är:
- sökfunktioner
- databaser
- verktyg/API:er
- externa beräkningssystem

LLM:en fungerar då mer som en kontrollerande intelligens än som hela systemet.

### Retrieval Augumented Generation (RAG)
Ett vanligt sätt att förbättra faktakvalitet är RAG. Principen bakom rag är:
1. systemet söker relevant information i en databas
2. resultatet läggs in i promptens kontext
3. modellen genererar ett svar baserat på den informationen

Det används ofta för:
- företagsdokument
- kunskapsbaser
- supportsystem

### Verktyg och funktioner
Modeller kan också kopplas till verktyg, till exempel:
- kodexekvering
- webbsökning
- databasanrop
- API-integrationer

I de systemen kan modellen:
- tolka användarens fråga
- välja rätt verktyg
- formulera resultatet

### Agenter
Mer avancerade system använder AI-agenter. En agent kan t.ex:
- planera steg
- använda flera verktyg
- iterera tills ett mål uppnås

Det gör att modellen kan lösa mer komplexa uppgifter än en enkel fråga-svar-interaktion.

# Sammanfattning (TL:DR)

Videon förklarar hur stora språkmodeller (LLM:er) fungerar från grunden, från träningsdatan till hur de används i riktiga AI-system. En LLM är i grunden ett stort neuralt nätverk tränat för att **förutsäga nästa token i en textsekvens**. Text delas först upp i tokens och omvandlas till numeriska representationer som modellen kan bearbeta.

Själva modellen bygger på **transformer-arkitekturen**, där en mekanism kallad **self-attention** gör att modellen kan väga relationer mellan ord i en text. Under pre-training tränas modellen på enorma mängder text från internet och andra källor. Den lär sig då statistiska mönster i språk, vilket gör att den kan generera text, skriva kod och resonera om många ämnen.

Efter det följer **post-training**, där modellen finjusteras för att följa instruktioner och bete sig som en hjälpsam assistent, ofta med hjälp av mänsklig feedback.

När modellen används (inference) genererar den text **token för token** baserat på sannolikheter. Det gör att svar kan variera och ibland innehålla fel eller såkallade hallucinationer.

I praktiska AI-system används LLM:er sällan ensamma. De kombineras ofta med verktyg, databaser och retrieval-system för att skapa pålitliga och användbara AI-lösningar.