# Generativ AI och interaktion med LLM:er

## Vad generativ AI är
Generativ AI är ett samlingsbegrepp för AI-system som kan skapa nytt innehåll utifrån mönster de lärt sig från träningsdata. Det kan handla om text, kod, bilder, ljus eller video. Istället för att bara klassificera, sortera eller förutsäga så försöker modellen producera något nytt som liknar strukturerna i datan den tränats på.

Det är viktigt att förstå att generativ AI inte "hämtar fram" färdiga svar ur ett dolt uppslagsverk. Den genererar nytt innehåll varje gång, baserat på sannolikheter och mönster.

**Exempel:** <br>
En traditionell AI-modell kan tillexempel användas för att avgöra om ett mail är spam eller inte. En generativ modell kan istället skriva ett nytt mail, sammanfatta en rapport eller skapa ett utkast till kod.

Det här gör generativ AI användbar i arbete som kräver:
- formulering
- strukturering
- omskrivning
- sammanfattning
- idéutveckling
- språk- eller kodproduktion

## LLM som underkategori
En LLM, Large Language Model, är den **typ** av generativ AI som är specialiserad på språk. Den tränas på mycket stora mängder text och lär sig statistiska samband mellan ord, fraser, meningar och större textmönster.

Det betyder att modellen blir bra på att forsätta text på ett sätt som verkar rimligt i sammanhanget. Den kan därför:
- svara på frågor
- sammanfatta texter
- skriva om innehåll
- skapa kod
- resonera i textform
- imitera olika stilar och format

Det bertyder däremot **inte** att modellen förstår världens på samma sätt som en människa. En LLM har ingen direkt åtkomst till verkligheten och ingen inre garanti för vad som är sant. Den arbetar med språkstatistik.

**Exempel:**
```text
Input:
"The capital of France is"

Output:
"Paris"
```
Det ser ut som ett faktasvar, men tekniskt sett är det resultatet av att modellen bedömer att "Paris" är den mest sannolika fortsättningen givet kontexten.

## Ett nytt sätt att interagera med information

### Google-sökning vs LLM-frågor
När man använder Google söker man ofta brett. Man försöker hitta flera källor, jämföra dem och själv avgöra vad som är relevant. Interaktionen är i grunden sökbaserat: användaren letar, väljer, läser och tolkar.

När man använder en LLM sker interaktionen mer dialogiskt. Man beskriver vad man vill ha, i vilket formar, på vilken nivå och ibland varför. Istället för en lista med källor får man ett direkt bearbetat svar.

Det här förändrar användarens roll. I en sökmotor gör användaren mer av utvalet och tolkningen. I en LLM flyttas mer av det arbetet till modellen. Därför blir prompten viktigare än sökordet.

**Exempel:** <br>
**Google-sökning**
```text
git rebase vs merge
```
**LLM-fråga**
```text
Kan du förklara skillnaden mellan git rebase och git merge, när man bör använda respektive metod och vilka risker som finns?
```
Det här innebär i praktiken:

**Sökmotorer passar bättre när du behöver**
- hitta fler källor
- verifiera fakta själv
- undersöka vad som finns
- få uppdaterad information direkt från webben

**LLM:er passar bättre när du behöver**
- få något förklarat
- omvandla text
- itirera på ett utkast
- resonera

## Från informationssökning till transformationsverktyg
En viktig poäng är att generativ AI inte bara används för "att ta reda på saker", utan också för att bearbeta saler. Det är ofta där den verkliga nyttan finns.

En användare kanske inte bara vill veta vad ett recept är, utan får det omgjort till vegetariskt. Inte bara veta skillnaden mellan två Git-kommandon, utan få en förklaring på rätt nivå för sitt team. Inte bara läsa en manual, utan ställa en fråga till manualen.

Det är därför generativ AI ofta fungerar bättre som arbetsverktyg än som ren faktamotor.

**Exempel:** <br>
- ladda upp en instruktionsbok till bilen och använd genAI för att "söka i den" och få hjälp
- använda samma sätt för att felsöka en diskmaskin eller liknande

Viktigt att komma ihåg: <br>
Om en metod fungerar i ett område bör man fråga sig hur den kan generaliseras till andra problem.

Man lär sig ett arbetssätt, snarare än bara svaret, t.ex:
- mata in relevant underlag
- avgränsa uppgiften
- låt modellen transformera materialet
- verifiera resultatet

## Hur LLM:er genererar svar

### Autoregressiv generering
Det flesta morderna språkmodeller genererar text autoregressivt. Det betyder att modellen skapar en token i taget och varje ny token blir en del av den kontext som nästa token baseras på.

Förenklat ser processen ut såhär:
1. modellen läser prompten och tidigare kontext
2. modellen beräknar sannolikheten för nästa token
3. modellen väljer en token
4. den tokenen läggs till i kontexten
5. processen upprepas tills svaret är klart

Det förklarar varför LLM:er är starka på flytande språk, men också varför de ibland "låser in sig" i ett spår. Om modellen tidigt börjar generera i fel rikting påverkar det resten av svaret.

Därför är det viktigt att ha tydliga prompts, bra avgränsare och exempel när man vill ha ett bättre resultat.

### Context window som arbetsminne
Context window kan beskrivas som modellens arbetsminne. Det är den mängd text modellen kan ta hänsyn till samtidigt när den genererar ett svar.

Det inkluderar:
- din prompt
- tidigare meddelanden i chatten
- eventuellt bifogar innehåll
- modellens egna tidigare genererade text i samma svar

Ju mer relevant kontext modellen har, desto bättre kan den ofta prestera. Men när kontexten blir för lång eller för blandad ökar risken att modellen tappar fokus eller börjar väga in fel saker.

**Exempel**: <br>
En chat som blandar:
- frontendfrågor
- backendfrågor
- deployment
- privata sidospår
- fler gamla instruktioner

kan ge sämre precision än separata chattar med ren kontext. Det är därför viktigt att:
- ha separata chattar per arbetsområde
- starta om när kontexten är rörig
- inte överlasta modellen mer irrelevant information

## Varför analys för svar ibland hjälper
Ibland hjälper det att be modewllen bryta ner problemet steg för steg innan den svarar.

Poängen är inte att modellen "tänker som en människa", utan att ett längre resonemang skapar mer användbar kontext för modellen själv under genereringen. När modellen först skriver ut delsteg blir de delstegen synliga i dess arbetsminne och kan påverka nästa steg i rätt riktning.

Detta hänger ihop med:
- autoregressiv generering
- context window
- beräkningskapacitet per token
- sannolikhetsstyrning

Varje ny token är ytterligare ett beräkningssteg. Om modellen frå lägga på några steg på analys innan slutsats kan svaret ibland bli bättre.

**Exempel:**
```text
Lös problemet steg för steg och ge slutsatsen sist.
```
Det är särskilt användbart när uppgiften kräver:
- flera mellanled
- logisk struktur
- avvägningar
- planering
- felsökning

Det är mindre relevant när uppgiften redan är enkel eller när man använder en modell som redan har interna resonemangssteg. Där bör man inte utgå från att extra "tänk högt"-instruktioner ger bättre resultat.

## Praktiska steg för bättre resultat

### Skriv tydliga instruktioner
En av de mest grundläggande strategierna är att vara specifik. Vaga prompts ger ofta generella svar. Tydliga prompts minskar tolkningsutrymmet och gör outputen mer användbar.

Att bara säga "skriv det här till mig" eller "sammanfatta detta" lämnar för många saker öppna:
- vilken längd?
- vilken målgrupp?
- vilken detaljnivå?
- vilket format?
- vilket syfte?

### Specificera format, längd och struktur
Många problem med AI-svar handlar inte om att modellen "inte kan", utan om att den levererar i fel form. Därför är output-format ofta lika viktigt som själva uppgiften.

En modell kan ofta följa format mycket väl om du anger det tydligt.

**Exempel:** <br>
Istället för att skriva
```text
"Summera det här"
```

Så är det bätre att skriva
```text
"Ge mig först en tvåmeningssammanfattning av den här texten, sen vill jag ha underrubriker med en mening per delområde"
```

### Använd avgränsare
När instruktioner och innehåll blandas ihop ökar risken att modellen misstolkar vad som är data och vad som är uppgift. Därför är avgränsare en enkel men viktig strategi.

Avgränsare kan vara:
- kodblock
- citatblock
- XML-liknande taggar
- tydliga rubriker

Det hjälper modellen att urskilja mellan:
- instruktioner
- underlag
- exempel
- önskar output-format

**Exempel:**
```text
<task>
Sammanfatta texten nedan för studentet i ett MLOps-program
</task>

<text>
....
</text>
```
Det är särskilt användbart när du:
- skickar in längre textmassor
- blandar instruktioner med källtext
- vill undvika promptförvirring
- arbetar med återkommande promptmallar

### Ge exempel istället för bara regler
En LLM är väldigt bra på att följa mönster. Därför räcker det ofta inte bara att beskriva vad du vill ha. Det kan vara effektivare att visa hur ett bra resultat ser ut.

Det kallas ofta
- one-shot prompting när du visar ett exempel
- few-show prompting när du visar flera exempel

Poängen är att exemplen fungerar som ett mönsker modellen kan fortsätta på. Det minskar tolkningsutrymmet och ökar sannolikheten att format, ton, och struktur blir som du vill ha den.

### Dela upp stora uppgifter i mindre delar
Ett återkommande problem är att användare ger modellen en för stor uppift på en gång. Då ökar risken för ytliga svar, missade delar och sämre precision.

Att bryta ner uppgiften i steg gör både användaren och modellen mer strukturerade. Det hjälper också när du själv inte riktigt vet vad du vill ha.

**Exempel:** <br>
Istället för
```text
"Bygg den här sidan åt mig"
```
kan man börja med:
1. dela upp sidan i komponentet
2. beskriv krav för varje komponent
3. ta en del i taget

### Använd rätt modell för rätt jobb
Alla modeller är inte lika bra på allt. Vissa är starkare på avancerat resonemang, andra är snabbare och billigare. Poängen är praktiskt snarare än filosofisk. Om du bara behöver ett snabbt, enkelt utkast kan en mindre eller snabbare modell räcka. Om du behöver mer avancerad analys eller högre precision kan det vara värt att använda en större modell eller ett verktyg med tillgång till källor.

Välj modell utifrån:
- kostnad
- hastighet
- precision
- krav på källor
- uppgiftens komplexitet

## Verifiering och kritisk analys

### Verifiera alltid outputen
En LLM "ljuger" inte i mänsklig mening. Den försöker inte medvetet vilseleda. Den följer sin matematiska funktion och producerar den text som verkar mest sannolik i sammanhanget. Problemet är att ett sannolikt svar inte automatiskt är ett sant svar.

Därför måste AI-svar granskas kritiskt, särskilt när de används som underlag för beslut, fakta eller yrkesmässiga leverabler.

Frågor att ställa:
- Är detta korrekt?
- Är detta komplett?
- Är resonemanger logiskt?
- Vilken källa bygger det på?
- Är svaret vinklat eller partiskt?
- Är modellen rätt verktyg för den här frågan?

## Begränsningar och risker

### Begränsad tillgång till aktuell information
En förtränad språkmodell har inte automatiskt tillgång till realtidsdata. Utan internet, verktyg eller externa datakällor kan den inte säkert svara på frågor som kräver färsk information.

**Exempel:**
```text
"Vad blir vädret imorgon?"
```
Det kräver uppdaterad extern data, inte bara träningsdata. Om uppgiften kräver t.ex:
- aktuella nyheter
- väder
- priser
- börsdata
- sportresultat
- ny lagstifting

Så behöver modellen kombineras med externa källor eller verktyg.

### Hallucinationer
Hallucinationer innebär att modellen genererar något som låter trovärdigt men är fel. Det kan vara:
- påhittade fakta
- påhittade källor
- missvisande förklaringar
- självsäkra men felaktiga slutsatser

Detta är inte ett undantag från hur modellen fungerar, utan en direkt följd av mekanismen bakom genereringen.

### Varför det händer
**Sannolikhetbaserad generering** <br>
Modellen väljer det som verkar mest sannolikt språkligt, inte det som är verifierat sant.

**Parametrisk kunskap**<br>
Modellen lagrar inte fakta som i en databas, utan som distribuerade vikter och mönster. Därför kan den blanda ihop eller rekonstruera fel.

**Sampling och temperatur** <br>
Mer varierad generering kan öka kreativitet, men också öka risken för fel.

**RLHF** <br>
Mänsklig feedback tränar modellen att vara hjälpsam och välformulerad, vilket ibland gör att den svarar när den egentligen borde avstå eller markera osäkerhet.

**Exempel:** <br>
En modell kan hitta på en referens eller ange ett bibliotek, en lag eller ett begrepp som **låter** rätt men inte finns.

**Minska risk genom att**
- begära källor
- verifiera dem
- bryta ner uppgifter
- använda externa verktyg
- be modellen markera osäkerhet
- jämföra med andra källor eller modeller

### Bias och partiskhet
LLM:er tränas på stora textmängder från världen, ofta från internet. Därför är de inte neutrala från början. De bär med sig mönster från data, urval och efterföljande finjustering.

Bias kan uppstå i flera led:

**Datakällor** <br>
Internet speglar redan historiska, kulturella och politiska skevheter.

**Underrepresentation** <br>
Mindre språk, mindre grupper eller mindre dokumenterade perspektiv får svagare representation.

**Mönsterförstärkning** <br>
Eftersom modellen optimerar sannolikhet tenderar den att reproducera det vanligaste snarare än det mest nyanserade.

**Mänsklig feedback** <br>
RLHF kan introducera ytterligare bias beroende på vilka människor som bedömt vad som är ett "bra" svar.

**Exempel:** <br>
Om ett yrke i träningsdatan ofta kopplas till ett visst kön eller en viss grupp kan modellen statistiskt luta åt samma mönster.

För att hantera bias behöver man:
- vara medveten om att den finns
- aktivt granska perspektiv
- be om alternativa tolkningar
- jämföra flera källor
- inte förväxla välskrivet språk med neutralitet

### Kontextförorening och fokusförlust
En annan praktisk begränsning är att långa eller blandade konversationer kan göra svaren sämre. Modellen kan då:
- tappa fokus
- väga in gammal irrelevant information
- hålla fast vid tidigare feltolkningar
- bli mindre precis

**Exempel:** <br>
Om du först diskuterat frontend, sen semesterplaner, sen juridiska frågor och därefter ber om kodhjälp i samma chat så ökar risken att irrelevant kontext spiller över.

Det är skälet till att:
- nya chattar ofta är bättre
- varje chat bör ha tydligt fokus
- man bör ibland börja om istället för att "bråka vidare" med modellen

---

# Slutsats
Generativ AI och LLM:er är kraftiga verktyg för att:
- förklara
- sammanfatta
- strukturera
- transformera information
- stödja lärande och problemlösning

Men de är inte sanningsmaskiner. De fungerar bäst när de används med:
- tydliga instruktioner
- relevant kontext
- rätt modellval
- kritisk verifiering
- mänskligt omdöme