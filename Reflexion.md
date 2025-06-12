# Reflexion zur erweiterten CI-Pipeline

## Beschreibe den Zweck der zwei Stages (Builder und Runner) in deinem multi-stage Dockerfile für die React App und warum dieser Ansatz für CI/CD vorteilhaft ist.

Das Dockerfile hat zwei Stufen. Die erste Stufe heißt "Builder". Dort wird die React-App mit Node.js gebaut. In dieser Phase werden alle Abhängigkeiten installiert und der Build-Befehl ausgeführt (`npm ci` und `npm run build`).

Die zweite Stufe heißt "Runner". Diese verwendet ein schlankes nginx-Image. Hier wird nur das fertige Build-Ergebnis (also die gebauten Dateien aus dem Ordner `dist/`) bereitgestellt.

Der Vorteil ist, dass die finale Anwendung in einem kleineren, sicheren Image läuft. Alle Entwicklungs-Abhängigkeiten bleiben im Builder-Teil. Das ist besser für den Einsatz in Produktion und reduziert die Angriffsfläche und die Größe des Images.

---

## Wie hast du deine Docker Hub Zugangsdaten sicher in deiner CI-Plattform gespeichert? Warum ist das sicherer, als sie direkt in der Pipeline-Konfigurationsdatei im Git-Repository zu hinterlegen?

Ich habe die Zugangsdaten in GitHub als sogenannte "Secrets" gespeichert. Dort habe ich zwei Einträge angelegt:

- `DOCKERHUB_USERNAME` für meinen Docker Hub Benutzernamen
- `DOCKERHUB_TOKEN` für den Zugangstoken von Docker Hub

Diese Werte werden nicht im Code angezeigt. Sie sind nur in der GitHub Actions Umgebung während der Ausführung verfügbar. Das ist sicherer als sie direkt in der `docker.yml` Datei anzugeben, weil sie nicht öffentlich einsehbar sind – auch nicht im Repository oder in Logs.

---

## Beschreibe die Abfolge der vier Hauptschritte, die deine erweiterte CI-Pipeline nun ausführt (Code holen, Image bauen, Login, Image pushen) und was jeder Schritt bewirkt.

1. **Code holen**  
   GitHub Actions holt den aktuellen Stand des Repositories mit `actions/checkout`.

2. **Login**  
   Die Pipeline meldet sich bei Docker Hub an, indem sie den Benutzernamen und das Token aus den Secrets verwendet.

3. **Image bauen**  
   Es wird ein Multi-Stage Docker Image im Verzeichnis `frontend/` gebaut. Das Image enthält das gebaute React-Projekt.

4. **Image pushen**  
   Das Image wird mit einem eindeutigen Tag zu Docker Hub hochgeladen, damit es später genutzt oder deployed werden kann.

---

## Welche Informationen hast du als Image-Tag verwendet, um das gebaute Docker Image eindeutig zu identifizieren? Warum ist ein eindeutiges Tag wichtig (besonders im Hinblick auf spätere Deployments)?

Ich habe den Git-Commit-Hash als Tag verwendet: `${{ github.sha }}`.  
Dadurch hat jedes Image einen eigenen, eindeutigen Namen.

Das ist wichtig, weil man so genau weiß, zu welchem Stand des Quellcodes das Image gehört. Man kann damit auch bestimmte Versionen gezielt wiederverwenden oder zurückrollen, falls es ein Problem gibt.

---

## Stell dir vor, deine Pipeline würde fehlschlagen, weil das Docker Image nicht gepusht werden konnte. Welche Schritte würdest du zur Fehlersuche unternehmen?

Ich würde zuerst die Logs in GitHub Actions ansehen, um zu sehen, in welchem Schritt es genau passiert ist.

Dann würde ich prüfen, ob die Secrets korrekt gesetzt sind:  
- Ist der Docker Hub Benutzername richtig geschrieben?  
- Ist der Token gültig oder abgelaufen?  

Wenn nötig, würde ich in Docker Hub einen neuen Token erstellen und das Secret in GitHub neu anlegen.

Außerdem würde ich den Image-Namen und das Tag überprüfen – es muss genau zum Docker Hub Repository passen.
