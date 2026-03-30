# 📝 Exempel-tenta

**Kurs:** AI-förstärkt kunskapsstrategi och hybrida AI-team
**Tid:** 3 timmar
**Hjälpmedel:** Dator/Inga
**Instruktion:**

* Motivera alla val
* Antag inget som inte framgår
* Skilj tydligt mellan AI, klassisk automation och människa
* Diagram ska kunna ritas i Visio

---

# Del 1 – Grundförståelse

**30 poäng**

### 1. Generativ AI i praktiken (10p)

Beskriv vad generativ AI är och hur det skiljer sig från traditionell AI.

Resonera därefter kring:

* varför modellen kan ge felaktiga svar
* vad det innebär i praktiska system

---

### 2. Kunskapstransformation (10p)

Förklara skillnaden mellan data, information och kunskap.

Beskriv ett scenario där:

* AI lyckas i första delen
* men misslyckas i sista

Motivera varför.

---

### 3. Verktyg och begränsningar (10p)

Förklara:

* vad ett “tool” är i ett agentsystem
* varför det behövs

Resonera kring:

* vad som händer om verktyg saknas
* vilka risker verktyg introducerar

---

# Del 2 – Tillämpning

**20 poäng**

### 4. Systemdesign-val (10p)

För varje uppgift nedan:

* välj lämplig lösning (LLM / agent / multi-agent / klassisk / hybrid)
* motivera

a) Validera att ett personnummer är korrekt formaterat
b) Tolka ett mejl och avgöra ärendetyp
c) Hämta aktuell väderdata och svara användare
d) Sammanfatta 500 interna dokument till beslutsunderlag

---

### 5. Promptdesign (10p)

Du ska skapa en återanvändbar prompt för att analysera interna dokument.

Krav:

* tydlig struktur
* återanvändbar i organisation
* minimerar feltolkning

Leverera:

1. prompt
2. kort motivering av designval

---

# Del 3 – Case (huvuddel)

**50 poäng**

## Case: “FleetOps Logistics”

Ett bolag hanterar transporter för företag i Norden.

### Input:

* mejl från kunder (förseningar, problem, ändringar)
* automatiska loggar från fordon
* interna rapporter från chaufförer

### Problem:

* 15 000 ärenden/månad
* mycket fri text + brus
* svårt att avgöra vad som är kritiskt
* många manuella steg
* varierande kvalitet i beslut

### Mål:

* minska handläggningstid
* prioritera rätt ärenden
* minska felaktiga beslut
* behålla kontroll över risk

---

## 6. Processanalys (10p)

Analysera processen:

* vilka delar är tolkningsbaserade
* vilka delar är regelstyrda
* vilka delar bör inte automatiseras

Motivera.

---

## 7. Designa ett agentflöde (15p)

Designa ett multi-agent-system.

**Krav:**

* minst 3 agenter
* minst ett tool call
* realistiskt att implementera
* tydlig separation av ansvar

Du ska:

* beskriva flödet så att det kan ritas i Visio
* ange vilket mönster du använder (t.ex handoff, supervisor, hybrid)
* motivera valet

---

## 8. Agent-specifikation (15p)

För varje agent:

* syfte
* input
* output
* vad den gör
* vilka verktyg (om några)
* vilka rättigheter

Motivera varför varje agent behövs.

---

## 9. Governance och risk (10p)

Identifiera risker i ditt system.

Resonera kring:

* var fel kan uppstå
* konsekvenser
* hur de kan begränsas

Beskriv även:

* var människa bör vara i loopen
* hur systemet bör loggas och följas upp

---

# Del 4 – Reflektion

**10 poäng**

### 10. AI i organisationer (10p)

Ta ställning till:

> “AI bör främst användas som beslutsstöd, inte beslutsfattare.”

Resonera kring:

* när detta stämmer
* när det inte gör det
* vilka konsekvenser fel användning kan få

