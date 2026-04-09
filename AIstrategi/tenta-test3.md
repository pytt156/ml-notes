
# 📝 **SALSTENTAMEN – AI-förstärkt kunskapsstrategi och hybrida AI-team**

**Tid:** 3 timmar
**Hjälpmedel:** Ej tillåtna
**Format:** Fri text + diagram (Visio eller motsvarande)
**Bedömning:** Helhetsförståelse, tillämpning, motivering, kritiskt resonemang

---

# 📦 **CASE: NexaCare Systems AB**

NexaCare Systems AB är ett nordiskt bolag som levererar digitala system till vårdcentraler och privata kliniker. Produkten består av:

* Journalsystem (webb + mobil)
* AI-stöd för triagering av patienter
* Interna analysverktyg för vårddata

## Organisation

* ~450 anställda
* 3 huvudteam: Support, Medical Ops, Sales/Customer Success
* ~12 000 inkommande ärenden/månad

## Datakällor

* Supportmail, chatt och telefonsammanfattningar
* Patientjournaler (känslig data)
* Loggar från systemet
* CRM-data
* Avtal och juridiska dokument (PDF)
* Interna riktlinjer (Confluence-liknande)

## Problem (nuvarande situation)

Organisationen lider av:

* **Informationsöverflöd**
* **Otydliga beslut**
* **Varierande kvalitet**
* **Långa ledtider**
* **Risker kopplade till känslig data (GDPR, medicinska beslut)**

### Konkret:

* Samma ärende kan hanteras olika beroende på person
* Kritiska incidenter missas eller eskaleras för sent
* Support svarar ibland felaktigt eller ofullständigt
* Sales gör avtal baserat på ofullständig kunddata
* Ledningen saknar tydlig överblick över problem

---

# 🔄 **Tillgängliga processer (välj fritt)**

Företaget har identifierat följande problemområden:

### **Process 1: Patientärenden (Support + Medical Ops)**

Input:

* Patientrelaterade supportärenden
* Kan innehålla känslig data, symptom, journalutdrag

Problem:

* Svårt att avgöra allvarlighetsgrad
* Risk för felaktiga råd
* Kräver ibland medicinsk expert

---

### **Process 2: Incidenthantering (Systemfel)**

Input:

* Loggar + inkommande supportärenden

Problem:

* Svårt att avgöra om det är verklig incident
* Data finns i flera system
* Sen kommunikation till kunder

---

### **Process 3: Avtal & kundförnyelser (Sales)**

Input:

* Kundmail + avtal (PDF) + CRM-data

Problem:

* Mycket manuell analys
* Risk för felaktiga beslut
* Svårt att hitta relevant historik

---

### **Process 4: Intern kunskapsanalys**

Input:

* Tusentals tickets, loggar, feedback

Problem:

* Ingen strukturerad analys
* Beslut tas på magkänsla

---

### **Process 5: Behörighetsförfrågningar**

Input:

* Interna requests (mail/slack)

Problem:

* Långsam process
* Säkerhetsrisker
* Otydlig ansvarsfördelning

---

# 🧠 **UPPGIFT (huvuddel)**

Du ska designa ett **AI-baserat multi-agent-system** för att förbättra en av processerna ovan.

---

## **1. Processval & problemförståelse**

Beskriv:

* Vilken process du valt
* Vilket problem du löser
* Varför detta är lämpligt för AI (och varför vissa delar inte är det)

---

## **2. Systemdesign (kärnuppgift)**

Designa ett komplett flöde:

### Du ska inkludera:

* Multi-agent-arkitektur
* Tydliga steg i flödet
* Input/output mellan steg
* Minst ett tool call
* Ev. pipelines/deterministiska steg

**Leverans:**

* Diagram (Visio-liknande)
* Kort beskrivning av flödet

---

## **3. Agentdesign**

För varje agent:

* Syfte
* Systeminstruktion (kort)
* Input
* Output
* Förmågor
* Rättigheter (vad får agenten göra)

---

## **4. Verktyg (Tools)**

Beskriv:

* Minst ett konkret tool
* Vad det gör (input/output)
* Varför det behövs (varför LLM inte räcker)

---

## **5. Kommunikation & överlämning**

Beskriv:

* Hur agenter skickar information mellan varandra
* Vad som skickas (struktur, inte fluff)
* Hur du undviker:

  * kontextförlust
  * felpropagering

---

## **6. Governance & risk**

Identifiera:

* Minst 5 konkreta risker i ditt system

Beskriv:

* Hur de uppstår
* Hur de hanteras (guardrails)

---

## **7. Hybriddesign**

Beskriv:

* Vilka delar är:

  * deterministiska
  * agentbaserade
* Varför

---

## **8. Definition of Done & stoppvillkor**

Definiera:

* När är systemet “klart”?
* När ska det stoppa?
* Hur undviker du loopar?

---

# 🧩 **DEL 2 – Teoretisk tillämpning (kortare svar)**

Besvara utan koppling till ditt case.

---

### **Fråga 1**

Förklara varför ett multi-agent-system kan vara bättre än en single-agent i komplexa organisationer.

---

### **Fråga 2**

Beskriv skillnaden mellan:

* Tool
* RAG
* Pipeline

---

### **Fråga 3**

Vad innebär:

* “All tool output är osäker input”

och varför är det kritiskt?

---

### **Fråga 4**

Förklara skillnaden mellan:

* Data
* Information
* Kunskap

och varför det är relevant i AI-system

---

### **Fråga 5**

Beskriv två situationer där AI **inte bör användas**

---

### **Fråga 6**

Vad innebär:

* Human-in-the-loop

och när behövs det?

---

### **Fråga 7**

Vad är prompt injection och hur skyddar man sig?

---