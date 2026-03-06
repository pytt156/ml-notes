
# Databias och modellens tillförlitligthet

Maskininlärningsmodeller kan vara mycket bra på att identifiera och predikta mönster i data, men deras kvalitet är i grunden begränsad av den data de tränas på. En modell lär sig statistiska samband från träningsdatan och generaliserar dessa samband till nya observationer. Om tränningsdatan innehåller felaktigheter, skevheter eller irrelevanta mönster så kommer modellen att reproducera eller förstärka dessa.

Detta är en central fråga inom **AI-etik och ansvarsfull AI-utveckling:** modeller är inte neutrala system utan reflekterar de data och antaganden som ligger bakom dem.

## Historisk data och oönskade artefakter
Många modeller tränas på **historisk data**. Historiska dataset kan dock innehålla mönster som inte är önskvärda att reproducera i framtiden. Dessa mönster kallas ibland **artefakter** i datan - statistiska samband som uppstår av historiska, sociala eller praktiska skäl snarare än verklige orsakssamband.

Till exempel kan ett dataset innehålla systematiska snedfördelningar eftersom:
- Vissa grupper historiskt varit underrepresenterade
- Datainsamlingen har skett på ett begränsat sätt
- Vissa variabler indirekt speglar strukturella bias

Ett ofta diskuterat exempel är rekryteringssytem. Om en modell tränas på historiska anställningsbeslut i en bransch där en viss grupp varit underrepresemterad kan modellen lära sig att associera denna grupp med lägre sannolikhet att bli anställd. Modellen reproducerar då **historiska strukturer** snarare än att göra en neutral bedömning av kompetens.

Det är alltså inte nödvändigtvis modellen i sig som är "biased", utan **datan som modellen lär sig ifrån**.

### Kontextberoende data
Ett annat problem uppstår när träningsdatan inte representerar **den verkliga kontext där modellen ska användas**.

Anta att man vill bygga en modell som hjälper till att planera lagerpåfyllning i en butik. Om modellen tränas på data från endast en viss typ av dag, tillexempel söndagar, kommer den att lära sig efterfrågemönster som är specifika för just den dagen. Den kommer då sannolikt att göra dålig prediktioner för andra dagar, till exempel fredagar då efterfrågan ofta ser annorlunda ut.

På samma sätt kan enstaka **extrema observationer** i datan påverka modellen. Om ett dataset innehåller ovanligt höga försäljningssiffror på grund av en kampanj eller ett tillfälligt evenemang kan modellen överestimera framtida efterfrågan om dessa situationer inte identifieras och hanteras.

Problemet handlar alltså ofta om att **träningsdatan inte är represantativ** för den verkliga fördeling av situationer som modellen kommer att möta.

## Avsaknad av data

Bias i modeller uppstår inte bara genom vad som finns i datan, utan också genom **vad som saknas**.

Om vissa grupper, miljöer eller scenarier inte finns representerade i träningsdatan kommer modellen att ha svårt att generalisera till dem. Detta problem har varit särskilt tydligt i vissa historiska bilddataset. Om majoriteten av bilderna representerar en viss demografi kan modeller som tränas på dessa dataset presetera sämre på andra grupper.

Det är ett exempel på **sampling bias**, där datasetet inte representerar populationen som modellen senare ska användas på.

## Fokus på modell istället för data

I praktiskt maskininlärningsarbete är det vanligt att stor uppmärksamhet läggs på:
- modellarkitektur
- hyperparameteroptimering
- träningsstrategier

Det är dock lätt att förbise att **datans kvalitet och struktur är ofta viktigare än själva modellen**. Om datasetet är för litet, skevt eller innehåller irrelevanta variabler kan en mer avancerad modell snarare förstärka problemen än lösa dem.

Däför är **dataanalys och datagranskning** en central del av ansvarsfull modellutveckling.

## Hantering av skev data

Det finns flera tekniska strategier för att minska effekterna av skev eller obalanserad data.

En metod är **oversampling**, där man ökar representationen av underrepresenterade klasser eller grupper i träningsdaran. Det kan göras genom att:
- duplicera observationer från en minoritetsklass
- genera syntetiska datapunkter (t.ex med metoder som SMOTE)

Syftet är att göra träningsdatan mer balanserad så att modellen inte domineras av majoritetsklasser.

Det är dock viktigt att förstå att sådana tekniker **inte** löser alla etiska eller statistiska problem. Om grunddatan är kraftigt skev eller saknar viktiga variabler kan tekniska korrigeringar bara delvis kompensera för detta.

## Sammanfattning

Maskininlärningsmodeller är beroende av den datan de tränas på. Problemen i data - som historiska bias, bristande representation eller kontextberoende artefakter - kan leda till systematiskt felaktiga eller orättvisa prediktioner.

Därför är ett centralt ansvar i utvecklingen av AI-system att:

- analyser och förstå träningsdatan
- identifiera möjliga bias och artefakter
- säkerställa att datan representerar den verkliga användningsmiljön

I många fall är **datakvalitet och dataval** viktigare för modellens resultat än valet av algoritm eller arkitektur.
