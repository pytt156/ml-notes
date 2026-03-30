# 📝 EXEMPEL-TENTA (3h)

**Tid:** 3 timmar
**Hjälpmedel:** Dator (ingen internetaccess)
**Format:** Skrivs i text + diagram (valfritt verktyg, t.ex. Visio/Mermaid)

---

# DEL 1 – Grundförståelse (ca 30 min)

**Syfte:** Testar att du förstår grunderna (G-nivå)

### 1. Generativ AI vs traditionell AI (5p)

Förklara skillnaden mellan generativ AI och traditionell AI.

* Vad är det fundamentala problemet en LLM löser?
* Vad innebär det att modellen är probabilistisk?

---

### 2. Data → information → kunskap (5p)

Förklara skillnaden mellan:

* data
* information
* kunskap

Ge ett konkret exempel från en organisation.

---

### 3. Hallucinationer och risk (5p)

Förklara:

* varför hallucinationer uppstår
* varför de är farliga i organisationer
* hur de kan minskas

---

### 4. Tools och RAG (5p)

Beskriv:

* vad ett tool är
* varför en LLM behöver tools
* hur RAG fungerar (kort processbeskrivning)

---

### 5. AI-agent (5p)

Vad är en AI-agent?

Beskriv:

* skillnaden mellan LLM och agent
* agentens loop (kort)

---

# DEL 2 – Resonemang & tillämpning (ca 60 min)

**Syfte:** Testar förståelse + analys (G → VG)

---

### 6. När ska man INTE använda en agent? (10p)

Resonera kring:

* vilka typer av processer som lämpar sig dåligt för agenter
* varför
* ge minst 2 konkreta exempel

---

### 7. Single-agent vs multi-agent (10p)

Resonera kring:

* varför multi-agent ofta ger bättre kvalitet
* vilka nackdelar det har
* när single-agent kan vara bättre

---

### 8. Governance och risk (10p)

Beskriv hur du skulle designa ett säkert agentsystem.

Inkludera:

* human-in-the-loop
* least privilege
* loggning
* prompt injection

Motivera varför varje del behövs.

---

### 9. Promptdesign (10p)

Du ska designa en prompt för en agent som:

* sammanfattar kundärenden
* klassificerar dem
* prioriterar dem

Skriv en strukturerad prompt (med tydliga sektioner).

---

### 10. Verktyg och beslut (10p)

Scenario:
En agent ska avgöra om ett ärende kräver teknisk support.

Resonera kring:

* varför ett tool kan vara bättre än ren LLM-logik
* vilka risker som finns
* hur du skulle validera output

---

# DEL 3 – Case (ca 90 min)

**Syfte:** Huvuddelen (VG-nivå)
Här testas exakt det kursen säger:
→ processdesign + multi-agent + affärsnytta 

---

## CASE: SmartSupport AB

Företaget:

* SaaS + IoT-system
* 10 000 supportärenden/månad
* Input:

  * mail
  * formulär
  * loggar
  * kunddata
* Problem:

  * lång svarstid
  * inkonsekventa svar
  * svårt att prioritera rätt

---

## Uppgift

Designa ett **multi-agent-system** som förbättrar supportflödet.

---

## 1. Översikt (10p)

Beskriv kort:

* vilket problem du löser
* vad ditt system gör
* varför det skapar affärsnytta

---

## 2. Diagram (15p)

Rita ett flöde som visar:

* agenter
* verktyg
* överlämningar
* ev. human-in-the-loop

---

## 3. Agenter (30p)

Beskriv minst 3 agenter.

För varje agent:

* syfte
* input
* output
* systeminstruktion (kort)
* vilka verktyg den använder
* vilka rättigheter den har

---

## 4. Kommunikation / handoff (10p)

Beskriv:

* vad som skickas mellan agenter
* hur du undviker:

  * kontextförlust
  * fel
  * överflödig data

---

## 5. Verktyg (10p)

Minst ett tool ska användas.

Beskriv:

* vad det gör
* input/output
* varför det behövs

---

## 6. Risker & governance (10p)

Identifiera minst 3 risker, t.ex:

* hallucination
* dataläckage
* fel beslut

Beskriv hur de hanteras.

---

## 7. Motivering (5p)

Motivera:

* varför ditt system är bättre än dagens
* varför det är realistiskt att implementera

---