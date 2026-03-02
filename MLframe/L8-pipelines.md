# Pipelines

## Vad är en pipeline?

En pipeline är en strukturerad sekvens av steg som tillsammans utgör ett komplett maskininlärningsflöde - från rådata till färdig modell eller prediktion.

I praktiken betyder det:

> Data -> transformationer -> modellträning -> utvärdering -> eventuell export

Anledningen till att man bygger pipelines är att:
- ML-arbete består av flera beroende steg
- Samma steg ofta måste köras upprepade gånger (t.ex vid experimentering)
- Man vill ha reproducerbarhet och tydlig struktur
- Man vill minimera manuella misstag

En pipeline är allstå inte bara "bekämlighet" utan ett sätt att göra ML-arbete systematiskt och repeterbart.

## Pipeline som serie av transformationer
Ofta syftar man på preprocessing när man säger pipeline. Exempel:
- Skalning
- Encoding av kategoriska variabler
- Feature engineering
- Dimensionalitetsreduktion
- Modell

I t.ex `SciKit-Learn` är `pipeline` bokstavligen en sekvens av transformer-objekt följt av en estimator.

Det viktiga här är:
- Varje steg har en tydlig roll
- Stegen körs i en bestämd ordning
- Samma transformation applicerad konsekvent på train och test

Det minskar risken för data leakage.

## ETL - pipelines
Pipeline-tänket kommer egentligen från data engineering.

ETL står för **Extract -> Transform -> Load**.

En ETL-pipeline handlar om hur data flyttas och transformeras innan den ens når modellen.


| Typ | Fokus |
|-----|-----|
|ETL-pipeline|Datainsamling och förädling|
|ML-pipeline|Modellträning och inferens|
|MLOps-pipeline|Hela livscykeln (data->träning->deploy->monitorering|

## DVC-pipeline
DVC-pipelines är något annat än preprocessing-pipelines.

En DVC-pipeline beskriver:
- Vilka steg i projectet som beror på varandra
- När något ska köras om
- Hur artefakter (data, modeller) verionshanteras

Det är alltså en **exekveringspipeline**, inte matematik/transformation-pipeline.

Exempel:
```
data → preprocess → train → evaluate
```
Om `data` ändras körs allt om.<br>
Om bara `train.py` ändras körs bara träningen om.

## Generell pipeline vs. uppdelade funktioner

Det går att skriva en generell pipeline för flera modeller som tar hänsyn till olika preprocessing-steg, men risken med att göra det är att det blir för stort och grötigt och kräver för många parametrar och inputs.

Generellt problem med "mega-pipeline":
- För många if-satser
- Otydligt ansvarsfördelning
- Svår att testa isolerat
- Svårt att förstå för andra

Ett mer robust angreppssätt är:
- Små, tydliga funktioner
- Konfigurationsstyrning
- Separera:
    - dataladdning
    - preprocessing
    - modell
    - träning
    - utvärdering

## Funktion som träningspipeline
Att kapsla en träningsprocess i en funktion är bra praktik.

Exempel på koncentuell struktur:
```python
def train_model(config):
    data = load_data(config)
    X_train, X_test = split(data, config)
    pipeline = build_pipeline(config)
    model = pipeline.fit(X_train)
    evaluate(model)
    return model
```
Detta ger:
- Återanvändbarhet
- Testbarhet
- Möjlighet till experimentstyrning
- Enhetligt entry-point