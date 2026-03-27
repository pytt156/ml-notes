# Multi-Agent Workflows
Multi-agent-system bygger på samma grundprincip som ett välfungerande team: man delar upp ansvar i tydliga roller istället för att låta en och samma enhet göra allt. Det leder i praktiken till högre kvalitet, bättre kontroll och lägre risk.

Att använda en single-agent motsvarar i verkligheten att låta en och samma person vara projektledare, utvecklare, testare och jurist - något som sällan fungerar i verkliga system.

## När en single-agent inte räcker
Problemet med single-agent-system uppstår när komplexiteten ökar. Då blir flera strukturella svagheter tydliga.

För det första uppstår roll-blandning. Samma modell förväntas planera, utföra, granska och förklara - vilket i sin tur leder till sämre kvalitet i varje enskild del.

För det andra är det svårt för modellen att granska sig själv. LLM:er kan formulera sig övertygande även när den har fel, vilket för självvalidering opålitlig.

Ett tredje problem är prompt-overload. Instruktionerna växer över tid, blir svåra att underhålla och riskerar att överskriva context window. Dessutom kan modellen "låsa fast" i tidigare felaktiga antaganden.

Språbarheten försämras betydligt i single-agent-system. Det blir svårt att förstå varför ett beslut togs eller var i processen ett fel uppstod.

## Vad multi-agent-system löser
Multi-agent-arkitektur adresserar de problemen genom att införa tydlig ansvarsfördelning.

Den viktigaste principen är seperation of concerns. Istället för en generell agent delas systemet upp i roller som exempelvis:
- Planner (planerar)
- Doer (utför)
- Critic (granskar)

Det möjliggör oberoende granskning, vilket i sin tur ger bättre kvalitet och mer robusta resultat.

Även säkerheten förbättras genom att varje agent får begränsade rättigheter (least privilege). En agent som bara ska läsa ska inte kunna skriva eller exekvera åtgärder.

Arkitekturen blir också mer skalbar. Enskilda komponenter kan bytas ut eller förbättras utan att hela systemet påverkas.

## Varför ska vi använda mönster (patterns)?
Utan struktur riskerar multi-agent-system att bli kaotiska. Därför används etablerade mönster för att skapa:
- Förutsägbarhet
- Testbarhet
- Säkerhet

I praktiken kombineras ofta flera mönster beroende på användningsfall.

## Centrala multi-agent-mönster

### Sekventiella agenter (handoff)
I det här mönstret skickas arbetet vidare steg för steg mellan agenter, likt en stafett.

![handoffpattern](/assets/handoffagent.png)

Det här mönstret används när processen har flera steg i en bestämd ordning, men där input och output kan kräva resonemang.

**Exempel: CV-granskning**
- Agent 1 extrahetrar information
- Agent 2 matchar mot krav
- Agent 3 skriver rekommendation

*Fördelen* är att alla agenter får ett tydligt ägarskap. *Nackdelen* är att fel tidigt i kedjan kan bli förstärkt när den skickas vidare (cascading errors).

### Sekventiellt flöde med orchestrator
Här styr ett central system flödet, istället för att agenter själva skickar vidare.

![sekork](/assets/sekork.png)
Det passar när input och output är veldefinerade och inte kräver tolkning.

**Exempel: Fakturahantering**
1. Läs faktura
2. Validera data
3. Bokför

### Supervisor med specialist-agenter (agents-as-tools)
Här fungerar en "manager"-agent som orchestrator för arbetet genom att anropa specialist-agenter.

![supervisor](/assets/supervisetool.png)

Supervisor-agenten bestämmer dynamiskt vilka agenter som ska användas. Själva supervisorn gör inget med datan, utan i dess systemprompt står det t.ex "du är en supervisor som ska välja rätt agent med rätt tools baserat på uppgiften/underlaget".

Anledningen till att supervisorn inte använder alla tools själv är åter igen "seperation of concerns" - den ska bara vara expert på att välja rätt agent och de agenterna ska i sin tur vara experter på att använda sina egna verktyg.

Det är särskilt användbar när:
- Steg inte är förutbestämda
- Flera datakällor finns
- Beslut kräver resonemang

**Exempel: Kundsupport**
- FAQ-agent -> policies
- CRM-agent -> kundhistorik
- Logg-agent -> systemdata

Fördelen är att det är väldigt flexibelt. Nackdelen är att supervisor kan lätt bli en flaskhals.

### Planner - Doer - Critic
Planner, doer, critic är ett av det mest centrala mönstren för kvalitetssäkring.

![pdc](/assets/plannerdoes.png)

Doer får en plan av planner, doer kör stegen och använder sina verktyg. Critic tar in det doer har gjort och planen och bedömer om doer har levt upp till sina krav/sitt mål. Om critic inte godkänner det doer har gjort så går det tillbaka till planner.

**Exempel: Rapportgenerering**
- Planner bryter ner uppgiften
- Doer skriver innehåll
- Critic granskar fakta och kvalitet

Det här mönstret passar bäst när uppgiften är mer "fråga -> svar" och när kvaliteten, korrektheten på outputen är särskilt viktig och ska kunna verifieras.

### Hybridflöde (Deterministiskt + Icke-Deterministiskt)
Det här mönstret kombinerar regelbaserade beslut och resonemang från en LLM och ger ett stabilt men också flexibelt system.

![hybrid](/assets/deterickedeter.png)

Det passar extra bra när ett flöde har en fast uppsättning vägval, förutbestämda valmöjligheter eller när ett vägval måste uppfylla krav för att fortsätta.

**Exempel: IT-support**
- Regel: "Handlar det om att reseta lösenord?" -> Ja -> automatisera
- Nej -> LLM analyserar problem
- -> Väljer åtgärd

### Human-in-the-loop
Human in the loop används när systemet är byggt för att kunna hantera uppgifter som kan vara skadliga, riskfyllda eller irreversibla, t.ex skriva över data, radera filer osv. Då stannar systemet och kräver mänsklit godkännande innan det går vidare.

![hitl](/assets/humanloop.png)

**Exempel: Skicka mail till kund**
- Agent skriver svar
- Männniska granskar innan det skickas

---

## Ofta krävs en kombination av flera mönster

![programmingexample](/assets/program.png)

Planner analyserar problemet (inputen från användaren), doer skriver koden baserat på vad planners plan och critic granskar resultatet. Om allt godkänns så går det vidare till en agent som skapar commit och pull requests.

**Exempel: Systemprompt för Planner**
```xml
<Context>
 Du är en agent i ett fleragentsystem för
kodgenerering. Systemet består av flera steg
och agenter där din roll är att analysera
användarens uppgift innan kod skrivs och
skapa en plan.
</Context>
<Objective>
 Analysera användarens mål och skapa en
tydlig och kort implementeringsplan
 som beskriver vad funktionen ska göra och
vilka krav som gäller.
</Objective>
<Style>
 Kortfattad, Strukturerad, Tydlig och
konkret
</Style>
<Tone>
 Neutral, teknisk och analytisk.
</Tone>
<Audience>
 Planen är avsedd för en annan
AI-agent som ska skriva
Python-kod baserat på din plan.
</Audience>
 <Response>
Formatera planen med mål, edge
cases och testexempel. Skriv
inte någon kod.
 </Response>
```

De andra agenterna har EGNA systemprompts. I större system är strukturerade prompts avgörande - både för läsbarhet och för att andra agenter ska kunna tolka dem korrekt.

## Kommunikation och överlämning
I fleragentsystem avgör kvaliteten på överlämningen ofta kvaliteten på hela arbetsflödet. En agent kan vara hur bra som helst på sin deluppgift, men om nästa agent får fel kontext, otydliga instruktioner eller ett svar formulerat mål förs felet vidare och förstärks. Därför bör kommunikation mellan agenter behandlas som en egen designfråga, inte som något som "bara händer" mellan två steg.

Det centrala är att varje agent måste få precis den informationen som krävs för nästa steg, varken för lite eller för mycket. För lite kontext gör att nästa agent gissar. För mycket kontext gör att viktig information drunknar, att kostnaden ökar och att modellen lättare tappar fokus. En bra överlämning är komprimerad, strukturerad och handlingsbar.

Två vanliga sätt att organisera överlämning är handoff och manager-orchestrating.

I ett handoff-flöde fungerar överlämningen som en stafettpinne. Agent 1 producerar ett resultat och lämnar vidare en sammanfattad, relevant kontext till Agent 2, som i sin tur lämnar vidare till Agent 3. Fördelen med det är att varje agent har ett tydligt ägarskap: varje agent ansvarar för sin del och behöver inte förstå hela systemet. Nackdelen är att tidiga fel blir lätt dyra längre fram. Om första agenten missförstår problemet och den missuppfattningen inte fångas upp, kommer efterföljande steg att bygga vidare på samma fel.

![handoff](/assets/handoff.png)

I ett manager- eller supervisor-mönster ligger kontrollen istället hos en central agent eller ett centralt system. Den avgör vilka specialistagenter som ska användas, i vilken ordning och vilken information som ska skickas vidare. Det ger bättre kontroll, tydligare routing och ofta enklare governance. Samtidigt kan managern bli en flaskhals. Den måste hålla mycket i minnet samtidigt, vilket ökar promptlängd, komplexitet och risken för att contextfönstret fylls av mellanresultat.

![supervisor](/assets/supervisor.png)

## Vad en bra överlämning innehåller
En användbar tumregel är att fråga: **Vad måste nästa agent veta för att kunna lösa sin uppgift korrekt utan att gissa?**

Det brukar innebära att en överlämning behöver innehålla följande delar, helst i ett strukturerat format som JSON, XML, YAML eller gemensam ärendefil:

- **MÅL:** Vad försöker vi lösa? Målet ska vara konkret nog för att styra agentens arbete. "Skriv något om fakturan" är svagt. "Extrahera fakturanummer, belopp, förfallodatum och markera avvikelser" är användbart.
- **Constraints:** Vilka regler gäller? Det kan vara policykrav, säkerhetsregler, tone of voice, tidsgränser, tillåtna verktyg eller vad agenten uttryckligen **inte** får göra.
- **Kända fakta:** Vad vet vi redan? Vad har redan verifierats? Nästa agent ska inte behöva göra om samma kontroll eller börja från noll.
- **Vad som redan testats:** Det här är viktigt för både effektivitet och för att undvika loopar. Om tre verktyg redan har misslyckats med samma sak så bör nästa agent veta det.
- **Öppna frågor eller oklarheter:** Det som inte är säkert ska markeras som osäkert. En bra överlämning skiljer på verifierat, antaget och oklart.
- **Outputformat:** Om nästa agent ska returnera exempelvis en lista med åtgärder, en JSON-struktur, ett beslutsunderlag eller färdig text så måste det vara tydligt från början.
- **Confidence eller osäkerhetsnivå:** Nästa steg blir bättre om systemet vet vad som är välunderbyggt och vad som bara är preliminärt.
- **Next actions:** Vad förväntas nästa agent att göra nu? Inte bara allmän kontext, utan tydlig uppgift.

Det viktiga är att inte "skicka med allt", utan att skicka vidare **rätt sak i rätt form.** Att dumpa hela chattloggen på nästa agent är inte en bra lösning, överlämningen ska fungera som en brief, inte som ett ostrukturerat minne.

### Exempel på dålig och bra överlämning
En dålig överlämning är ofta vag, överlastad eller tvetydig:
```json
{
  "message": "Användaren verkar vilja ha hjälp med ett problem i systemet. Kolla vad som är fel."
}
```
Den typen av överlämning säger nästan inget användbart. Nästa agent vet inte vad som är problemet, vad som redan kontrollerats, vilken typ av output som förväntas eller vilka begränsningar som finns.

En bättre överlämning är komprimerad men handlingsbar:
```json
{
  "goal": "Diagnostisera varför användaren inte kan logga in i internportalen.",
  "constraints": [
    "Får inte återställa konto utan mänskligt godkännande",
    "Får endast använda loggverktyget och kunskapsbasen",
    "Svara på svenska"
  ],
  "known_facts": [
    "Användaren får felmeddelandet 'invalid session'",
    "Lösenordsreset har redan testats utan effekt",
    "Två andra användare rapporterade samma fel senaste timmen"
  ],
  "open_questions": [
    "Är felet kontospecifikt eller miljörelaterat?"
  ],
  "expected_output": {
    "format": "markdown",
    "sections": [
      "Trolig orsak",
      "Bevis",
      "Rekommenderad nästa åtgärd"
    ]
  },
  "confidence": 0.72,
  "next_action": "Analysera loggar och avgör om felet sannolikt ligger i sessionshantering eller autentiseringstjänst."
}
```
Här blir det tydligt vad nästa agent ska göra, vad den inte får göra, vad som redan är känt och hur svaret ska se ut.

### Gemensam ärendefil
I större system är det ofta bättre att inte låta all vitkig information ligga i fria textmeddelanden mellan agenter. Istället kan man ha en gemensam case-fil eller ett gemensamt state-objekt som uppdateras stegvis. Då blir det lättare att hålla ordning på vad som hänt, vad som verifierats och vad som återstår.

En sån struktur skulle kunna se ut såhär:
```yaml
case_id: "SUP-2026-0314"
user_goal: "Få hjälp med ett driftfel i API-miljön"
status: "investigation"
facts:
  - "Fel uppstår endast i produktion"
  - "Timeout efter 30 sekunder"
  - "Databas svarar normalt"
decisions:
  - step: "routing"
    decision: "Skicka till loggagent"
    reason: "Felet verkar miljöspecifikt"
tool_results:
  - tool: "log_search"
    summary: "Ökad mängd 502-fel i gateway"
    trusted: false
sources:
  - "gateway-logs-2026-03-27"
open_questions:
  - "Är upstream-tjänsten överbelastad?"
next_step: "Skicka till analysagent för rotorsaksbedömning"
risk_level: "medium"
```
Poängen med en sån vil är inte bara ordning och reda. Den gör också systemet mer testbart, mer spårbart och lättare att granska i efterhand.

### Loggning av överlämningar
Att logga överlämningar mellan agenter är god praxis. I praktiken fungerar loggen som systemets arbetsjournal. Där ska man kunna följa vilka besluts som togs, vilken information som skickades vidare, vilka verktyg som användes och varför ett visst nästa steg valdes. Det är viktigt för felsökning, kvalitetssäkring, regelefterlevnad och kostnadskontroll.

När ett fleragentsystem beter sig fel är problemet ofta inte att "modellen var dålig", utan att tidigare överlämning var otydlig, att fel agent fick uppgiften eller att output från ett verktyg behandlades som sanning utan granskning. Utan loggning blir sådana problem svåra att lokalisera.

En användbar loggpost bör åtminstone innehålla:
- Vilken agent som agerade
- Vilken input den fick
- Vilket beslut den tog
- Vilka verktyg som användes
- Vilket resultat som skickades vidare
- Nuvarande state och nästa steg

Även här är struktur viktigare än mängd. Loggar ska inte bara vara lång, utan läsbara.

**Exempel på loggrad:**
```json
{
  "timestamp": "2026-03-27T10:42:13Z",
  "agent": "critic_agent",
  "input_ref": "case:SUP-2026-0314",
  "decision": "reject",
  "reason": "DoD ej uppfylld: orsaksanalys saknar evidens från loggar",
  "tools_used": ["policy_check"],
  "next_step": "planner_agent",
  "risk_level": "medium"
}
```
En sån logg gör det möjligt att i efterhand förstå varför ett steg stoppades och vad som behövde förbättras.

### Middleware mellan agenter
När överlämning sker via berktygsanrop eller automatiserade pipelines bör det finnas ett lager mellan agenterna som validerar, filtrerar och standardiserar input och output. Det lagret kan beskrivas som middleware.

Middleware fyller flera funktioner. Den kan sanera bort farliga instruktioner, maskera personuppgifter, validera att JSON följer schema, stoppa output som saknar obligatoriska fält och hindra att en agent råkar skicka vidare otillåtna kommandon. På så sätt blir systemet mindre beroende av att varje enskild agent "kommer ihåg" alla regler.

![middleware](/assets/middleware.png)

I robusta system är det här ofta bättre än att lägga allt ansvar på modellens prompt.

### Vanliga fallgropar i överlämning

- **Kontextförlust:** När en agent har summerat för hårt, glömt viktiga fakta eller skickat vidare en allt för tunn sammanfattning. Resultatet blir att nästa agent måste gissa eller att användaren får upprepa sig. Det är extra vanligt när man försöker optimera för korta prompts utan att skilja på vad som är centralt och perifert.
- **Skiftande ton och stil:** Om vissa agenter arbetar i ett tekniskt, formellt språk och andra i ett mer förenklat eller marknadsförande språk kan slutresultatet bli ojämt. Det är inte bara ett estetiskt problem. Tonen kan faktiskt påverka precision, risknivå och hur väl svaret är anpassat till användningsområdet.
- **Felrouting:** När ärendet skickas till fel specialist eller i fel ordning. Ett klassiskt exempel är att en agent börjar skriva lösningar innan problemet ens är korrekt klassificerat. I såna fall hjälper inte bätte skrivförmåga; felet ligger i workflow-designen.
- **Flaskhals:** Ju fler regler, verktyg och specialistagenter en manager måste hålla reda på, desto svårare blir det att fatta konsekventa beslut. Det liknan problemet "gudobjekt" i mjukvarudesign: ett enda objekt vet och gör för mycket.
- **Otydlig definition of done:** Om systemet inte vet vad som räknas som ett färdigt och godkänt resultat är det lätt att agenter fortsätter i onödiga loopar. En critic-agent kan fortsätta att underkänna output, eller en doer-agent kan fortsätta att förbättra text som redan är tillräckligt bra, eftersom stoppvillkoret inte är tydligt formulerat.

#### Exempel på svag och stark DoD:
Svag:
```
"Uppgiften är klar när svaret är bra"
```
Starkare:
```
Uppgiften är klar när:
1. alla obligatoriska fält är ifyllda
2. minst två källor har kontrollerats
3. inga policybrott finns
4. critic-agenten markerar resultatet som godkänt
5. ingen öppen blockerande fråga kvarstår
```
Nu vet systemet vad som faktiskt krävs för att få avsluta.

## Säkerhet och risker
Fleragentsystem ger kraft och flexibilitet, men introducerar också fler riskytor. Varje extra agent, verktygskoppling och överlämning är en potentiell felkälla. Därför måste man hela tiden väga nytta mot risk. En extra agent är inte automatiskt en förbättring. Ibland blir systemet robustare av att vara enklare.

- **Prompt injection:** Om en agent hämtar innehåll från webben, dokument eller andra externa system så kan det innehållet innehålla instruktioner som försöker påverka modellen. Verktygsoutput ska därför alltid behandlas som osäker input, inte som auktoritativ styrning.

- **Överbehörighet:** En agent som bara ska läsa information ska inte också kunna genomföra köp, skicka mejl eller radera filer. Ju bredare behörighet en agent har, desto större blir skadan om den resonerar fel, utsätts för manipulation eller får ett dåligt tool-resultat.

- **Fel tool-användning:** En modell kan välja rätt verktyg men ge fel parametrar, feltolka svaret eller dra för säkra slutsatser från ofullständig output. Därför behöver verktygsanrop kombineras med validering och ibland med separata kontrollsteg.

- **Dataläckage:** Data kan läcka inte bara ut ur systemet, utan även internt mellan agenter som inte borde ha tillgång till viss information. En agent kan tillexempel läsa personuppgifter i ett system och sen skicka dem vidare till en annan agent som egentligen inte behöver dem.

- **Hallucinationer:** Att en agent uttrycker sig övertygande betyder inte att den har rätt. Det blir extra farligt när en agents felaktiga slutsats packeteras snyggt och sen förs vidare som "fakta" till nästa agent.

- **Feedback-loopar, kostnadssken:** Agenter kan trigga varandra fram och tillbaka, ofta på grund av otydliga stoppvillkor eller för svag styrning, vilket både ökar kostnaden och gör beteendet svårare att förutsäga.

- **Role confusion:** Agenten blandar ansvar. En planner börjar implementera, en critic börjar skriva om innehåll istället för att bedöma det, eller en läsagent börjar fatta beslut den inte är tänkt att fatta.

- **Cascading errors:** Ett fel i ett tidigt steg blir "sanning" i nästa steg och förstärks längst vägen. Det är fleragentsystemets version av viskleken..

## Praktiska skydd
För att bygga robusta fleragentsystem behövs inte bara bra prompts, utan tydliga skyddsmekanismer i arkitekturen.

- **All tool-ouput = osäker input:** Resultat från sökningar, databaser, dokument eller externa API:er ska granskas, valideras eller åtminstone klassas med rätt osäkerhetsnivå innan de påverkar beslut.

- **Least privilege:** Varje agent ska ha minsta möjliga behörighet för att kunan utföra sitt jobb.

- **Schema-validering:** Om agenters input och output följer tydliga scheman blir det lätare att upptäcka fel tidigt.

- **Human-in-the-loop:** Vid högriskbeslut, när handlingar är irreversibla, externa eller ansvarstunga (skicka mail, flytta pengar, godkänna juridiska dokument, radera) så bör alltid en människa godkänna innan åtgärden utförs.

- **Logging & audit:** Man ska alltid kunna följa plan, beslut, tool-calls, källor och överlämningar i efterhand. Utan det blir det svårt att både förbättra systemet och felsöka.

- **Gränser:** T.ex timeouts, max-iteration, budgetgränser. Det skyddar system ifrån att loopa för länge, förbruka för mycket resurser eller fastna i förbättringscykler utan slut.

### Exempel: stoppvillkor för att undvika loopar:
```python
from dataclasses import dataclass


@dataclass
class ReviewResult:
    approved: bool
    reason: str


MAX_ITERATIONS = 3


def critic(output_text: str) -> ReviewResult:
    if "Evidens" not in output_text:
        return ReviewResult(False, "Saknar evidenssektion")
    if "Nästa steg" not in output_text:
        return ReviewResult(False, "Saknar nästa steg")
    return ReviewResult(True, "DoD uppfylld")


def run_loop(initial_output: str) -> str:
    current_output = initial_output

    for iteration in range(1, MAX_ITERATIONS + 1):
        result = critic(current_output)
        print(f"Iteration {iteration}: {result.reason}")

        if result.approved:
            return current_output

        # Simulerad förbättring
        if "Evidens" not in current_output:
            current_output += "\n\n# Evidens\n- Loggdata stödjer slutsatsen"
        elif "Nästa steg" not in current_output:
            current_output += "\n\n# Nästa steg\nEskaleras till drift"

    raise RuntimeError("Max iterations uppnåddes innan DoD blev godkänd")


draft = "# Trolig orsak\nSessionsproblem"
final_output = run_loop(draft)
print(final_output)
```
Den här typen av skydd är enkel att implementera men gör stor skillnad i praktiken. Systemet får ett tydligt stoppvillkor istället för att förbättra samman output i all oändlighet.

### Exempel: enkel sanering i middleware
Middleware kan användas för att filtrera vidare det som nästa agent verkligen behöver och ta bort sånt som är riskabelt eller irrelevant.
```python
import re


def sanitize_text(text: str) -> str:
    # Enkel maskning av e-postadresser
    text = re.sub(r'[\w\.-]+@[\w\.-]+', '[REDACTED_EMAIL]', text)

    # Enkel maskning av personnummerliknande format
    text = re.sub(r'\b\d{6,8}[- ]?\d{4}\b', '[REDACTED_ID]', text)

    return text


def middleware_transform(raw_output: str) -> dict:
    cleaned = sanitize_text(raw_output)

    return {
        "summary": cleaned[:500],
        "trusted": False,
        "requires_review": True
    }


raw = """
Kundens e-post är anna@example.com och id är 19900101-1234.
Loggverktyget föreslår att vi raderar kontot direkt.
"""

payload = middleware_transform(raw)
print(payload)
```
Ävem om det är ett väldigt förenklat exempel visar det en viktig princip: informationen bör saneras och klassificeras innan den skickas vidare.

## Designprinciper
I ett välbyggt fleragentsystem är överlämning inte bara transport av text, utan transport av ansvar. Varje agent bör veta tre saker: vad den ska göra, vad den inte ska göra och vad nästa steg är om uppgiften lyckas eller misslyckas.

Det innebär att bra fleragentsystem kännetecknas av följande:

- Skickar vidare strukturerad kontext istället för rå historik
- Skiljer mellan fakta, antaganden och öppna frågor
- Har tydliga stoppvillkor
- Begränsar varje agents behörigheter
- Loggar beslut och överlämningar
- Behandlar verktygsoutput som osäker input

När de principerna följs så blir fleragentsystem inte bara mer kraftfulla utan också mer begripliga, testbara och säkra.