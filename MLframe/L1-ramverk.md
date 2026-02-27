# Ramverk och exekvering

## Exekveringsmodeller

Alla ramverk bygger på samma matematik:
- Tensoroperationer
- Linjär algebra
- Gradientbaserad optimering

Det som skiljer sig åt mellan dem är **hur beräkningen exekveras**, det är en arkitekturfråga, inte matematisk.

---

### Plain python

En vanlig python-funktion:
- Kör direkt när den anropas
- Inget byggs upp i förväg
- Ingen optimering sker innan körning

All ML-kod _börjar_ som vanlig matematik i **Python**.

---

### PyTorch - Eager Execution

PyTorch kör som standard i **eager mode**.

Det betyder:
- Varje rad exekveras direkt.
- Tensoroperationer sker omedelbart.
- Beräkningsgrafen byggs dynamiskt under körningen.

Konsekvenser:
1. Det är lätt att debugga
2. Man kan skriva Python-logik fritt
3. Det är intuitivt att förstå

Eager execution prioriterar:
- Utveckningshastighet
- Läsbarhet
- Flexibilitet

Det är en viktig anledning till att PyTorch är populärt i forskning.

---

### TensorFlow - Graph Execution `(tf.function)`
TensorFlow kan använda graph execution.

Processen ser ut såhär:
1. Funktionen spåras
2. En statisk beräkningsgraf byggs
3. Grafen exekveras

Här finns en tydlig seperation mellan:
- Att definera beräkningen
- Att köra beräkningen

Fördelar:
- Optimering kan göras globalt
- Grafen kan exporteras
- Distribution till produktion underlättas

Nackdel:
- Mindre flexibel under utveckling
- Svårare att debugga

Det är mer "ingengörsmässigt" än "forskningsmässigt".

---

### JAX - JIT (`jit`)
JAX använder en Just-In-Time-kompliering.

Process:
- Första anrop -> funktionen komplieras
- Efterföljande anrop -> oprimerad version körs

Krav:
- Funktionen måste vara ren (inga sidoeffekter)
- Dataflöder måste vara statiskt spårbart

Detta möjliggör:
- Aggressiv optimering
- Effektiv hårdvaruanpassing

JAX ligger arkitektoniskt närmare ett kompliatorsystem än traditionell Python-exekvering.

---

### torch.compile - PyTorch möter grafoptimering
Nyare versioner av PyTorch har `torch.compile`.

Det innebär att PyTorch kan:
- Analysera din eager-kod
- Generera en optimerad graf
- Köra den effektivare

Det är ett försök att kombinera:
- PyTorchs flexibilitet
- Graph execution-optimering

Stöd och prestanda beror på backend:
- CUDA (NVIDIA GPU) -> mest stabilt
- MPS (Mac GPU) -> begränsat
- CPU -> varierande stöd

Det är ett kompabilitetsproblem, inte matematiskt.

---

## CPU, GPU och CUDA

ML-modeller består nästan alltid av:
- Matrismultiplikationer
- Tensoroperationer

Det är exakt det GPU:er är byggda för.

### CPU
- Färre kärnor
- Varje kärna är kraftfull
- Bra för komplex logik och sekventiell kod

### GPU
- Många enklare kärnor
- Samma operation på stora datamängder
- Hög genomströmmning

En matrismultiplikation kan delas upp i tusentals små multiplikationer -> perfekt för GPU.

### CUDA
CUDA är NVIDIAs programmeringsplattform för GPU.

Ramverk använder CUDA för att:
- allokera minne på GPU
- kör kernel-operationer
- parallellisera beräkningar

Om CUDA saknas eller är felkonfigurerat:
- Ramverket kan försöka använda GPU ändå
- Varningar uppstår
- Fel kan se ut som kodproblem

Därför är det viktigt att skilja mellan:
- Matematiskt fel
- Miljöfel

---


# Device-hantering i PyTorch

För portabel kod används:
- `"cuda"` om GPU finns
- annars `"cpu"`

Typiskt flöde:
1. Kontrollera om CUDA är tillgängligt
2. Välj device
3. Flytta modellen och tensorer till device

Det gör att koden blir:
- Maskinoberoende
- Reproducerbar
- Robust

# Helhetsförståelse

1. Matematiken (linjär algebra)
2. Exekveringsmodell (eager vs. graph)
3. Hårdvaruanpassning (GPU/CPU)
4. API-design och modellval

Allt bygger på samma matematiska kärna.

Skillnaden mellan ramverk ligger i :

- Hur beräkningar planeras
- Hur de optimeras
- Hur de körs på hårdvara