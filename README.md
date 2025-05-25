# 🔁 DevOps 07 – Continuous Integration (CI)

## 👥 Zusammenarbeit

### 📄 Person und Repository

|                          |                                                                                              |
|--------------------------|----------------------------------------------------------------------------------------------|
| Bearbeiter               | Alessio Pugliese                                                                             |
| Repository               | [DevOps-07-CI](https://github.com/pugliale/DevOps-07-CI)                                     |

---

## 💻 Verwendete Systeme & Tools

| Tool / Technologie     | Beschreibung                                                                                   |
|------------------------|-----------------------------------------------------------------------------------------------|
| **Jenkins**            | CI/CD Server zur Automatisierung von Builds, Tests und Deployments                           |
| **Gradle**             | Build Tool für Java-Projekte                                                                  |
| **JaCoCo**             | Code Coverage Analyse-Tool                                                                    |
| **JUnit**              | Test-Framework für automatisierte Unit Tests                                                  |
| **SonarQube**          | Analyseplattform zur Bewertung von Codequalität                                               |
| **Docker**             | Containerplattform zur Ausführung von Jenkins und Agenten                                     |
| **Node.js & npm**      | JavaScript-Laufzeitumgebung für das Frontend                                                  |
| **Selenium IDE**       | Integrationstests für UI-Tests                                                                |
| **Git & GitHub**       | Versionskontrolle und Quellcodeverwaltung                                                     |

---

## 📖 Theoretischer Hintergrund

### 🔁 Was ist Continuous Integration?

**Continuous Integration (CI)** bezeichnet einen Prozess in der Softwareentwicklung, bei dem Code-Änderungen kontinuierlich in ein zentrales Repository integriert und automatisch gebaut sowie getestet werden. Dadurch können Fehler früh erkannt und die Qualität der Software sichergestellt werden.

Ziele von CI:
- Frühzeitige Fehlererkennung durch automatische Builds und Tests
- Verkürzung von Release-Zyklen
- Reduktion von Integrationsproblemen

---

### ⚙️ Jenkins

**Jenkins** ist eines der bekanntesten Open-Source-Werkzeuge zur Umsetzung von CI/CD-Prozessen. Es bietet:
- Plugins zur Integration von Build-Tools, Test-Frameworks und Analyseplattformen
- Weboberfläche zur Konfiguration und Überwachung von Jobs
- Unterstützung für Pipelines (sowohl deklarativ als auch skriptbasiert)

### 🔬 Build & Test Tools

| Tool        | Funktion                                                                 |
|-------------|--------------------------------------------------------------------------|
| **Gradle**  | Kompiliert Quellcode, führt Tests aus, erstellt Artefakte                |
| **JUnit**   | Führt automatisierte Tests auf Klassen-/Methodenebene aus                |
| **JaCoCo**  | Misst, wie viel Code von Tests tatsächlich abgedeckt wird                |

### 🧠 Codeanalyse mit SonarQube

SonarQube analysiert Java-Code auf:
- **Bugs** (Fehler im Code)
- **Code Smells** (schlechter Stil)
- **Security Hotspots**
- **Coverage**
- Und vieles mehr

SonarQube kann direkt in Jenkins integriert werden, sodass bei jedem Commit eine aktuelle Analyse durchgeführt wird. Damit entsteht eine **nahtlose Qualitätskontrolle** im Entwicklungsprozess.

---

## 🔧 Praktische Umsetzung
### 📦 Jenkins Setup & JUnit-Integration

#### 🚀 Jenkins Installation via Docker

Jenkins wurde über Docker gestartet, um ein isoliertes und portables Setup zu ermöglichen:

```bash
docker run -p 8080:8080 -p 50000:50000 --name jenkins \
  -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```

<img src="images/Bild1.png" width="600">

Nach dem Start war Jenkins über `http://localhost:8080` erreichbar. Das Unlock-Passwort wurde im Log ausgegeben und im Browser eingegeben.

<img src="images/Bild2.png" width="600">
<img src="images/Bild3.png" width="600">

#### ⚙️ Erste Konfiguration

- Standard-Plugins wurden installiert
- Benutzerkonto für Jenkins wurde erstellt
- Die Oberfläche war danach vollständig einsatzbereit

<img src="images/Bild4.png" width="600">

---

### 🔧 Gradle Build einrichten

Ein neuer **Freestyle-Job** wurde in Jenkins erstellt. Ziel war es, das Java-Backend-Projekt mit Gradle zu bauen und Tests automatisch auszuführen.

#### 🔗 GitHub-Verbindung

Das Repository `https://github.com/pugliale/DevOps-07-CI` wurde als Quelle hinterlegt.

<img src="images/Bild5.png" width="600">

#### 🧪 Build-Schritte

Als Build-Step wurde folgender Befehl definiert:

```bash
./gradlew test
```

Damit werden alle Unit Tests mit **JUnit 5** ausgeführt.

---

### ✅ Erstes Build & Testergebnis

Der Build wurde manuell ausgelöst. Jenkins führte den Testlauf über Gradle aus.

<img src="images/Bild6.png" width="600">
<img src="images/Bild7.png" width="600">

Im Jenkins-Job war ersichtlich:
- Der Build war **erfolgreich**
- Die Testklasse `ScoreKeeperTest` wurde erkannt und ausgeführt
- Alle Tests bestanden

---

### 📊 Testreport-Visualisierung (JUnit)

Durch das Aktivieren der JUnit-Testreportanzeige im Jenkins-Job konnte eine grafische Übersicht erzeugt werden:

- Anzahl Testfälle
- Erfolgsquote
- Verlauf über Builds hinweg

<img src="images/Bild8.png" width="600">

---

### 🧪 Test mit negativem Beispiel

Zur Validierung wurde gezielt ein fehlerhafter Testfall eingebaut. Anschliessend wurde der Build erneut ausgeführt:

- Jenkins erkannte den **fehlgeschlagenen Test**
- Der Build-Status wechselte auf **rot (fehlerhaft)**

Dies demonstriert die **Verlässlichkeit und Aussagekraft automatisierter Tests im CI-Prozess**.

<img src="images/Bild9.png" width="600">
<img src="images/Bild10.png" width="600">

---
### 🐳 Docker, Artefakte & SonarQube Analyse

---

### 🐳 Jenkins Job für Docker Build

Ein zweiter Jenkins Job wurde erstellt, um zusätzlich zum Gradle-Build ein **Docker-Image** zu erzeugen. Ziel war es, das fertige Backend-Projekt zu containerisieren.

#### ⚙️ Build Steps:
- Zuerst `./gradlew build` zum Erzeugen des `.jar`-Artefakts
- Dann Docker-Build mit:

```bash
docker build -t devopsdemo-backend .
```

<img src="images/Bild11.png" width="600">

Der Build lief erfolgreich durch – Jenkins konnte das Image erstellen.

---

### 📦 Gradle-Build-Output: .jar-Artefakt

Im `build/libs/`-Ordner wurde die **kompilierte Anwendung als .jar-Datei** erzeugt:

- `devopsdemo-0.0.1-SNAPSHOT.jar`

Dieses Artefakt kann direkt in Docker-Container eingebettet oder zur Weiterverarbeitung genutzt werden.

<img src="images/Bild12.png" width="600">

---

### 🧪 Testfall-Fehlschlag zur Verifizierung

Zur Demonstration der Zuverlässigkeit wurde ein **gezielter Fehler** im Code eingebaut. Ein erneuter Build in Jenkins zeigte:

- Test schlägt fehl
- Jenkins meldet Fehler klar im Log
- Build wird rot markiert

Dies ist wichtig, um **Regressionen frühzeitig zu erkennen**.

<img src="images/Bild13.png" width="600">

---

### 🔍 SonarQube Analyse im Jenkins Build

#### 🧱 Vorbereitung:

- SonarQube lokal über Docker gestartet (`localhost:9000`)
- Token für Projekt `DevOpsDemo-Backend` erstellt

#### ⚙️ Jenkins SonarQube Build-Konfiguration:

In den **Build-Schritten** des Jenkins-Jobs wurde ein zusätzlicher Schritt ergänzt:

```bash
./gradlew sonarqube \
  -Dsonar.projectKey=DevOpsDemo-Backend \
  -Dsonar.projectName='DevOpsDemo-Backend' \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.token=<TOKEN>
```

<img src="images/Bild14.png" width="600">

---

### 📊 SonarQube Ergebnisanalyse

Im SonarQube Dashboard wurden alle Metriken und Kennzahlen angezeigt:

- A-Rating für Maintainability, Reliability und Security
- 100 % Code Coverage dank JaCoCo
- Kein Duplicate Code
- Keine Bugs oder Security Hotspots

<img src="images/Bild15.png" width="600">
<img src="images/Bild16.png" width="600">
<img src="images/Bild17.png" width="600">

Der Code gilt damit laut Analyse als **technisch exzellent**.

---

### 📁 Projektdateien & Struktur

Zur Übersicht wurde auch die Jenkins-Konfiguration geprüft:

- Die `.jar`-Datei lag im Build-Ordner
- Dockerfile wurde korrekt erkannt
- Alle relevanten Dateien wurden versioniert

<img src="images/Bild18.png" width="600">

---

### 🧱 Jenkins Pipeline mit Jenkinsfile & Cloud-Agent

---

### 📝 Jenkinsfile (declarative Pipeline)

Ein neues `Jenkinsfile` wurde erstellt und ins Repository eingecheckt. Dieses steuert den Build-Prozess deklarativ in mehreren Stages:

```groovy
pipeline {
    agent any

    tools {
        gradle 'Gradle'
    }

    stages {
        stage('Build') {
            steps {
                sh './gradlew build'
            }
        }

        stage('Test') {
            steps {
                sh './gradlew test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh './gradlew sonarqube'
                }
            }
        }
    }
}
```

<img src="images/Bild19.png" width="600">
<img src="images/Bild20.png" width="600">

---

### 🧪 Jenkins Pipeline Job erstellen

Ein neuer Job vom Typ **Pipeline** wurde erstellt.  
Die Konfiguration erfolgte über SCM → Git mit Link zum Repository und Pfad zum `Jenkinsfile`.

<img src="images/Bild21.png" width="600">
<img src="images/Bild22.png" width="600">

---

### 🚀 Pipeline Build erfolgreich

Der erste Build mit dem Jenkinsfile wurde gestartet. Jenkins führte automatisiert die Phasen **Build → Test → Analyse** aus.

<img src="images/Bild23.png" width="600">
<img src="images/Bild24.png" width="600">

---

### 🧪 Fehlerhafte Pipeline zur Verifikation

Anschliessend wurde ein **Fehler im Code eingebaut** (Test schlägt fehl). Der nächste Pipeline-Run reagierte korrekt:

- Pipeline wird rot (Fehler)
- Fehlerhafte Stufe klar markiert
- Testreport zeigt Ursache

<img src="images/Bild25.png" width="600">
<img src="images/Bild26.png" width="600">
<img src="images/Bild27.png" width="600">

---

### ☁️ Jenkins Cloud-Agent via Docker

Um Builds dynamisch in einem isolierten Container auszuführen, wurde ein Cloud-Agent über Docker eingerichtet.

#### Schritte:
- Docker-Plugin in Jenkins installiert
- Templates für Container-Umgebung definiert
- Agent wird bei Bedarf automatisch erzeugt und wieder entfernt

<img src="images/Bild28.png" width="600">
<img src="images/Bild29.png" width="600">
<img src="images/Bild30.png" width="600">
<img src="images/Bild31.png" width="600">

---

### 🧪 Pipeline mit Cloud-Agent testen

Build mit aktivierter Cloud-Agent-Konfiguration:

- Jenkins erzeugt Container on-demand
- Alle Stages laufen im Agent-Container
- Nach Build wird der Container entfernt

<img src="images/Bild32.png" width="600">
<img src="images/Bild33.png" width="600">
<img src="images/Bild34.png" width="600">
<img src="images/Bild35.png" width="600">
<img src="images/Bild36.png" width="600">

---

### 🧼 Cleanup und Logs

- Alte Builds analysiert
- Container gelöscht
- Logs exportiert und Build-Zustände dokumentiert

<img src="images/Bild37.png" width="600">
<img src="images/Bild39.png" width="600">
<img src="images/Bild40.png" width="600">

---

### 📊 Weitere Builds & Regressionstests

Die Pipeline wurde mehrfach ausgeführt mit:
- erfolgreichem Zustand (grün)
- absichtlich fehlgeschlagenen Tests (rot)
- Reanalyse im SonarQube

<img src="images/Bild41.png" width="600">
<img src="images/Bild42.png" width="600">
<img src="images/Bild43.png" width="600">
<img src="images/Bild44.png" width="600">
<img src="images/Bild45.png" width="600">
<img src="images/Bild46.png" width="600">

---

### 📄 Ergänzende Tests & Pipeline-Dokumentation

Zum Abschluss wurden nochmals gezielte Validierungen durchgeführt, um die Stabilität und Nachvollziehbarkeit der Continuous-Integration-Pipeline in Jenkins sicherzustellen. Die folgenden Screenshots dokumentieren die wichtigsten Aspekte:

---

- **🧾 Jenkins Build-Historie**  
  Darstellung der gesamten Build-Historie in Jenkins. Hier erkennt man die Statusanzeige für erfolgreiche und fehlerhafte Builds, inklusive Rückverfolgbarkeit.

  <img src="images/Bild47.png" width="600">

---

- **📊 Übersicht über Pipeline-Ausführung**  
  Zeigt den vollständigen Ablauf eines CI-Laufs mit Zeitstempeln. Alle Stages (z. B. Clone, Build, Test) sind sichtbar – ein Beleg für stabile Abläufe.

  <img src="images/Bild48.png" width="600">

---

- **⚙️ Build-Konfiguration im Detail**  
  In diesem Screenshot ist die Build-Konfiguration ersichtlich, etwa die genutzten Parameter und Projektdefinitionen innerhalb der Pipeline.

  <img src="images/Bild49.png" width="600">

---

- **📈 Visualisierung der Stage-Sequenz**  
  Jenkins zeigt den vollständigen Ablauf der CI-Phasen in grafischer Form: Start, Checkout, Test, SonarQube-Analyse, Success. Dies erleichtert die Diagnose und Nachvollziehbarkeit.

  <img src="images/Bild50.png" width="600">

---

- **🖥️ Konsolenausgabe bei erfolgreichem Lauf**  
  Konsolenlog mit erfolgreicher Ausführung aller Pipeline-Stufen. Hier wird deutlich: Die Jenkinsfile wurde erkannt, die Stages wurden ausgeführt und die Tests sind grün.

  <img src="images/Bild51.png" width="600">

---

- **🔍 SonarQube-Analyse-Ergebnis**  
  Die Codeanalyse mit SonarQube wurde erneut durchgeführt. Ergebnis: Keine neuen Sicherheitslücken, gute Coverage und keine Regressionsfehler.

  <img src="images/Bild52.png" width="600">

---

- **📋 Zusammenfassung im Jenkins-Dashboard**  
  Abschliessende Übersicht zur Build-Ausführung im Jenkins-Dashboard. Alle Metriken und Ergebnisse sind klar nachvollziehbar.

  <img src="images/Bild53.png" width="600">

---

✅ **Fazit dieser Validierung:**  
Die CI-Pipeline ist stabil, dokumentiert und in der Lage, alle Builds automatisiert und zuverlässig auszuführen – inkl. Feedback durch SonarQube und Jenkins Logs.

---

## 🧠 Erkenntnisse & Fazit

### 📌 Fachlich gelernt

- Die Einführung von **Jenkins** in Kombination mit **GitHub**, **Gradle**, **JUnit**, **JaCoCo**, **Docker** und **SonarQube** zeigt, wie stark Continuous Integration automatisiert werden kann.
- Ich habe gelernt, **Jenkins-Jobs** sowohl manuell über die UI als auch deklarativ über ein **Jenkinsfile** zu steuern.
- Das Zusammenspiel von **Build, Test, Analyse und Visualisierung** bietet eine solide Basis für jede DevOps-Pipeline.
- Die Nutzung von **Docker-Containern** für Jenkins selbst sowie für Agenten führte zu einer konsistenten und isolierten Umgebung.
- Durch bewusste Fehler konnte ich verifizieren, dass meine CI-Konfiguration zuverlässig auf Probleme reagiert.

---

### 💬 Persönliche Reflexion

Diese Aufgabe hat mir eindrücklich gezeigt, wie vielschichtig und mächtig moderne Build- und Qualitätssicherungsprozesse sind. Was anfangs komplex erschien, ergab durch die Praxis und den Aufbau in Etappen ein stimmiges Gesamtbild.

Besonders die **Transparenz durch automatische Testreports, SonarQube-Auswertungen und Pipelines** überzeugte mich. Ich sehe nun deutlich, wie wichtig CI im Kontext agiler und iterativer Entwicklung ist.

> 🧠 *„Jeder Commit ist ein Versprechen – CI stellt sicher, dass wir es einhalten.“*

---

📅 *Frühjahrssemester 2025 – ZHAW School of Management and Law*
