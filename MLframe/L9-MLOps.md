# MLOps - översikt och livscykel

Anteckningar från föreläsning om MLOps som helhet och hur livscykeln ser ut - från idé till produktion och vidare.

MLOps är så mycket mer än att bara träna en modell, utan det är ett strukturerat sätt att arbeta med hela systemet runt modellen: data, experiment, versionering, deployment och uppföljning.

Livscykeln kan delas upp i fem huvudsteg.

## 1. Problem framing
Det första och kanske viktigaste steget är att definera vad problemet faktiskt är. Man börjar då med att definera:
- Affärsmål
- Tekniska mål
- Success metrics
- Databehov och begränsningar

Ofta kommer initiativet från en PO, CTO eller liknande som uttrycker något relativt vagt, som t.ex:
> "Vi vill veta vad kunderna tycker"
Då är det vårt ansvar att översätta detta till ett konkret maskininlärningsproblem.

Exempel:
- Förutspå aktiekurs (regression)
- Klassificera kundfeedback (klassificering)
- Tolka sensordata (anomaly detection eller regression)

### Metrics

Här måste man definera hur framgång mäts.<br>
Det kan vara:
- RMSE (regression)
- F1-score (obalanserad klassificering)
- Precision/Recall
- Accuracy

Men ibland är den viktigaste metricen inte teknisk utan affärsmässig.

Exempel: en aktiemodell kanske utvärderas på:
> "Hur mycket pengar tjänar den över en viss period?"

Det är mer business metric än modell-metric. Man kan kalla det en meta-metric: den mäter hela systemets effekt, inte bara modellens predektiva värden.

I vissa problem är valet av metric avgörande. I odlingsexempel:
> "Givet dessa koordinater, går det att odla?"

Här kan precision vara viktigare än recall, eftersom en false positive (att säga att mark går att odla när den inte gör det) kan vara dyrt. Metric-valet måste alltså kopplas till risk och kostnad.

### Data och begränsningar
I detta steg måste man också identifiera:
- Vilken data finns?
- Är den historisk?
- Är den av tillräcklig kvalitet?
- Är den relevant?
- Är den representativ?
- Är den balanserad?

Dessutom:
- Finns juridiska begränsningar så som GDPR?
- Finns risk för bias?
- Innehåller historisk data mönster som vi inte vill reproducera?

Exempel:<br>
I odlingsexemplet kan data bestå av:
- GPS-koordinater
- Markdata (bördighet, regn, soltimmar)
- Historiska odlingskator

I Kundfeedbacksproblem kommer GDPR och personuppgifter inte i bilden direkt.

En viktig poäng är att problemdefinition och datainventering ofta påverkar varandra. Ibland börjar man med ett mål och letar data. Ibland har man data och frågar:

> "Vad kan man göra med detta?"

Detta steg kan ta mycket lång tid. "Measure twice, cut once" gäller här. En feldefinierad metric eller fel problemformulering skapar problem i alla senare steg.

## 2. Data acquisition

## 3. Experimenting

## 4. Träning och validering

## 5. Packaging & deployment