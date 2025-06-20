# Lernjournal 3 ONNX

## Übersicht

| | Bitte ausfüllen |
| -------- | ------- |
| ONNX Modell für Analyse (Netron) | https://github.com/onnx/models/blob/main/validated/vision/classification/efficientnet-lite4/model/efficientnet-lite4-11-qdq.onnx |
| onnx-image-classification Fork (EfficientNet-Lite) | https://github.com/IliasBro/onnx-image-classification.git |

## Dokumentation ONNX Analyse

Ich habe das Modell EfficientNet-Lite4-qdq aus dem ONNX Model Zoo in Netron geladen. 

Architekturüberblick:

Das Modell basiert auf einer Convolutional Neural Network (CNN)-Struktur, wie auch in der Vorlesung (Folie 14) beschrieben. Bereits auf den ersten Blick erkennt man:

	•	Das Modell startet mit einem Eingabetensor (images:0), der Bilder in der Form (1, 3, 224, 224) erwartet.
	•	Es folgen mehrere Conv2D-Layer, oft kombiniert mit BatchNormalization, Relu oder Clip.
	•	Die Struktur besteht aus Bottleneck-Blöcken, wie typisch für EfficientNet-Modelle.
	•	Am Ende steht ein GlobalAveragePooling, gefolgt von einer Fully Connected Layer und einem Softmax-Operator (z. B. Softmax:0), der die Wahrscheinlichkeitsverteilung für die Bildklassen liefert.


<img src="images/onnx_qdq1_ehrenili.png" alt="Onnx" style="max-width: 100%; height: auto;">

<img src="images/onnx_qdq2_ehrenili.png" alt="Onnx" style="max-width: 100%; height: auto;">

Beispielhafte Operatoren:

Einige zentrale Operatoren im Graph:

	•	Conv: Faltungsschicht zur Merkmalserkennung.
	•	Relu: Aktivierungsfunktion.
	•	Add: Verknüpft Zwischenergebnisse in ResNet-ähnlichen Blöcken.
	•	Clip: Begrenzung von Werten – typisch bei quantisierten Modellen (wie hier bei -qdq).
	•	QLinearConv, QLinearAdd: Quantisierte Varianten der Standardoperatoren, verwendet zur Performance-Optimierung.

Besonderheiten bei EfficientNet-Lite4-qdq:

Da es sich um eine quantisierte Version handelt (qdq = Quantize-DeQuantize), werden viele Operatoren in ihrer quantisierten Form dargestellt. Diese sind effizienter für den Einsatz auf mobilen Geräten oder Edge-Devices. Sie zeigen, wie ONNX auch quantisierte Modelle gut lesbar darstellen kann.
## Dokumentation onnx-image-classification

Ich habe drei Bilder mit zwei Varianten des EfficientNet-Lite4-Modells getestet:

- `efficientnet-lite4-11.onnx` (Standardmodell, Float32)
- `efficientnet-lite4-11-qdq.onnx` (quantisiertes Modell mit QDQ-Operatoren)

### Ergebnisse im Vergleich

| Bild       | Modell            | Top-1 Klasse                                     | Wahrscheinlichkeit |
|------------|-------------------|--------------------------------------------------|---------------------|
| Matterhorn | Standardmodell    | lakeside, lakeshore                              | 0.0131              |
|            | Quantisiertes     | lakeside, lakeshore                              | **0.1881**          |
| Car        | Standardmodell    | beach wagon / station wagon                      | 0.2627              |
|            | Quantisiertes     | beach wagon / station wagon                      | **0.2897**          |
| Train      | Standardmodell    | streetcar, tram                                  | **0.3470**          |
|            | Quantisiertes     | electric locomotive                              | 0.1753              |

### Beobachtungen

- Bei *Matterhorn* und *Car* liefert das quantisierte Modell dieselbe Klasse wie das Standardmodell, aber mit **höherer Konfidenz**.
- Beim *Train*-Bild unterscheiden sich die Ergebnisse leicht: Beide erkennen einen Zug, aber mit unterschiedlichen Klassenbezeichnungen.
- Die Resultate zeigen, dass das quantisierte Modell insgesamt **vergleichbare Vorhersagequalität** bei reduzierter Modellgröße bietet.

### Fazit

Die Tests belegen, dass das quantisierte Modell `efficientnet-lite4-11-qdq.onnx` ähnlich zuverlässige Ergebnisse liefert wie das Float32-Modell. Durch die kompaktere Struktur ist es besonders für den Einsatz auf mobilen oder Edge-Geräten geeignet.


<img src="images/matterhorn1_ehrenili.png" alt="Onnx" style="max-width: 100%; height: auto;">

<img src="images/matterhorn2_ehrenili.png" alt="Onnx" style="max-width: 100%; height: auto;">

<img src="images/car1_ehrenili.png" alt="Onnx" style="max-width: 100%; height: auto;">

<img src="images/car2_ehrenili.png" alt="Onnx" style="max-width: 100%; height: auto;">

<img src="images/train1_ehrenili.png" alt="Onnx" style="max-width: 100%; height: auto;">

<img src="images/train2_ehrenili.png" alt="Onnx" style="max-width: 100%; height: auto;">