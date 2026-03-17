# Agenter och LangChain

## Vad är en AI-agent?
En AI-agent kan ses som ett skifte från passiv textgenerering till aktiv problemlösning. En traditionell språkmodell (LLM) är i grunden tränad för att förutsäga nästa ord i en sekvens, vilket gör den mycket bra på att formulera svar, resonera och förklara. Men i sin rena form saknar den förmågan att agera i världen.

En agent bildas när man bygger ett system runt modellen som gör att den inte bara svarar utan också:
- tolkar ett mål
- fattar beslut över tid
- utför handlingar via verktyg
- utvärderar sina egna resultat

Det innebär att agenten fungerar mer som en problemlösare än en svarsgenerator.
Ett viktigt sätt att förstå konceptet är genom den iterativa loopen:
1. Tolka problemet
2. Resonera kring nästa steg
3. Utföra en handling
4. Observera resultatet
5. Upprepa

![aiagent](/assets/aiagent.png)

Den loopen gör att agenten kan hantera uppgifter som inte har ett direkt svar, utan kräver flera steg, beslut och ibland omprövning.

Det är också anledningen till att moderna system so ChatGPT ofta klassas som agenter - de kombinerar språkförståelse med verktyg, minne och flerstegsinteraktion.

## Kärnkomponenter i en agent
För att en agent ska fungera i praktiken krävs en tydlig struktur. Utan strukturen blir beteendet ofta oförutsägbart eller ineffektivt.

### Mål
Målet är fundamentet för allt agenten gör, det fungerar som en riktning för både resonemang och handling.

Det som gör målformuleringen svårt är att språkmodeller är mycket känsliga för otydlighet. Om målet är vagt, till exempel "hjälp användaren", saknar modellen en tydlig optimeringspunkt. Resultatet blir ofta:
- överdrivet breda svar
- irrelevanta sidospår
- inkonsekventa beslut

Ett bra mål innehåller därför implicit eller explicit:
- vad som ska uppnås
- vilka begränsningar som finns
- när uppgiften är klar

Det brukar sammanfattas som en "definition of done". I praktiken är det ofta avgörande för om en agent fungerar bra eller inte.

### Planering
Planering är det som gör att agenten kan hantera komplexitet.

Istället för att försöka lösa hela problemet direkt, bryter agenten ner det i mindre delar. Det liknar klassiska idéer inom AI och kognitionsvetenskap, där komplex problemlösning sker genom dekomposition.

Det finns två viktiga aspekter här:
#### 1. Sekventiell nedbrytning
Agenten identifierar vilka steg som behövs och i vilken ordning de ska utföras

#### 2. Adaptiv planering
Agenten kan ändra planen baserat på ny information. Det är avgörande eftersom:
- verktygsresultat kan vara oväntade
- tidigare steg kan innehålla fel
- målet kan kräva omformulering

Det gör att planering i agentsystem ofta är dynamisk snarare än statisk.

## Verktyg (Tools), minne och kontroll
Verktyg är det som gör en agent användbar utanför sin egen "mentala värld". En språkmodell utan verktyg är begränsad till sin träningsdata och kan bara resonera hypotetiskt. Den kan gissa, approximera och formulera svar, men den kan inte verifiera, uppdatera eller agera.

När vi introducerar verktyg förändras det fundamentalt.

Med verktyg kan agenten:
- hämta aktuell och extern information
- utföra exakta beräkningar
- interagera med system (API:er, databaser, filer, etc.)

Det innebär att agenten går från att **simulera verkligheten** till att faktiskt **interagera med den**.

### Hur verktyg används i praktiken
En central princip i agentsystem är att modellen inte själv exekverar verktyg, istället fungerar den som en beslutsmotor.

Processen ser typiskt ut såhär:
1. Modellen avgör att ett verktyg behövs
2. Den formulerar ett anrop (vilket verktyg + vilka parametrar)
3. Systemet exekverar anropet
4. Resultatet skickas tillbaka till modellen
5. Modellen fortsätter sitt resonemang

Det skapar en viktig seperation:
- modellen tänker
- systemet gör

Den seperationen är avgörande för att kunna:
- kontrollera vad som faktiskt exekveras
- validera input och output
- begränsa risker


### Exempel:
```python
import os
from dotenv import load_dotenv

from langchain_ollama import ChatOllama
from langchain_core.tools import tool
from langchain_core.messages import HumanMessage
from langchain.agents import create_tool_calling_agent, AgentExecutor
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder

load_dotenv()

# Exempel på verktyg
@tool
def get_weather(city: str) -> str:
    """Hämtar väderinformation för en stad."""
    fake_weather_data = {
        "Stockholm": "6°C och molnigt",
        "Göteborg": "8°C och regn",
        "Malmö": "9°C och blåsigt"
    }
    return fake_weather_data.get(city, f"Ingen väderdata hittades för {city}.")


# LLM via Ollama
llm = ChatOllama(
    model=os.getenv("OLLAMA_MODEL", "llama3.1"),
    temperature=0
)

tools = [get_weather]

prompt = ChatPromptTemplate.from_messages([
    ("system", "Du är en hjälpsam agent. Använd verktyg när det behövs."),
    MessagesPlaceholder(variable_name="chat_history"),
    ("human", "{input}"),
    MessagesPlaceholder(variable_name="agent_scratchpad"),
])

agent = create_tool_calling_agent(llm=llm, tools=tools, prompt=prompt)

agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    verbose=True
)

response = agent_executor.invoke({
    "input": "Hur är vädret i Stockholm idag?",
    "chat_history": []
})

print(response["output"])
```
I det här exemplet:

- `ChatOllama` representerar språkmodellen
- `@tool` definierar vilka handlingar agenten kan utföra
- `create_tool_calling_agent` kopplar ihop modell, verktyg och prompt
- `AgentExecutor` kör agentloopen tills ett svar kan ges

Deta motsvarar den iterativa processen:
resonera → agera → observera → upprepa



### Verktyg som styrka - och svaghet
Verktyg gör agenten kraftfull, men introducerar samtidigt en avgörande insikt:
> All verktygsinput måste behandlas som osäker input

Till skillnad från modellens interna resonemang kommer verktygsdata från externa källor. Det innebär att den kan vara:
- felaktig (buggar, gammal data)
- ofullständig
- manipulerad
- innehålla instruktioner som försöker påverka agenten

Det gäller särskilt vid t.ex:
- webbsökning
- dokumentläsning
- användargenererat innehåll

En agent får därför aldrig "lita blint" på verktygsoutput, utan måste behandla den som vilken extern input som helst.

### Säkerhetsimplikationer av verktyg
När en agent får möjlighet att använda verktyg förändras riskbilden drastiskt.

En traditionell chatbot kan ge felaktigt svar. En agent med verktyg kan däremot:
- radera data
- skicka felaktig information
- fatta beslut med verkliga konsekvenser

Det gör att säkerhet inte är ett tillägg, utan en **central del av aktitekturen**.

Två särskilda problem är:
#### Prompt injection
Här försöker extern data (t.ex en webbsida eller ett dokument) påverka agentens beteende genom att innehålla instruktioner. Det handlar inte om att systemet hackas, utan att modellen luras att följa fel instruktioner.

#### Överbehörighet
Om agenten har mer tillgång än den behöver ökar konsekvenserna av varje fel. Det är ett klassiskt systemdesignproblem som blir extra kritiskt i agentsystem.

### Guardrails och kontrollmekanismer
För att hantera riskerna krävs explicita kontrollmekanismer (guardrails).

Några centrala principer:
- **Least privilege** <br>
    Ge agenten minsta möjliga behörighet för uppgiften
- **Validering av verktygsdata** <br>
    Kontrollera format, källor och integritet
- **Allowlist för verktyg**<br>
    Definiera exakt vilka handlingar som är tillåtna
- **Human-in-the-loop** <br>
    Kräv godkännande vid högriskåtgärder
- **Loggning och spårbarhet** <br>
    Spara beslut, anrop och resultat för analys

De mekanismerna gör att systemet blir:
- säkrare
- mer förutsägbart
- enklare att felsöka

### Minne och kontext
Minne är det som gör att agenten kan arbeta över tid istället för att behandla varje steg isolerat.

Utan minne:
- upprepas arbete
- tidigare beslut tappas bort
- resonemang blir inkonsekventa

Med minne kan agenten:
- bygga vidare på tidigare steg
- hålla reda på mellanresultat
- anpassa sig till användaren

Samtidigt finns det en viktig teknisk begränsning: språkmodeller har ett begränsat kontextfönster. Därför används ofta externa minneslösningar, till exempel:
- databaser
- vektordatabaser (RAG)

Det gör att minnet blir både:
- en arkitektuell fråga (hur lagras data?)
- en designfråga (vad ska sparas och när?)

### Feedback och iterativ kontroll
Feedback är det som gör att agenten inte bara utför steg, utan också förbättrar sig. En agent utan feedback beter sig som en enkel pipeline:
- steg 1 -> steg 2 -> steg 3 <br>
    utan att reflektera över kvaliteten.

Det fungerar dåligt i komplexa problem.

Med feedback kan agenten:
- upptäcka fel
- omvärdera sina beslut
- justera sin plan

Det är särskilt viktigt i kombination med verktyg, eftersom felaktig verktygsdata annars kan få kedjeeffekter genom hela systemet.

## Multimodalitet
Multimodalitet innebär att agenten inte är begränsad till text, utan kan arbeta med flera typer av information. Det förändrar fundamentalt vad en agent kan göra. I en textbaserad värld kan agenten:
- svara
- resonera
- skriva

I en multimodal värld kan agenten
- lyssna (speech -> text)
- se (image -> text)
- prata (text -> speech)
- skapa visuellt innehåll

Det gör att agenten kan:
- integreras i flera miljöer
- interagera mer naturligt med människor
- hantera rikare informationskällor

Ur ett systemperspektiv innebär det också ökar komplexitet, eftersom olika datatyper kräver olika modeller och pipelines.

## Deterministiska vs icke-deterministiska agenter

### Deterministiska agenter
Deterministiska system är förutsägbara. De följer fasta regler och producerar samma output för samma input. Det gör dem:
- enkla att testa
- enkla att verifiera
- säkra i kritiska system

Men de är också begränsade:
- de kan inte hantera osäkerhet
- de kan inte anpassa sig till nya situationer

Därför används de främst där problemet redan är väldefinierat.

### Icke-deterministiska agenter
Icke-deterministiska agenter utnyttjar språkmodellens probalistiska natur. Det innebär att agenten kan:
- väga flera alternativ
- välja olika strategier
- anpassa sig efter kontext

Det gör dem mycket kraftfulla i öppna problem, men också svårare att kontrollera. De systemen är inte "slumpmässiga" i praktiken, utan **känsliga för kontext och promptdesign**.

## Top-p och Temperature
Top-p och Temperature styr över hur modellen genererar text, men deras effekt blir särskilt viktig i agentsystem eftersom de påverkar beslutsfattande.

### Top-p
Top-p begränsar vilka ord modellen får välja mellan. Det påverkar:
- hur bred modellens "tankeyta" är
- hur många alternativa spår den överväger

Ett lägre värde gör modellen mer fokuserad, medan ett högre värde gör den mer explorativ.

### Temperature
Temperature påverkar hur starkt modellen följher sannolikhetsfördelningen. Det påverkar:
- variation
- kreativitet
- stabilitet

Låg temperature gör agenten mer konsekvent, medan hög temperature kan göra den mer innovativ men också mer oförutsägbar.

---

| Parameter   | Styr främst                                         | Lågt värde ger              | Högt värde ger                 |
| ----------- | --------------------------------------------------- | --------------------------- | ------------------------------ |
| Temperature | Hur starkt modellen följer sannolikhetsfördelningen | stabilare, mer förutsägbart | mer variation, mer kreativitet |
| Top-p       | Hur bred kandidatpool modellen får välja från       | smalare urval               | bredare urval                  |

---

### Praktisk betydelse i agenter
I agentsystem används ofta:
- låg temperature för beslut och logik
- högre temperature för generering

Det beror på att:
- beslut kräver stabilitet
- textskapande gynnas av variation

## LangChain
LangChain är i grunden ett sätt att hantera den komplexitet som uppstår när man går från en enskild språkmodell till ett helt agentsystem. En LLM i sig är enkel att använda - man skickar in en prompt och får ett svar. Men så fort man vill att modellen ska arbeta i flera steg, använda verktyg, minnas tidigare interaktioner eller hämta extern data, då växer snabbt komplexiteten i systemet.

Det är där LangChain kommer in. Istället för att bygga all logik själv erbjuder det en struktur för hur olika delar kan kopplas ihop på ett konsekvent sätt. Man kan se det som ett lager ovanpå modellen som organiserar hur resonemang, handlingar och dataflöden samverkar.

En central idé är att bryta ner systemet i mindre delar som samarbetar. Istället för en stor, svåröverskådlig funktion som gör allt, bygger man upp flöden där varje steg har en tydlig roll. Ett typsikt sådant flöde kan se ut såhär:
- tolka användarens input
- hämta relevant information
- låta modellen resonera kring problemet
- formulera ett svar eller ta nästa steg

Den här uppdelningen gör det lättare att förstå vad som händer i systemet, och framförallt att ändra eller förbättra en del utan att påverka allt annat.

När man arbetar med agenter blir det här ännu viktigare. En agent följer inte ett fast flöde, utan måste kontinuerligt avgöra vad den ska göra härnäst. LangChain erbjuder strukturer för det genom att låta modellen få tillgång till verktyg och välja mellan dem beroende på situation. På så sätt implementeras den iterativa loop där agenten växlar mellan att tänka och agera.

Samtidigt hjälper LangChain till att hantera några av de mest praktiska utmaningarna i sådana system. Det gäller framförallt:
- hur verktyg definieras och anropas på ett kontrollerat sätt
- hur minne lagras och återanvänds trots begränsat kontextfönster
- hur externa datakällor (t.ex via RAG) integreras i modellens resonemang

Det gör att man kan bygga system som inte bara svarar på frågor, utan faktiskt arbetar med information över tid.

Det är också viktigt att hålla koll på vad LangChain **inte** gör. Den ersätter inte behovet av bra design. Om målet är otydligt, om verktygen är dåligt definierade eller om säkerheten brister, kommer inte ramverket att "rädda" systemet. Däremot gör det det enklare att implementera en bra design på ett strukturerat sätt.

Sammanfattningsvis fungerar LangChain som ett sätt att gå från experiment till system- Det hjälper till att organisera hur en agent tänker, agerar och minns, och gör det möjligt att bygga mer avancerade lösningar uutan att tappa kontroll över komplexiteten.

![langchain](/assets/langchain.png)

# TL;DR
En AI-agent är ett system som kombinerar en språkmodell med planering, verktyg, minne och feedback för att kunna lösa problem iterativt istället för att bara generera svar. Den arbetar i en loop av att tolka mål, resonera, agera och utvärdera resultat. Verktyg gör agenten kraftfull genom att möjliggöra verklig interaktion, men introducerar också säkerhetsrisker som måste hanteras med tydliga guardrails. Designen av mål, kontrollmekanismer och minne är avgörande för hur bra agenten fungerar. Ramverk som LangChain hjälper till att strukturera denna komplexitet, men ersätter inte behovet av genomtänkt systemdesign.