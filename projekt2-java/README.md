# Projekt 2 Java

## Übersicht

| | Bitte ausfüllen |
| -------- | ------- |
| Variante | Vorhandener Datensatz  |
| Datensatz (wenn selbstgewählt) | Format JPG, Schmetterlingsbilder unterteilt in Train und Test Datasets |
| Datensatz (wenn selbstgewählt) | https://www.kaggle.com/datasets/phucthaiv02/butterfly-image-classification |
| Modell (wenn selbstgewählt) | Beispiel: https://djl.ai/examples/docs/train_cifar10_resnet.html |
| ML-Algorithmus | Supervised Learning mit CNN |
| Repo URL | https://github.com/IliasBro/Mariposas.git |



## Dokumentation

Projektziel: Entwicklung einer Java-basierten Webanwendung zur Klassifikation von Schmetterlingsbildern. Die Anwendung nutzt die Deep Java Library (DJL) mit PyTorch und Spring Boot, um Bilder zu analysieren und die Schmetterlingsart zu bestimmen.

Projektstruktur: Das Projekt folgt einer modularen Struktur mit separaten Verzeichnissen für Daten, Modelle, Quellcode und Konfigurationsdateien. Ein Dockerfile ermöglicht die Containerisierung der Anwendung für eine konsistente Bereitstellung.

<img src="images/projekt2_mariposasWebsite_ehrenili.png" alt="projekt2" style="max-width: 100%; height: auto;">

 
### Daten

Datenquelle: Ein Dataset von Kaggle.com mit Schmetterlingsbildern, organisiert in Klassenverzeichnissen, z. B. dataset/train/monarch, dataset/train/swallowtail usw.

Vorverarbeitung: Ein Shell-Skript (ExampleImages.sh) und ein Python-Skript (LooseDataToFolders.py) wurden verwendet, um Beispielbilder zu extrahieren und die Daten für das Training vorzubereiten.

Datenaufteilung:

	•	Trainingsdaten: Bilder in dataset/train/
	•	Testdaten: Bilder in dataset/test/


<img src="images/projekt2_kaggle_ehrenili.png" alt="projekt2" style="max-width: 100%; height: auto;">

<img src="images/projekt2_pythonScript_ehrenili.png" alt="projekt2" style="max-width: 100%; height: auto;">

### Training

Framework: Deep Java Library (DJL) mit PyTorch-Backend.

Trainingsprozess:

	•	Modellarchitektur: Ein Convolutional Neural Network (CNN) wurde für die Bildklassifikation implementiert.
	•	Hyperparameter: Standardwerte wurden verwendet, mit der Möglichkeit zur Anpassung über Konfigurationsdateien.
	•	Training: Das Modell wurde mit den vorbereiteten Trainingsdaten trainiert und anschließend mit den Testdaten validiert.

Ergebnisse: Das trainierte Modell zeigte eine hohe Genauigkeit bei der Klassifikation der Schmetterlingsarten auf den Testdaten.

### Inference / Serving

Anwendung: Eine Spring Boot Webanwendung ermöglicht das Hochladen von Schmetterlingsbildern zur Klassifikation.

Ablauf:

	1.	Benutzer lädt ein Bild über die Weboberfläche hoch.
	2.	Das Bild wird vom Server empfangen und an das trainierte Modell übergeben.
	3.	Das Modell gibt die vorhergesagte Schmetterlingsart mit einer Wahrscheinlichkeit zurück.
	4.	Das Ergebnis wird dem Benutzer angezeigt.

Technologien: Spring Boot für die Webanwendung, DJL für die Modellinferenz.

### Deployment

Containerisierung: Ein Dockerfile wurde erstellt, um die Anwendung in einem Docker-Container bereitzustellen.

Build-Prozess:

	1.	Die Anwendung wird mit Maven gebaut: ./mvnw clean package.
	2.	Ein Docker-Image wird erstellt: docker build -t mariposas ..
	3.	Der Container wird gestartet: docker run -p 8080:8080 mariposas.

Vorteile: Die Containerisierung ermöglicht eine konsistente und portable Bereitstellung der Anwendung über verschiedene Umgebungen hinweg.


<img src="images/projekt2_azurePortal_ehrenili.png" alt="projekt2" style="max-width: 100%; height: auto;">
