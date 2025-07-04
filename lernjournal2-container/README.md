# Lernjournal 2 Container

## Docker Web-Applikation

### Verwendete Docker Images

| | Bitte ausfüllen |
| -------- | ------- |
| Image 1 | postgres |   
| Image 1 | https://hub.docker.com/_/postgres | 
| Image 2 | adminer | 
| Image 2 | https://hub.docker.com/_/adminer | 
| Docker Compose | https://github.com/IliasBro/MDMDockerCompose.git | 

### Dokumentation manuelles Deployment

Für das manuelle Deployment habe ich zunächst lokal überprüft, wie man einen einzelnen PostgreSQL-Datenbankcontainer mit Docker starten kann. Dabei habe ich folgenden Befehl verwendet:

docker run --name postgres-db \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_DB=mydatabase \
  -p 5432:5432 \
  -d postgres:latest

Damit wurde ein Container mit einer PostgreSQL-Datenbank gestartet, der auf Port 5432 erreichbar war.

Um die Datenbank grafisch zu verwalten, habe ich zusätzlich einen Adminer-Container manuell gestartet:

docker run --name adminer \
  --link postgres-db:db \
  -p 8080:8080 \
  -d adminer

Anschließend konnte ich Adminer im Browser über http://localhost:8080 öffnen und mich mit folgenden Daten anmelden:
	•	System: PostgreSQL
	•	Server: postgres-db
	•	Benutzername: admin
	•	Passwort: secret
	•	Datenbank: mydatabase

Das manuelle Deployment hat funktioniert, war aber deutlich aufwendiger als mit Docker Compose, vor allem wenn mehrere Container konfiguriert und gestartet werden müssen.

<img src="images/compose1_ehrenili.png" alt="compose" style="max-width: 100%; height: auto;">

<img src="images/compose2_ehrenili.png" alt="compose" style="max-width: 100%; height: auto;">



### Dokumentation Docker-Compose Deployment

Im nächsten Schritt habe ich das gleiche Setup mit Docker Compose umgesetzt. Dafür habe ich eine Datei docker-compose.yml erstellt, die beide Container und ein gemeinsames Netzwerk definiert. Die Datei sah so aus:

version: "3.8"

services:
  postgres-db:
    image: postgres:latest
    container_name: postgres-db
    restart: always
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: mydatabase
    ports:
      - "5432:5432"
    networks:
      - lernjournal2
    volumes:
      - postgres_data:/var/lib/postgresql/data

  adminer:
    image: adminer
    container_name: adminer
    restart: always
    ports:
      - "8080:8080"
    depends_on:
      - postgres-db
    networks:
      - lernjournal2

networks:
  lernjournal2:

volumes:
  postgres_data:


Ich habe den Befehl docker-compose up -d im Terminal ausgeführt, um die Container im Hintergrund zu starten. Danach war Adminer unter http://localhost:8080 verfügbar.

Die Verbindung zur Datenbank funktionierte genauso wie beim manuellen Deployment – nur einfacher und übersichtlicher. Alle Konfigurationen (z. B. Benutzername, Passwort, Port) sind zentral in der YAML-Datei gepflegt.

Auch das Stoppen der Container war durch den Befehl docker-compose down deutlich einfacher als beim manuellen Löschen einzelner Container.

## Deployment ML-App

### Variante und Repository

| Gewähltes Beispiel | Bitte ausfüllen |
| -------- | ------- |
| onnx-sentiment-analysis | Nein |
| onnx-image-classification | Ja |
| Repo URL Fork | https://github.com/IliasBro/onnx-image-classification.git |
| Docker Hub URL | https://hub.docker.com/repository/docker/iliasbro/ehrenilirepo/general |


In diesem Projekt habe ich ein GitHub-Repository verwendet, das eine ONNX-basierte Bildklassifikations-App enthält:
https://github.com/IliasBro/onnx-image-classification.git

Ziel war es, die App in mehreren Umgebungen zu deployen: lokal, in Azure Web Apps, Azure Container Instances (ACI) und Azure Container Apps (ACA).

### Dokumentation lokales Deployment

Zuerst habe ich das Projekt lokal getestet, um sicherzustellen, dass es überhaupt korrekt funktioniert. Ich bin wie folgt vorgegangen:

	1.	Projekt geklont:

    git clone https://github.com/IliasBro/onnx-image-classification.git

    cd onnx-image-classification

    2.	Docker Image lokal gebaut:

    docker build -t iliasbro/ehrenilirepo .

    3.	Container lokal gestartet:

    docker run -p 5000:5000 iliasbro/ehrenilirepo


Die Anwendung war anschließend unter http://localhost:5000 erreichbar.

Lernfaktor: Ich habe verstanden, wie man ein Docker-Image aus einem lokalen Projekt erstellt und startet, und wie Portweiterleitung funktioniert.

### Dokumentation Deployment Azure Web App

Ich habe versucht, die Anwendung in Azure Web Apps als Container bereitzustellen:

	1.	Ressourcengruppe erstellt:

    az group create --name ehrenili-docker --location switzerlandnorth

    2.	App Service Plan erstellt:

    az appservice plan create --name onnx-image-classification --resource-group ehrenili-docker --sku F1 --is-linux

    3.	App erstellt:

    az webapp create --resource-group ehrenili-docker --plan onnx-image-classification --name onnx-image-classificatio5400 --container-image iliasbro/ehrenilirepo:latest


 Ergebnis: Die App konnte nicht erfolgreich gestartet werden. Die Konfiguration lief zwar fehlerfrei durch, aber die Anwendung wurde nicht korrekt geladen. Vermutlich lag es an inkompatiblen Ports oder Anforderungen des ONNX-Modells, die nicht mit dem eingeschränkten F1-Plan kompatibel sind.

Lernfaktor: Ich habe gelernt, wie Azure Web Apps mit Docker Images funktionieren, welche Limits beim F1-Plan bestehen und warum man oft mehr Kontrolle braucht als Web Apps bieten.

### Dokumentation Deployment ACI

Zweiter Versuch mit Azure Container Instances:

	1.	Ressourcengruppe erstellt:

    az group create --location switzerlandnorth --name ehrenili-aci

    2. App erstellt:

    az container create \
    --resource-group ehrenili-aci \
    --name onnx-image-classification \
    --image iliasbro/ehrenilirepo:latest \
    --dns-name-label onnx-image-classification5430 \
    --ports 5000 \
    --os-type Linux \
    --cpu 1 \
    --memory 1.5

Ergebnis: Leider war die Anwendung auch hier nicht stabil erreichbar. Der Container lief nicht korrekt an oder wurde direkt wieder gestoppt. Ursache könnten Plattforminkompatibilitäten oder fehlende Health Checks gewesen sein.

Lernfaktor: Ich habe den Umgang mit ACI gelernt, insbesondere wie man Ressourcen wie CPU/RAM zuteilt, und wie wichtig Logs und Überwachung beim Deployment sind.

### Dokumentation Deployment ACA

Der dritte Versuch mit Azure Container Apps war erfolgreich.

	1.	Ressourcengruppe erstellt:
    az group create --location switzerlandnorth --name ehrenili-aca

    2. Container App Environment erstellt:

    az containerapp env create --name onnx54 --resource-group ehrenili-aca --location westeurope

    3.	Docker Image für Linux AMD64-Plattform gebaut:

    docker build --platform linux/amd64 -t iliasbro/ehrenilirepo:latest .
    docker push iliasbro/ehrenilirepo:latest

    4.	Container App erstellt:

    az containerapp create \
    --name onnx-image54540000 \
    --resource-group ehrenili-aca \
    --environment onnx54 \
    --image iliasbro/ehrenilirepo:latest \
    --target-port 5000 \
    --ingress external \
    --query properties.configuration.ingress.fqdn


Ergebnis: Erfolgreich! Die Anwendung war erreichbar unter:
https://onnx-image54540000.happyground-6c6050c7.westeurope.azurecontainerapps.io/

Lernfaktor: Ich habe verstanden, wie ACA funktioniert, wie man Plattformprobleme durch den Build-Parameter löst (--platform linux/amd64) und warum ACA eine gute Kombination aus Flexibilität und Einfachheit bietet.

<img src="images/onnx_1_ehrenili.png" alt="deploy" style="max-width: 100%; height: auto;">

<img src="images/onnx_2_ehrenili.png" alt="deploy" style="max-width: 100%; height: auto;">



Fazit

Auch wenn nur der ACA-Ansatz erfolgreich war, habe ich beim Durchprobieren alle wichtigen Azure-Containerdienste kennengelernt. Ich verstehe jetzt, welche Lösung sich für welche Anforderungen eignet – und warum ein funktionierendes Deployment mehr ist als nur „ein Image starten“.
