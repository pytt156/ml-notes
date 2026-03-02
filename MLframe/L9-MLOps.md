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
När problem och databehov är definerade går man vidare till att faktiskt samla in och strukturera datan.

Detta innefattar:
- Datainsamling
- Labeling
- Governance
- Versionshantering
- Provenance (ursprungsspårning)

### Datainsamling och ETL
Här systematiserar man: **Extract, Transform, Load**

I odlingsexemplet kan det innebära:
- Hämta GPS-data
- Hämta externa markdatafiler
- Transformera till rätt format
- Lagra i databas eller data warehouse

Ett data warehouse är ofta att föredra eftersom det möjliggör strukturerad åtkomst till rätt data i rätt format.

Exempel på pipeline:
Data → Python/Fivetran → Data warehouse → ML

Fivetran är ett exempel på ett verktyg för data ingestion.

### Labeling
Labeling är ofta mer komplext än vad man tror. <br>
Kundfeedback är sällan baar "positiv/negativ". Det kan behövas labels som:
- Produkt
- Personal
- Atmosfär
- Service
- Tonläge
- Grad av nöjdhet

Det kan bli ett stort manuellt arbete.

### Metadata och versionering
Att hålla koll på (detta är avgörande):
- Var datan kommer ifrån
- När den ändrades
- Vilken version som ändrades

Det finns hela företag som specialiserar sig på metadata management. <br>
I mindre projekt gör man en rimlig insats, t.ex:
- DVC för data-versionering
- Tydliga datascript
- Lagrad experimenthistorik

## 3. Experimenting
Nu börjar den klassiska data science-delen. <br>
Här jobbar man ofta i notebooks med:
- EDA
- Visualiseringar
- Feature Engineering
- Modellval
- Hyperparametertuning

Men i MLOps-sammanhang är det viktigt att detta inte blir "ad hoc".

Man bör:
- Separera kod i .py-filer
- Anropa funktioner från notebook
- Spara hyperparametrar
- Logga experiment
- Kunna återskapa varje körning

I odlingsexemplet:
- Läs data från warehouse
- Bygg träningsscript
- Experimentera med olika modeller
- Spara varje modellversion
- Spara metrics och parametrar

Detta möjliggör jämförelse och återanvändning

## 4. Träning och validering
Här formaliseras det som experimenteringen visat fungerar.

Man:
- Tränar modellen i kontrollerad miljö
- Sparar träningsdata tillsammans med modellen
- Jämför mot baseline

Baseline är viktigt. Man sparar ofta den bästa modellen hittills och jämför nya modeller mot den.

När en modell är tillräckligt mycket bättre (enligt fördefinerade kriterier) kan den "promoveras".

Det är viktigt att:
- Promotion criteria är definerade i förväg
- Man inte byter modell baserat på känsla

I detta steg förbereds modellen för produktion.

## 5. Packaging & deployment
Nu görs modellen produktionsklar.

Det kan innebära:
- Dockerisering
- Export till TorchScript
- Export till ONNX
- API-exponering (FastAPI)

Ofta sätts CI/CD upp.

Exempel:
- Merge till main triggar GitHub Actions
- Bygger Docker-image
- Deployar till EC2

Målet är att modellen ska uppdateras automatiskt när kod uppdateras.

Ett viktigt MLOps-krav är spårbarhet:
- Vilken kodversion?
- Vilken dataversion?
- Vilken modell?
- Vilken container?

Allt ska vara förståeligt och reproducerbart.

## Rollen som MLOps Engineer
En MLOps Engineer är ofta involverad i hela kedjan, men gör inte nödvändigtvis allt själv.

Samarbeten:
- Data Engineers (datapipeliners)
- Data Scientists (modellutveckling)
- Backend engineers (API)
- DevOps (infrastruktur)

I små bolag gör man mer själv. <br>
I större organisationer kan man vara mer fokuserad på början (problem + struktur) eller slutet (development + monitorering).

## Övergripande princip
Det viktigaste i ett MLOps-flöde är:
- Reproducerbarhet
- Spårbarhet
- Struktur
- Tydlighet
- Automatisering