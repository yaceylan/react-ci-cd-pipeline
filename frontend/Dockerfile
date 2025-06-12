# -------- Build Stage --------
FROM node:24-alpine AS builder

# Arbeitsverzeichnis im Container
WORKDIR /app

# Nur package.json und lockfile zuerst kopieren für besseren Cache
COPY package*.json ./

# Installiere nur notwendige Abhängigkeiten
RUN npm ci

# Restliche Dateien kopieren
COPY . .

# Optionales Build-Argument (z. B. für API-URL)
ARG VITE_API_URL=http://localhost:3000
ENV VITE_API_URL=$VITE_API_URL

# Produktionsbuild erzeugen
RUN npm run build

# -------- Run Stage --------
FROM nginx:alpine AS runner

# Kopiere den Build in das Standard-NGINX-Verzeichnis
COPY --from=builder /app/dist /usr/share/nginx/html

# Port, auf dem die App im Container verfügbar ist
EXPOSE 80

# Startkommando für den Container
CMD ["nginx", "-g", "daemon off;"]
