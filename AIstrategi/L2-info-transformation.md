# Gen AI som verktyg för informationshämtning och kunskapstransformation

Internet gjorde information tillgänglig och Generativ AI gjorde den mer begriplig. Den stora nyttan ligger inte i att modellen "vet", utan i att den kan hjälpa toll med att transformera stor mängder rått material till något som går att arbeta med. Samtidigt är det viktigt att skilja mellan data, information och kunskap. Välformulerad text är **inte** samma sak som sann kunskap.

## Data, information och kunskap
**Data** är råa observationer utan tydlig tolkning eller sammanhang. Det kan vara siffor, loggar, textfragment, transkiptioner eller enskilda kundkommentarer.

**Information** uppstår när data organiseras, struktureras eller sätts i sammanhang. Då blir det möjligt att se mönster, skillnader och samband.

**Kunskap** kräver ytterligare ett steg: tolkning, erfarenhet och användning i en konkret situation. Först där kan man svara på frågan: _Vad bör vi göra och varför?_

Det här är en central styrka hos en Gen AI. En modell kan snabbt sammanfatta, jämföra, extrahera och klassificera stor mängder data. Den är alltså stark i över gången från **data** till **information**. Men övergången från information till kunskap kräver mer än språkförmåga, där behövs kontext, omdöme och förståelse för situationen. Därför kan en modell låta övertygande även när den bygger på felaktig, vinklad eller ofullständig information.

### Exempel: kundfeedback
Om en organisation får in kommentarer som _"Appen krashar när jag laddar upp bild"_ och _"Bilduppladdning fungerar inte på Android"_ är det först bara **rå data**. När kommentarerna sammanställs och man ser att många klagomål gäller bilduppladdning och att majoriteten kommer från Android-användare har man **information**. **Kunskap** uppstår först när det tolkas i sitt sammanhang, t.ex: en ny version av appen verkar ha introducerat en bugg i Androids bilduppladdning.

### Exempel: Bildanalys
Rå statisti över sidvisningar, klick och konverteringar är **data**. När analysen visar att trafiken ökade med 40% men att konverteringen sjönk från 5 till 2 procent blir det till **information**. **Kunskap** uppstår när det tolkas som att den nya landningssidan lockar fler besökare men gör att sämre jobb med att få dem att genomföra önskar handling.

## Strukturerade instruktioner till modeller och agenter
Bra resultat från GenAI bygger ofta på tydliga instruktioner. En användbar struktur är att specifiera:
- **bakgrund eller kontext**: varför uppgiften görs och vilket sammanhang den ingår i
- **uppgift eller objective**: exakt vad modellen ska göra
- **målgrupp**: vem resultatet är till för
- **ton och stil**: hur svaret ska låta
- **outputformat**: hur resultatet ska presenteras, t.ex som markdown, tabell eller sammanfattning

Det här är särskilt viktigt när man bygger agenter för återkommande arbete. Ju tydligare ramarna är, desto mindre risk att modellen producerar relevant språk, men fel sorts innehåll.

När man itererar på en instruktion är det ofta bättre att starta en ny kontext än att fortsätta i samma chatt. I en lång konversation läser modellen in allt tidigare innehåll igen, vilket kan skapa oönskade sidoeffekter eller blanda ihop gamla och nya mål. Samma kontext är däremot användbar när tidigare material faktiskt behövs för att lösa uppgiften.

## Prompt chaining
**Prompt chaining** innebär att man delar upp den komplex uppgift i flera steg istället för att försöka lösa allt i en enda prompt. Det är ofta effektivare eftersom flera delmoment kräver olika typer av bearbetning. Först kan man samla in material, sen strukturera det, därefter förklara det, och till sist applicera det i ett konkret sammanhang.

En typisk kedja för att förstå ett svårt ämne eller en forskningsrapport kan se ut såhär:
1. Hitta relevant material
2. Samla mer data genom att be om liknande innehåll eller fördjupning
3. Dela upp materialet i logiska delar och be om flera sammanfattningar istället för en enda
4. Behåll först den tekniska precisionen och komplexiteten
5. Gör sedan förklaringen personlig genom att ange bakgrund, kunskapsnivå och användningsområde
6. Applicera till sist innehållet i ett konkret mål, tillexempel beslut, planering eller lärande

En bra princip är att inte förenkla för tidigt. Om originalmaterialet är tekniskt och komplext bör den första bearbetningen försöka bevara innehåll och jargong. Förenkling och pedagogisk anpassning blir starkare efter att strukturen och innehållet redan har fångats upp.

En med generell kedjestruktur kan beskrivas såhär:
```
Expandera → Strukturera → Prioritera → Fördjupa → Applicera → Personalisera → Simulera
```
Det är en användbar modell för studieteknik till affärsidéer, programmeringsinlärning och förhandling.

## GenAI är inte bara text
Många modeller är starkast i språk, men informationshämtning är inte begränsad till text. Material kan hämtas från PDF:er, bilder, ljud, videos, transkriptioner, poddar och andra format. Det viktiga är inte formatet i sig, utan att informationen kan göras tillgänglig för analys och tolkning.

På samma sätt kan output anpassas till olika former. Resultatet kan vara en klassisk textsammanfattning, men också exempelvis bildgenerering, tal, eller ett mer pedagogiskt format som gör innehållet lättare att ta till sig. Grundtanken är att information kan representeras på många sätt, och att rätt representation beror på användarens mål.

## Tools: kopplingen mellann modellen och verkliga system
En språkmodell är bar på språk och mönsterigenkänning, men den har normalt inte direkt tillgång till extern data eller förmåga att utföra handlingar på egen hand. För det behövs **tools**, alltså kopplingar till externa funktioner som webbsökning, databaser, dokumenthämtning, kodkörning eller API-anrop. Systemet kör själva anropet och skickar tillbaka resultatet, som modellen sedan använder i sitt svar.

Vanliga exempel är:
- webbsökning för färsk information
- RAG (retreival-augumented generation), där modellen hämtar relevant information ur filer eller kunskapsbaser
- kodkörning för beräkningar och databehandling
- funktionsanrop och API-anrop för att hämta eller skicka data

Det är viktigt av tre skäl: för det första kan modellen arbeta med aktuell information istället för att gissa utifrån gammal träningsdata. För det andra blir beräkningar mer tillförlitliga när de görs i kod istället för fri text. För det tredje möjliggörs automatisering - en agent kan inte bara svara, utan också utföra moment i ett arbetsflöde. Tools minskar också hallucinationer eftersom modellen får tillgång till hårda fakta eller dokument, databaser eller kod. Men risken för feltolkning försvinner inte helt. Modellen kan fortfarande läsa verktygsresultat på fel sätt.

### Säkerhet och guardrails
Tool-användning kräver skyddsmekanismer. Indata från verktyg måste behandllas som opålitlig tills den har verifierats. Ett dokument, en webbsida eller användarinlägg kan innehålla instruktioner som försöker manipulera modellen, till exempel genom prompt injection. Därför behövs filtrering, begränsade behörigheter, logging, spårbarhet och mänsklig kontroll vid känsliga steg. Särskilt åtgärder som att skicka mail, radera data eller fatta högriskbeslut bör alla ligga bakom _human-in-the-loop_.

## Anpassade agenter
En anpassad agent är särskilt användbar när samma typ av uggift återkommer. Det kan handla om att sammanfatta dokument på ett visst sätt, skriva anteckningar i en bestämd struktur eller hjälpa till med lärande enligt en återkommande metod. Då är det effektivt att bygga in instruktionerna i agenten istället för att skriva om varje gång.

För pågående projekt med gemensam kontext kan det vara bättre att arbeta i en miljö där filer, källor och tidigare arbete följer med över tid. Valet mellan en specialiserad agent och ett mer kontexttungt projekt beror alltså på om problemet är återkommande eller om sammanhanget är det viktigaste. Kontextlängt och tokenbudget spelar också roll, eftersom de avgör hur mycket material agenten kan ta in och bearbeta.

Ett typiskt upplägg för en dokumentagent kan beskrivas såhär:
```
CONTEXT: analysera och sammanfatta studier och rapporter i PDF-format
OBJECTIVE: dela upp dokumentet i logiska avsnitt och sammanfatta varje del utförligt
STYLE: strukturerad och detaljerad
TONE: exakt och tydlig
AUDIENCE: person som studerar IT och behöver vidare bearbetning
RESPONSE FORMAT: markdown
```
Poängen med en sån struktur är att göra agentens uppgift tydlig redan från början, så att resultatet blir konsekvent och användbart.

## Kunskapstransformation i organisationer
Många organisationer har inte brist på information, utan ett överskott. Dokument, mejl, möten, tickets, loggar och rapporter skapar ett konstant inflöde av text och data. Flaskhalsen är ofta människans begränsade tid för att läsa, jämföra, minnas och prioritera. Här kan generativ AI fungera som ett destilleringslager mellan informationsmängden och den mänskliga analysen.

Målet med sådan destillering är inte att kasta bort innehåll, utan att reducera brus och lyfta fram det väsenliga. En bra kunskapstransformation ska bevara mening, kontext och relevans samtidigt som den minskar mängden material som en människa behöver ta sig igenom.

### Vad LLM:er gör bra
LLM:er är särskilt bra på att:
- sammanfatta långa texter
- jämföra flera källor samtidigt
- identifiera mönster, teman och avvikelser
- anpassa resultatet till olika målgrupper, tillexempel chef, tekniker eller kund

### Vad LLM:er inte gör bra
De är däremot inte i sig bra på:
- sanning och ansvar
- kontext utanför den text de faktiskt får
- omdöme, prioritering och konsekvensbedömning utan tydliga instruktioner

Därför bör AI användas som filter, inte facit. Den kan hjälpa till att reducera informationsmängden, prioritrera vad som är värt att läsa och skapa bättre beslutsunderlag. Men i högriskbeslut måste mänsklig granskning vara det sista steget.

### Exempel på kunskapstransformtion
I **operativt och administrativt** arbete kan AI sammanfatta långa mejl-trådar till beslut, öppna frågor och nästa steg, skriva möteranteckningar från videomöten eller skapa statusrapporter från projektverktyg.

Inom **kundsupport** kan den sammanfatta kundhistorik, klustra återkommande problem och översätta tekniska felsökningar till kundanpassade svar.

Inom **teknik och drift** kan den sammanfatta loggar, identifiera mönster i incidenter, skriva release notes från commits och ge översikter av systemarkitektur.

I **analys- och kunskapsarbete** kan den jämföra rapporter, identifiera gemensamma slutsatser, hitta trender i fritext och skapa beslutsunderlag genom att väga alternativ mot varandra.

Två återkommenade arkitekturmönster syns i sådana system:
- Ett automatiserad flöde där data hämtas, extraheras, modereras, sammanfattas, formatteras och sen används i en utåtgående process, t.ex ett nyhetsbrev.

- Ett fråge- och svarssystem där material först traskiberas eller laddas upp, passerar ett säkerhetslaget, hämtas från flera datakällor och sedan används för att bygga kontext inför ett svar.

I båda fallen är poängen densamma: AI fungerar som ett lager som omvandlar rå information till handlingsbar struktur.

## Risker och Etik
Riskerna kan delar in i tre huvudområden: **kvalitet**, **organisation** och **användning**.

### Kvalitetsrisker
- **Hallucinationer**: modellen fyller i luckor som om de vore fakta
- **förlorad kontext**: viktiga nyanser filtreras bort
- **överförenkling**: komplexa frågor reduceras för hårt
- **partiskhet**: snedvridningar från träningsdata eller källmaterial påverkar resultatet

En praktiskt konsekvens är att man inte bara behöver granska slutsatserna, utan också vad som eventuellt har utelämnats. En sammanfattning kan vara språkligt stark men ändå missa det som faktiskt är mest relevant.

### Organisatoriska risker
- **integritet**: känslig eller personlig information kan sammanfattas fel eller hanteras olämpligt
- **sekretess**: data kan användas i fel sammanhang
- **lagar och regler**: det kan vara svårt att säkerställa efterlevnad om processerna är otydliga

### Risker i användningen
- **överförtroende**: AI-output behandlas som sanning
- **otydligt ansvar**: det blir oklart vem som äger beslutet
- **brist på spårbarhet**: svårt att veta vilka källor som användes
- **automatisering utan kontroll**: särskilt farligt i högriskbeslut

## Principer för ansvarsfull användning
Ansvarsfull användning bygger på några grundprinciper_
- human-in-the-loop i kritiska steg
- tydliga instruktioner och tydliga begränsningar
- källor, transperens och verifiering
- AI som beslutsstöd, inte beslutsfattare

Ett konkret exempel är att alltid kontrollera citat, referenser och källhänvisningar när precision är viktig. Modeller kan formulera falska referenser på ett mycket övertygande sätt. Därför måste trovärdighet bedömas genom faktiskt kontroll, inte genom språkets självsäkerhet.

## Blir vi smartare eller dummare av GenAI?

_**Det beror på hur tekniken används**_. Generativ AI kan fungera som **scaffolding**, alltså ett stöd som hjälper användaren att förstå, träna och tänka bättre. Men den kan också fungera som answer vending, där användare bara hämtar färdiga svar utan att själv bearbeta innehållet. Utfallet beror därför mindre på verktyget i sig och mer på arbetsformen.

När användningen blir passiv finns en risk att kognitiv avlastning övergår i kognitivt bortfall. Man tränar mindre på att formulera sig, resonera, minnas och bearbeta information på djuper. Då kan välskrivna svar skapa en **falsk känsla av förståelse**.

Forskningen som lyfts i materialet pekar också på blandade effekter: AI kan i vissa upplägg kopplas till **sämre kritiskt tänkande**, medan den i andra sammanhang höjer produktivitet och **stödjer lärande**, särskilt för mindre erfarna användare.

När AI används aktivt kan den istället frigöra tid till analys, jämförelse och beslut. Den kan visa mönster, ge exempel, förklara svåra begrepp och hjälpa nybörjare att se hur experter strukturerar problem. Då förstärker den människans tänkande istället för att ersätta det. Den praktiska slutsatsen är att AI gör mest nytta när den används för att stödja förståelse, reflektion och träning, **inte** när den används som ersättning för dem.

# Sammanfattning - TL:DR
GenAi är framförallt stark på att omvandla stora mängder **data till information** genom att sammanfatta, strukturera, jämföra och extrahera innehåll. Däremot blir det inte kunskap förrän informationen tolkas i rätt sammanhang och används med mänsklit omdöme.

Den praktiska nyttan ökar när man använder tydliga instruktioner, delar upp komplexa uppgifter i flera steg (**prompt chaining**) och kopplar modellen till externa verktyg som webbsökning, dokumenthämtning, databaser och kodkörning. Då kan AI både arbeta med aktuell information och minska risken för fel.

I organisationer fungerar AI bäst som ett **filter och ett destilleringslager**: den minska informationsöverflöd, lyfetr fram det viktigaste och gör material mer handlingsbart. Men den ska inte behandlas som facit, eftersom den kan hallucinera, förenkla för hård, tappa kontext eller återge partisk information.

Den viktigate slutsatsen är att GenAI bör användas som **beslutsstöd, inte beslutsfattare**. Vid känsliga eller viktiga beslut behöver verifiering, spårbarhet, guradrails och mänsklig kontroll. Samma sak gäller lärande: AI gör oss smartare när den används för att förklara, stötta och träna tänkande, men den gör oss mindre smarta när den bara används för att leverera färdiga svar.