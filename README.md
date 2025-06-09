# Distributed Egg Hunt Application with AWS & Cryptography

## Kurzbeschreibung
Dieses Projekt ist eine interaktive Webanwendung (Flask), die eine digitale Ostereier-Suche simuliert. Es nutzt AWS-Dienste wie **DynamoDB** zur globalen Speicherung von Fundmeldungen und **SNS** für die Benachrichtigung bei Ei-Funden. Ein zentrales Merkmal ist die **Implementierung kryptographischer Signaturen** (RSA-PSS) zur Authentifizierung der Fundmeldungen, was die Integrität und Vertrauenswürdigkeit des Systems gewährleistet. Jedes Ei ist mit einem eindeutigen QR-Code verknüpft, der das Scannen und Registrieren des Fundes ermöglicht.

## Relevanz für DLR-Projekte (z.B. ATHMoS / Missionskontrollsysteme)
Obwohl dieses Projekt eine spielerische Anwendung ist, demonstriert es mehrere Kernkompetenzen, die für die Entwicklung robuster und skalierbarer Software im Kontext des DLR und insbesondere für Projekte wie "ATHMoS" oder allgemeine Missionskontrollsysteme von hoher Relevanz sind:

* **Cloud Computing & Verteilte Architekturen:** Erfahrung im Umgang mit AWS (DynamoDB, SNS) und dem Entwurf von Systemen, die über mehrere Dienste interagieren. Dies ist entscheidend für moderne, resiliente Raumfahrtbodensegmente.
* **Datenverarbeitung & Pipelines:** Das Senden von Fundereignissen via SNS und deren Persistierung in DynamoDB zeigt Grundkenntnisse im Aufbau von Datenpipelines und der Verarbeitung von Ereignissen.
* **Sicherheit & Authentizität:** Die Implementierung von RSA-PSS-Signaturen zur Authentifizierung von Nachrichten unterstreicht das Verständnis für kritische Sicherheitsaspekte in verteilten Systemen, die für den sicheren Betrieb von Raumfahrtmissionen unerlässlich sind.
* **Python-Entwicklung:** Die gesamte Anwendung ist in Python geschrieben und zeigt fundierte Kenntnisse in der Entwicklung komplexer Webanwendungen und Backend-Logik.
* **Potenzial für Containerisierung & Orchestrierung:** Die Anwendung ist modular aufgebaut und ideal für ein Deployment in Container-Umgebungen wie **Kubernetes**, was eine Anforderung für die DLR-Stelle ist und meine Fähigkeit unterstreicht, Applikationen für skalierbare Infrastrukturen zu entwickeln.

## Technologien
* **Backend:** Python 3.x, Flask
* **Datenbanken:** SQLite (lokal für Eier-Definitionen), AWS DynamoDB (für globale Funde)
* **Cloud Services:** AWS SNS (Simple Notification Service)
* **Kryptographie:** `cryptography.hazmat` (für RSA-PSS Signaturen)
* **Weitere Bibliotheken:** `qrcode`, `boto3`, `requests`, `uuid`, `werkzeug`
* **Deployment-Kontext:** Designed für Cloud-Umgebungen; Potenzial für Kubernetes-Deployment.

## Funktionen
* **Ei-Management (Admin-Bereich):** Hinzufügen, Anzeigen und Verwalten von Ostereiern mit Standortinformationen und Hinweisen.
* **QR-Code-Generierung:** Automatische Generierung eindeutiger QR-Codes für jedes Ei, die zu einer Fund-URL führen.
* **Fund-Registrierung:** Benutzer können Eier durch Scannen des QR-Codes als gefunden markieren.
* **Lokale & Globale Fundstatistik:** Verfolgung gefundener Eier pro Benutzer (SQLite) und globaler Funde (DynamoDB).
* **Sichere Benachrichtigung:** Senden von signierten Fundmeldungen an ein AWS SNS Topic, mit Validierung der Signatur am Empfänger-Ende (simuliert durch den `/sns_notification` Endpoint).
* **Public Key Distribution:** Exponierung des öffentlichen Schlüssels über den `.well-known`-Standardpfad für die Verifikation.

## Installation und Ausführung
Um das Projekt lokal auszuführen, folge diesen Schritten:

1.  **Voraussetzungen:**
    * Python 3.x
    * Pip (Python-Paketmanager)
    * AWS-Konto mit konfigurierten Anmeldeinformationen (AWS CLI oder Umgebungsvariablen) für `eu-central-1` mit Berechtigungen für DynamoDB (Tabelle `global-egg-finds`) und SNS (Topic `arn:aws:sns:eu-central-1:982999333742:egg_found_event`).
    * Erstelle eine DynamoDB-Tabelle namens `global-egg-finds` mit `userId` (String) und `eggId` (String) als Primärschlüssel.

2.  **Repository klonen:**
    ```bash
    git clone https://github.com/Raph11111/Oster-EIer-Tracking.git(https://github.com/Raph11111/Oster-EIer-Tracking.git)
    cd DeinRepositoryName
    ```

3.  **Virtuelle Umgebung erstellen und aktivieren:**
    ```bash
    python3 -m venv venv
    source venv/bin/activate  # macOS/Linux
    # oder
    # .\venv\Scripts\activate # Windows
    ```

4.  **Abhängigkeiten installieren:**
    ```bash
    pip install -r requirements.txt
    ```

5.  **Schlüsselgenerierung (für Signierung):**
    Erzeuge ein privates/öffentliches Schlüsselpaar. Speichere den privaten Schlüssel als `private_key.pem` im Projekt-Root und den öffentlichen Schlüssel als `egg_security.pub` im Verzeichnis `static/.well-known/`.
    ```bash
    # Beispielhaftes Kommando zur Generierung (Python-Code oder OpenSSL)
    # import os
    # from cryptography.hazmat.primitives.asymmetric import rsa
    # from cryptography.hazmat.primitives import serialization
    # private_key = rsa.generate_private_key(public_exponent=65537, key_size=2048)
    # public_key = private_key.public_key()
    # with open("private_key.pem", "wb") as f:
    #     f.write(private_key.private_bytes(encoding=serialization.Encoding.PEM, format=serialization.PrivateFormat.PKCS8, encryption_algorithm=serialization.NoEncryption()))
    # os.makedirs("static/.well-known", exist_ok=True)
    # with open("static/.well-known/egg_security.pub", "wb") as f:
    #     f.write(public_key.public_bytes(encoding=serialization.Encoding.PEM, format=serialization.PublicFormat.SubjectPublicKeyInfo))
    ```

6.  **Anwendung starten:**
    ```bash
    flask run --host=0.0.0.0 --port=3000
    ```
    (Stelle sicher, dass die Umgebungsvariable `FLASK_APP` auf deine Hauptdatei zeigt, z.B. `export FLASK_APP=app.py`)

7.  **Zugriff:**
    * Öffne deinen Browser und gehe zu `http://localhost:3000/`.
    * Der Admin-Bereich ist unter `http://localhost:3000/admin` verfügbar (hier kannst du Eier hinzufügen und ihre QR-Codes generieren).
    * Der SNS-Notification-Endpoint ist unter `http://localhost:3000/sns_notification` erreichbar (für AWS SNS Konfigurationen).

## Lizenz
Dieses Projekt ist unter der [MIT Lizenz](LICENSE) lizenziert.
