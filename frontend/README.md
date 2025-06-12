# React CI/CD Pipeline mit Docker und GitHub Actions

Dieses Projekt demonstriert eine vollständige CI/CD-Pipeline für eine Vite-basierte React-Anwendung. Die App wird automatisch in einem Docker Image verpackt und in eine Docker Hub Registry gepusht – mithilfe von **GitHub Actions**, einem **Multi-Stage Dockerfile** und **sicheren Secrets**.

---

## Projektstruktur

react-ci-cd-pipeline/

├── frontend/             # Vite React App

│   └── Dockerfile        # Multi-Stage Dockerfile 
(Node + nginx)

├── .github/workflows/

│   └── docker.yml       # CI-Workflow zum Bauen & Pushen

└── README.md


---

## CI/CD Ablauf (GitHub Actions)

Die CI-Pipeline wird bei jedem Push in den Haupt-Branch automatisch ausgeführt. Der Ablauf:

1.  **Checkout** des Codes
2.  **Login** bei Docker Hub über gespeicherte Secrets
3.  **Bau** eines Multi-Stage Docker Images
4.  **Push** des Images in das Docker Hub Repository

Das Image wird eindeutig mit dem **Git-Commit-Hash** getaggt.

---

## Verwendete Secrets

Die folgenden Secrets wurden im GitHub-Repository unter _Settings → Secrets → Actions_ hinterlegt:

| Name               | Beschreibung                      |
| :----------------- | :-------------------------------- |
| `DOCKERHUB_USERNAME` | Docker Hub Benutzername           |
| `DOCKERHUB_TOKEN`    | Docker Hub Access Token (Sicher!) |

---

## Dockerfile (Multi-Stage)

Das Dockerfile besteht aus zwei Stages:

-   **Builder** (Node 24 Alpine): Führt `npm ci` und `npm run build` aus
-   **Runner** (nginx:alpine): Dient als schlanker Webserver für die `dist/`-Dateien