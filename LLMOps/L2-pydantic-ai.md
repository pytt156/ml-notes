# Praktisk implementering

I praktiken kör man inte modeller lokalt. Man anropar dem via API. Det är standard i produktion av flera skäl: man slipper hantera GPU-infrastruktur, man får tillgång till de bästa modellerna, och man kan byta modell utan att ändra hela systemet.

## API-nycklar och säkerhet

API-nyckeln autentiserar dina anrop mot modell-API:et. Den *ska aldrig ligga i källkoden*. Standard-praxis är att lagra den i en `.env`-fil och ladda in den via environment variables.

```bash
# .env  (glöm inte .gitignore!)
OPENROUTER_API_KEY=sk-or-v1-xxxxxxxxxxxxx
MODEL_NAME=anthropic/claude-3.5-sonnet
```
```python
from dotenv import load_dotenv
import os

load_dotenv()  # Läser .env automatiskt

api_key = os.environ["OPENROUTER_API_KEY"]
model   = os.environ.get("MODEL_NAME", "anthropic/claude-3.5-sonnet")
```

## OpenRouter - ett API för alla modeller

OpenRouter fungerar som ett lager ovanpå flera modell-leverantörer (Anthropic, OpenAI, Meta, Mistral, m.fl). Samma API-format fungerar för alla, du byter bara `model`-parametern. Det är smart arkitektur: din kod behöver inte ändras om du vill testa en annan modell.

## PydanticAI - agent-abstraktionen

Istället för att anropa API:et direkt kan man kapsla in modellen i en **agent**. Det separerar *modell* från *logik* - om du vill byta modell ändrar du en rad.

```python
from pydantic_ai import Agent

agent = Agent(
    model="openai:gpt-4o",
    system_prompt="""Du är en erfaren kock.
Ge alltid recept med: ingredienslista, steg-för-steg-instruktioner
och tips för nybörjare.""",
    model_settings={"temperature": 0.5},
)

result = agent.run_sync("Ge mig ett recept på pasta carbonara")
print(result.data)

# Se tokenanvändningen
usage = result.usage()
print(f"Input tokens:  {usage.request_tokens}")
print(f"Output tokens: {usage.response_tokens}")
print(f"Totalt:        {usage.total_tokens}")
```

## System prompt vs. user prompt

System-prompten sätter den globala kontexten - modellens "personlighet", format, begränsningar. User-prompten är själva uppgiften. Modellen ser alltid båda, och system-prompten *räknas med i token-kostnaden vid varje anrop*.

```python
# Hela meddelande-historiken skickas vid varje anrop
messages = [
    {"role": "system",    "content": "Du är en kock..."},      # systemkontext
    {"role": "user",      "content": "Vad är pasta carbonara?"}, # första frågan
    {"role": "assistant", "content": "Pasta carbonara är..."},   # modellens svar
    {"role": "user",      "content": "Hur länge kokar man pastan?"} # nästa fråga
]
# ↑ ALLA dessa tokens betalas för vid varje nytt anrop.
# En lång konversation kostar exponentiellt mer.
```

### Tokenkostnad i praktiken
 
| Komponent | Tokens (uppskattning) | Kostnad (GPT-4o, ~$5/1M input) |
|---|---|---|
| System prompt | ~400 | $0.002 per anrop |
| User prompt | ~50 | $0.00025 |
| Svar (output) | ~300 | $0.006 (output kostar mer) |
| **Totalt ett anrop** | **~750** | **~$0.008** |
| 10 000 anrop/dag | 7,5M tokens | ~$80/dag |

System-prompten, konversationshistoriken och output-längden är de tre viktigaste variablerna att optimera om man jobbar i skala.

## Prompt-engineering - vad som faktiskt händer

Prompt engineering låter fancy, men i grunden är det ett ganska enkelt koncept: man försöker minska osäkerheten i modellens nästa-token-prediktion.

En vag prompt lämnar öppet för många tolkningar = modellen "gissar" mer = varierat, oförutsägbar output. En specifik prompt med klara instruktioner = snävare sannolikhetsfördelning = mer stabilt, förutsägbart svar.

```
# Dålig prompt - för vag
vague = "Berätta om pasta."

# Bra prompt - tydlig struktur och kontext
specific = """Du är en erfaren kock som undervisar nybörjare.

Förklara hur man lagar pasta al dente.
Strukturera svaret i tre delar:
1. Vad "al dente" betyder
2. Steg-för-steg-instruktion (max 5 steg)
3. Vanliga misstag att undvika

Håll varje del under 3 meningar."""
```
> **Viktigt:**
>> En prompt är ett sannolikhetskontrakt, inte ett verkligt kontrakt. Modellen *försöker* följa instruktioiner, men det är alltid en statistisk process. Det är därför man behöver testa, iterera och validera.

## Multimodal input

Moderna modeller kan hantera mer än text. Bilder, ljud och video kan kombineras med text-input:

```python
import base64

# Läs in bild och konvertera till base64
with open("bild.jpg", "rb") as f:
    image_data = base64.b64encode(f.read()).decode()

messages = [{
    "role": "user",
    "content": [
        {
            "type": "image_url",
            "image_url": {
                "url": f"data:image/jpeg;base64,{image_data}"
            }
        },
        {
            "type": "text",
            "text": "Beskriv vad du ser i denna bild."
        }
    ]
}]

# OBS: Kräver en modell med stöd för image-input,
# t.ex. gpt-4o, claude-3.5-sonnet, gemini-1.5-pro
```

## Den generella strukturen

Den generella strukturen som gäller de flesta LLM-applikationer är:

```
Input
  → Preprocessing
  → Prompt + system prompt
  → Modell  ←→  Verktyg (RAG, kalkylator, sökning…)
  → Postprocessing
  → Output
```

Det är här kopplingen till LLMOps börjar bli tydlig: en LLM är inte ett "intelligent objekt" du bara frågar saker. Det är en komponent i ett system som du måste designa, styra, övervaka och underhålla.