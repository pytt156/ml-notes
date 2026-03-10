# Strukturerad prompting

## Vad strukturerad prompting är
Strukturerad prompting innebär att man organiserar en prompt i tydliga delar istället för att skriva allt som en enda instruktion. Syftet är att minska tvetydighet, förbättra pecisionen och göra prompten lättare att återanvända och justera.

Det här är särskilt användbart i professionella sammanhang där samma typ av uppgift återkommer, till exempel:
- dokumentation
- sammanfattningar
- analys
- rapportbeskrivning
- AI-agenter
- systeminstruktioner

## Varför det fungerar
Strukturen hjälper både användaren och modellen. För användaren innebär det att man tvingas tänka igenom:
- vad modellen behöver veta
- vad den faktiskt ska göra
- vem resultatet är till för
- hur svaret ska låta
- hur det ska levereras

För modellen innebär det att instruktionen blir mindre tvetydig. Det blir tydligare vad som är:
- bakgrund
- uppgift
- målgrupp
- ton
- outputkrav

## En enkel grundstruktur
En praktisk struktur är:
1. **Överblick/kontext** <br>
    Vad handlar situationen om? Vad är bakgrunden?
2. **Uppgift/utförande** <br>
    Vad specifikt ska modellen göra?
3. **Mottagare/målgrupp** <br>
    Vem är svaret till för?
4. **Ton/stil** <br>
    Hur ska svaret "låta"?
5. **Output-format** <br>
    Hur ska svaret struktureras?

**Exempel:**
```text
<overview>
Vi skriver kursanteckningar om generativ AI för studenter på ett MLOps-program.
</overview>

<task>
Förklara few-shot prompting och ge ett praktiskt exempel.
</task>

<audience>
Studenter med grundläggande programmeringskunskap.
</audience>

<tone>
Neutral, tydlig och pedagogisk.
</tone>

<output>
Svara i Markdown med rubriker, exempel och kort sammanfattning.
</output>
```

### Instruktionernas placering spelar roll
Själva placeringen av instruktioner kan också påverka resultatet. Om du först skriver vad modellen ska göra och köttar in massa text efter det, så finns det en risk att modellen presterar sämre än om du skickar instruktionerna efter en massa text/koden som den ska göra något mer. Allra bäst verkar:
```text
<instructions>...</instructions>

<info>...</info>

<instructions>...</instructions>
```
Poängen är att modellen först får ramarna, sen materialer och däreefter en påminnelse om hur materialet ska användas. Det minskar risken för att informationen "tar över" prompten.

### Strukturerade prompts sparar iterationstid
En stor praktiskt poäng med strukturerade prompts är att de gör små ändringar enklare. Om du vill ändra t.ex:
- målgrupp
- ton
- outputformat

Så behöver du inte skriva om hela prompten. Du kan bara ändra den sektionen. Det gör dem mycket användbara i arbete där samma typ av uppgift återkommer.

## Slutsats
Strukturerad prompting är inte bara "snyggare prompts". Det är ett sätt att:
- minska tvetydighet
- styra output mer exakt
- göra promptar mer återanvändbara
- förbättra kvaliten genom tydligare ramar

Det är särskilt relevant i arbetslivet, där AI används återkommande och där konsekvens, precision och snabb iteration är viktigare än spontana engångsfrågor.