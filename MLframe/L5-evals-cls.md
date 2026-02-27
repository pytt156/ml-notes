# Evaluation metrics - klassifikation

### 1. Vad gör vi vid en utvärdering?
Vid klassifikation försöker modellen förutsäga en kategori.

Under träning säger vi:

> "Detta är korrekt label."

Under utvärderingen frågar vi:

> "Stämmer modellens prediktion med den sanna labeln?"

Resultatet är en jämförelse mellan:
- Prediktion
- Faktiskt värde (ground truth)

## Confusion matrix
För binär klassifikation används en 2x2-matris
```
        Pred 1  Pred 0
True 1  TP      FN
True 0  FP      TN
```

### Definitioner
- **True positive (TP)**: Modellen säger positiv -> och det var positivt
- **True negative (TN)**: Modellen säger negativ -> och det var negativt
- **False positive (FP)**: Modellen säger positiv -> och det var **inte** positivt
- **True positive (FN)**: Modellen säger negativ -> och det var **inte** negativt

## Accuracy
### Definition
$$
Accuracy = \frac{TP + TN}{TP + TN + FP + FN}
$$
Mäter: andel korrekta prediktioner av alla prediktioner

**Nackdelar**:
Accuracy fungerar dåligt vid obalanserade dataset.
#### Exempel:
- 100 000 personer
- 5% har diabetes

En modell som säger "Ingen har diabetes" får 95000 rätt och 5000 fel, dvs 95% accuracy, trots att modellen är kliniskt oanvändbar.

## Precision:
### Definition:
$$
Precision = \frac{TP}{TP + FP}
$$
Mäter: av alla som modellen var positiva - hur många var faktiskt positiva?

Precision är viktig när:
- False positives är kostsamma
- Exempel: spamfilter, juridiska beslut, vissa medicinska tester

## Recall (Sensitivity)
$$
Recall = \frac{TP}{TP + FN}
$$
Mäter: av alla som faktiskt är positiva, hur många fångade modellen?

Viktig när:
- False negatives är farliga
- Exempel: cancerdiagnostik