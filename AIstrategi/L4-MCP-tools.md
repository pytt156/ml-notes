# Agenter, verktyg och MCP

## Verktyg som förlängning av modellen
En språkmodell (LLM) är i grunden en avancerad resonemangs- och textgenereringsmotor. Den kan förstå problem, formulera svar och dra slutsatser - men den är isolerad från omvärlden. Den kan inte själv hämta data, anropa API:er eller utföra handlingar.

Här kommer verktyg in.

Ett verktyg kan förstås som ett kontrollerat funktionsanrop som modellen kan be systemet att exekvera. Det kan handla om allt från att hämta väderdata, söka i dokument, köra kod eller interagera med externa system.

Det viktiga är att modellen aldrig kör verktyget själv. Den föreslår bara ett anrop. Det är alltid systemet runt modellen som:

1. exekverar anropet
2. hämtar resultatet
3. skickar tillbaka det till modellen

Det skapar en tydlig seperation mellan **resonemang (modell)** och **handling (system + verktyg)**.

En bra mental modell är:

> LLM = hjärnan <br>
> Verktyg = händerna

Utan verktyg kan modellen bara prata. Med verktyg kan den agera.

## Tool calling - hur modellen använder verktyg
När en modell får tillgång till verktyg sker interaktionen i ett stegvis flöde:

![toolcall](/assets/toolcall.png)

Det centrala här är att modellen lär sig _när_ den saknar information och däför behöver ett verktyg.

Det fungerar eftersom moderna modeller tränas på mönster som:
- När intern kunskap inte räcker -> föreslå tool call
- När verktygsdata returneras -> använd det i svaret

Det innebär att modellen inte "vet" saker som väder i realtid, men den vet att den inte vet och kan därför välja rätt verktyg.

## RAG - ett verktyg
RAG (Retrieval-Augmented Generation) är i praktiken ett verktyg för att hämta information från dokument.

Processen kan förstås i tre steg:
1. Retrieval - systemet söker fram relevanta textbitar (inte hela dokumentet)
2. Augmentation - dessa läggs in i prompten tillsammans med frågan
3. Generation - modellen genererar ett svar baserat på detta

Det viktiga att förstå är att modellen inte söker själv, den får färdig kontext från systemet.

### Varför chunking är centralt
Istället för att lagra hela dokumentet delas de upp i mindre delar ("chunks"). Det gör att systemet kan:
- hitta exakt relevanta delar
- minska brus
- få plats inom modellens kontextfönster

Det är avgörande för att RAG ska fungera effektivt.

### Embeddings och vektordatabaser
För att förstå RAG på djupet behöver man förstå embeddings.

När text omvandlas till embeddings representeras den som en vektor i ett högdimensionellt rum. Liknande betydelser hamnar nära varandra.

Det gör att systemet kan hitta relevans även utan exakta ordmatchningar.

![embeddings](/assets/embeddings.png)

Man kan tänka på det som en form av semantisk KNN-sökning:
- Frågan omvandlas till en vektor
- Närmaste dokumentet i "betydelserummet" hämtas
- De används som kontext

Det förklarar varför en fråga om "husdjur" kan matcha dokument om både hundar och katter, t.ex.

## Risker med verktyg
När en modell börjar använda verktyg förändras riskbilden drastiskt.

I en vanlig chatbot är ett fel bara ett dåligt svar. I ett agentsystem kan ett fel bli en faktisk handling.

Kom ihåg:

> Verktygsoutput = osäker input

Det innebär att data från verktyg (API:er, dokument, webbsidor) aldrig ska litas på blint.

### Typiska problem
- Prompt injection via dokument eller webbsidor
- Felaktiga anrop (rätt verktyg, fel parametrar)
- Överbehörighet (agenten kan göra för mycket)
- Dataläckage

Därför måste systemet designas med säkerhet från början.

## MCP (Model Context Protocol)
När man börjar bygga med avancerade system uppstår snabbt ett problem: varje integration mot verktyg blir unik.

MCP löser det genom att standardisera hur verktyg exponeras och används.

Grundidén är att separera tre roller:
- LLM - beslutar vad som ska göras
- MCP Client - förmedlar kommunikation
- MCP Server - exponerar och kör verktyg

Arkitektur:

![mcp](/assets/mcp.png)

Det kan liknas lite vid ett API-laget, men specifikt designat för LLM-agenter.

### Varför MCP är viktigt
Det som gör MCP kraftfullt är inte bara standardisering - utan hur det förändrar systemdesign.

#### 1. Separation av ansvar
Istället för att agenten innehåller all logik:
- Agenten fokuserar på resonemang
- MCP-servern hanterar integrationer

Det gör system mer modulära.

#### 2. Återanvändbarhet
Ett berktyg som exponeras via MCP kan användas av:
- flera agenter
- olika modeller
- olika applikationer

Det gör att man bygger verktyg bara en gång och återanvänder dem.

#### 3. Skalbarhet i komplexa system
När antalet verktyg växer blir direkt integration ohanterlig.

MCP fungerar då som ett lager där:
- verktyg kan organiseras
- tillgång kan filtreras
- output kan transformeras

Det möjliggör också mer avancerade arkitekturer, t.ex multi-agent-system.

### MCP + Tool calling
Det är viktigt att se att MCP inte ersätter tool calling - det strukturerar det.
Flödet blir:
1. MCP-server exponerar verktyg
2. MCP-client gör dem synliga för modellen
3. Modellen väljer verktyg (tool call)
4. MCP-server exekverar
5. Resultat skickas tillbaka

Skillnaden är att verktygen nu är:
- standardiserade
- separerade från agenten
- enklare att byta ut

### MCP - förenklat kodexempel

```python
# MCP server (förenklad)
class MCPServer:
    def get_weather(self, city: str):
        # här skulle ett riktigt API-anrop ske
        return {"city": city, "temp": -3}

# MCP client
class MCPClient:
    def __init__(self, server):
        self.server = server

    def call_tool(self, tool_name, **kwargs):
        tool = getattr(self.server, tool_name)
        return tool(**kwargs)

# "Agent"
def agent(user_input, client):
    if "väder" in user_input.lower():
        result = client.call_tool("get_weather", city="Stockholm")
        return f"I {result['city']} är det {result['temp']}°C"
    
    return "Jag vet inte"

# setup
server = MCPServer()
client = MCPClient(server)

print(agent("Hur är vädret?", client))
```

Det visar bara kärnprincipen:
- agenten beslutar
- clienten förmedlar
- servern exekverar

I riktiga system sker det via standardiserade protokoll istället för direkta funktionsanrop.

# TL;DR

- LLM:er kan resonera men inte agera -> verktyg ger dem “händer”
- Tool calling = modellen föreslår verktyg, systemet kör dem
- RAG = verktyg för att hämta kunskap från dokument via embeddings
- Verktygsoutput är opålitlig -> måste behandlas som osäker input
- MCP standardiserar hur verktyg kopplas till agenter
- Resultatet: modulära, skalbara och säkrare agentsystem