# DEZSYS GK8.3 – Jenkins Hello Spencer Pipeline

**Autoren:** Fabian Steinhauser, Sebastian Nosek

## Überblick

Dieses Projekt zeigt eine Jenkins CI/CD Pipeline für eine einfache Python-Flask-Anwendung.

Ziel der Aufgabe war es, folgende Schritte automatisch über Jenkins auszuführen:

- Quellcode aus GitHub laden
- Abhängigkeiten installieren
- Tests ausführen
- Anwendung starten
- REST-API-Endpunkt testen
- Pipeline erfolgreich abschließen

Repository:

```text
https://github.com/FabianBoolean/DEZSYS_GK_JENKINS_HELLOSPENCER_Steinhauser
```

---

# Verwendete Technologien

- Jenkins
- Docker
- GitHub
- Python 3.11
- Flask
- Pytest
- curl

---

# Projektstruktur

Das Projekt besteht aus:

```text
src/
tests/
Dockerfile
Jenkinsfile
requirements.txt
count.txt
```

### Screenshot – Projektstruktur

![Projektstruktur](screenshots/Bildschirmfoto%202026-05-29%20um%2017.28.25.png)

---

# Jenkins Pipeline einrichten

In Jenkins wurde eine neue Pipeline mit folgendem Namen erstellt:

```text
GK83-HelloSpencer-Pipeline
```

Am Anfang war noch die Standardkonfiguration mit einem leeren `Pipeline script` eingestellt.

### Screenshot – Erste Pipeline-Konfiguration

![Erste Pipeline-Konfiguration](screenshots/Bildschirmfoto%202026-05-29%20um%2018.22.46.png)

---

# Pipeline aus GitHub laden

Die Konfiguration wurde danach auf folgende Einstellung geändert:

```text
Definition: Pipeline script from SCM
SCM: Git
Repository URL: https://github.com/FabianBoolean/DEZSYS_GK_JENKINS_HELLOSPENCER_Steinhauser.git
Branch Specifier: */main
Script Path: Jenkinsfile
```

Dadurch lädt Jenkins das `Jenkinsfile` direkt aus dem GitHub-Repository.

### Screenshot – Pipeline aus SCM

![Pipeline aus SCM](screenshots/Bildschirmfoto%202026-05-29%20um%2018.24.07.png)

---

# Erster Pipeline-Lauf

Nach dem Speichern der Konfiguration wurde der erste Jenkins-Build gestartet.

Der erste Build wurde noch nicht erfolgreich abgeschlossen, da in der Pipeline noch ein Schritt enthalten war, der dauerhaft weiterlief.

### Screenshot – Erster Build

![Erster Build](screenshots/Bildschirmfoto%202026-05-29%20um%2018.25.45.png)

---

# Jenkinsfile

Das `Jenkinsfile` beschreibt die einzelnen Schritte der Pipeline.

Die Pipeline verwendet ein Python-Docker-Image:

```groovy
agent {
    docker { 
        image 'python:3.11' 
        args '-p 5556:5556'
    }
}
```

Dadurch wird die Anwendung in einer sauberen Python-Umgebung ausgeführt.

---

# Pipeline-Schritte

## Pre-Build Cleanup

Vor dem Start werden alte Python-Prozesse beendet.

## Checkout

Der Quellcode wird aus GitHub geladen.

## Build

Die benötigten Python-Pakete werden installiert:

```bash
pip install flask
pip install requests
pip install pytest
```

## Test

Die Unit-Tests werden ausgeführt:

```bash
python -m pytest tests/test_hello.py -v
```

## Run

Die Flask-Anwendung wird gestartet:

```bash
nohup python src/hello.py > app.log 2>&1 &
```

Danach wird der API-Endpunkt getestet:

```bash
curl http://localhost:5556/api/hello
```

Erwartete Ausgabe:

```json
{
  "counter": 31,
  "message": "Hello Spencer",
  "status": "success"
}
```

## Test API

Zusätzlich werden API-Tests ausgeführt:

```bash
python tests/test_api.py
```

## Post Actions

Nach den Tests wird die Flask-Anwendung wieder beendet.

---

# Problem und Lösung

Beim ersten Pipeline-Lauf wurde die Pipeline nicht korrekt beendet, weil ein Schritt enthalten war, der dauerhaft weiterlief:

```groovy
sleep infinity
```

Dadurch blieb Jenkins im Build hängen.

Die Lösung war, diesen Schritt zu entfernen und die Anwendung am Ende über die Post Actions wieder zu beenden.

Danach konnte die Pipeline vollständig und erfolgreich durchlaufen.

---

# Erfolgreicher Pipeline-Build

Nach der Anpassung des Jenkinsfiles wurde die Pipeline erfolgreich ausgeführt.

Im Console Output war zu sehen:

```text
Ran 2 tests

OK
Finished: SUCCESS
```

### Screenshot – Erfolgreicher Build

![Erfolgreicher Build](screenshots/Bildschirmfoto%202026-05-29%20um%2018.31.27.png)

---

# Ergebnis

Die fertige Pipeline führt automatisch folgende Schritte aus:

1. Quellcode aus GitHub laden
2. Python-Umgebung mit Docker starten
3. Abhängigkeiten installieren
4. Unit-Tests ausführen
5. Flask-Anwendung starten
6. REST-API testen
7. Laufende Prozesse aufräumen
8. Build erfolgreich abschließen

---

# Fazit

Die CI/CD Pipeline wurde erfolgreich mit Jenkins umgesetzt.

Jenkins lädt den Code automatisch aus GitHub, installiert die benötigten Abhängigkeiten, führt Tests aus, startet die Flask-Anwendung und überprüft den REST-Endpunkt.

Der finale Build wurde erfolgreich abgeschlossen mit:

```text
Finished: SUCCESS
```