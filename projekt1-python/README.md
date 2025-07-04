# Projekt 1 Python

## Übersicht

| | Bitte ausfüllen |
| -------- | ------- |
| Variante | Eigenes Projekt |
| Datenherkunft | Format HTML, Data Scraping von Webseite|
| Datenherkunft | https://www.beliebte-vornamen.de |
| ML-Algorithmus | RandomForestClassifier |
| Repo URL | https://github.com/IliasBro/Project1.git |

## Dokumentation

### Data Scraping

Für das Projekt wurde ein Web-Scraper mit Python und BeautifulSoup entwickelt, der auf der Website beliebte-vornamen.de alle verfügbaren Namensrankings von 1890 bis 2026 extrahiert hat.
Der Scraper ist so konzipiert, dass er:

	•	die Dekadenübersichten durchläuft (z. B. 1990er, 2000er Jahre),
	•	die Jahreslinks auf jeder Seite dynamisch abruft,
	•	für neuere Seiten (2020–2026) ein separates Scraping-Logik nutzt, da diese anders strukturiert sind.
	•	Ergebnis: CSV-Datei scraped_data.csv mit über 9.000 Einträgen (year, gender, rank, name).

Der Code wurde modular aufgebaut (Funktionen für scrape_year_page, extract_year_links etc.) und ist vollständig automatisiert.

<img src="images/projekt1_website_ehrenili.png" alt="projekt1" style="max-width: 100%; height: auto;">

<img src="images/projekt1_mongodb_ehrenili.png" alt="projekt1" style="max-width: 100%; height: auto;">

<img src="images/projekt1_scrapedData_ehrenili.png" alt="projekt1" style="max-width: 100%; height: auto;">

### Training

Die Modelllogik wurde in einer eigenen Datei model.py entwickelt. Hier wurde ein „Regel-basiertes Modell“ erstellt, das:
	•	keine klassische ML-Methode verwendet, sondern:
	•	das letzte Jahr eines Namens
	•	den besten Rang
	•	und daraus einen kombinierten Score berechnet, um die Wahrscheinlichkeit für eine Platzierung in den Top 50 im Jahr 2027 zu schätzen.

Das Training bestand also aus:
	•	Berechnung von Features (most_recent_year, best_ranking)
	•	Speichern dieser Feature-Matrix + globaler Konstanten (MIN_YEAR, MAX_YEAR, MAX_RANK) als Pickle-Datei model/model.plk

📌 Wichtige Änderung im Projektverlauf:

Das trainierte Modell wurde später ersetzt durch eine regelbasierte Formel direkt in app.py, um das Verhalten nachvollziehbarer und vollständig datenbankgestützt zu machen. Statt auf model.plk zuzugreifen, berechnet die App die Wahrscheinlichkeit auf Basis von:

	•	dem letzten vorkommenden Jahr des Namens,
	•	der besten historischen Platzierung,
	•	einer kombinierten Gewichtung (Jahr + Rang),
	•	und einer Skalierung auf einen Bereich zwischen 5 % und 95 %.

Die Berechnung erfolgt live aus der MongoDB und macht das Modell besonders transparent, wartbar und dynamisch.


<img src="images/projekt1_model_ehrenili.png" alt="projekt1" style="max-width: 100%; height: auto;">


### ModelOps Automation

Es wurde ein automatisierter MLOps-Workflow eingerichtet, um eine möglichst einfache Wiederverwendbarkeit des Projekts zu gewährleisten:

	•	Nutzung einer .env-Datei zur zentralen Verwaltung sensibler Zugangsdaten (MongoDB URI, Azure Credentials, DockerHub Token usw.)
	•	Das Backend (app.py) lädt automatisch alle Umgebungsvariablen mit python-dotenv.
	•	MongoDB wird dynamisch verwendet, um:
	•	Predictions live abzuspeichern,
	•	Trainingsdaten vollständig dynamisch zu verarbeiten (kein statisches Modell mehr).

Zusätzlich:

	•	GitHub Actions CI/CD Workflow (deploy.yml)
	•	Triggert automatisch bei Pushes auf main
	•	Baut ein Docker-Image
	•	Pusht das Image zu DockerHub
	•	Updated die Azure Container App (inkl. Setzen von Umgebungsvariablen)

### Deployment

Die Applikation ist vollständig containerisiert mit Docker und wird auf Azure Container Apps deployed.

	•	Dockerfile definiert ein sauberes, plattformunabhängiges Python-Environment (mit --platform linux/amd64).
	•	Frontend ist in einfachem HTML/JS realisiert und über Flask static_folder eingebunden.
	•	Die App kann über Azure öffentlich genutzt werden:
	•	GET /predict?name=Anna&gender=female liefert die Wahrscheinlichkeit in JSON.
	•	MongoDB wird über Azure Cosmos DB mit MongoDB API gehostet und verbindet sich via MONGODB_URI.


<img src="images/projekt1_dockerfile_ehrenili.png" alt="projekt1" style="max-width: 100%; height: auto;">