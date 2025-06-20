# Lernjournal 1 Python

## Repository und Library

| | Bitte ausfüllen |
| -------- | ------- |
| Repository (URL)  | https://github.com/IliasBro/WordCountApp.git
| Kurze Beschreibung der App-Funktion | Die Web-Applikation erlaubt es, Text einzugeben und wahlweise die Anzahl der Buchstaben oder Wörter zu zählen. Die Eingabe erfolgt über ein HTML-Formular, die Verarbeitung im Backend mit Flask, und das Ergebnis wird dynamisch zurückgeliefert. |
| Verwendete Library aus PyPi (Name) | flask, jinja2, markupsafe, werkzeug|
| Verwendete Library aus PyPi (URL) | [https://pypi.org/project/Flask/](https://pypi.org/project/Flask/), [https://pypi.org/project/Jinja2/](https://pypi.org/project/Jinja2/), [https://pypi.org/project/MarkupSafe/](https://pypi.org/project/MarkupSafe/), [https://pypi.org/project/Werkzeug/](https://pypi.org/project/Werkzeug/)|


## App, Funktionalität

Ziel war es, eine kleine Flask-basierte Webanwendung zu erstellen, die Texteingaben auswertet. Benutzer geben Text ein und wählen aus, ob sie die Anzahl der Wörter oder Buchstaben zählen möchten. Das Backend verarbeitet die Eingabe und gibt die gezählte Anzahl als JSON-Response zurück.

Die Benutzeroberfläche (index.html) enthält:

* Ein Texteingabefeld
* Zwei Auswahlmöglichkeiten ("Wörter zählen" oder "Buchstaben zählen")
* Einen Absende-Button
* Eine Anzeige für das Ergebnis

<img src="images/python3_ehrenili.png" alt="python" style="max-width: 100%; height: auto;">

Die Logik im Backend (src/app.py):

* Liest die Form-Daten
* Prüft den gewählten Zählmodus ("words" oder "letters")
* Berechnet die Anzahl entsprechend und liefert die Antwort als JSON

## Dependency Management

Für die Abhängigkeitsverwaltung wurde pip verwendet. Die benutzten Libraries sind in der Datei requirements.txt dokumentiert.

Setup-Befehl:
pip install -r requirements.txt

## Deployment


Für das Deployment wurde Microsoft Azure App Service verwendet. Ziel war es, die lokale Flask-App als ZIP-Archiv auf Azure zu deployen und online zugänglich zu machen.

Vorgehen Schritt für Schritt:

Projekt vorbereiten (ZIP-Archiv erstellen):
zip -r app.zip . -x "*.venv*" "*.git*" "**pycache**/\*"

Azure Ressourcen erstellen:
az group create --name ehrenili54 --location switzerlandnorth
az appservice plan create --name ehrenili54 --resource-group ehrenili54 --sku B1 --is-linux
az webapp create --resource-group ehrenili54 --plan ehrenili54 --name ehrenili54 --runtime "PYTHON|3.11"

Startup-Command setzen:
az webapp config set --resource-group ehrenili54 --name ehrenili54 --startup-file "gunicorn src.app\:app"

Deployment durchführen:
az webapp deploy --resource-group ehrenili54 --name ehrenili54 --src-path flask-app/app.zip --type zip

Nach erfolgreichem Deployment war die Anwendung unter folgender URL verfügbar:
[https://ehrenili54.azurewebsites.net](https://ehrenili54.azurewebsites.net)

<img src="images/python1_ehrenili.png" alt="python" style="max-width: 100%; height: auto;">

<img src="images/python2_ehrenili.png" alt="python" style="max-width: 100%; height: auto;">




Fazit:
Die App konnte erfolgreich online bereitgestellt werden. Durch das Deployment mit Azure wurde deutlich, wie wichtig die korrekte Struktur des Projekts und ein funktionierender Startbefehl sind. Besonders hilfreich war die Nutzung von gunicorn für den produktiven Betrieb.
