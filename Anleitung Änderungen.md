📚 KOMPLETTE ANLEITUNG: Dify Management & Anpassungen
🏗️ Was ist was?
yaml
PRODUCTION (Live System):
├── Server: Hetzner (162.55.188.206)
├── Domain: https://ai.eic-insurance.de
├── Ordner: /opt/dify/docker
├── Läuft: 24/7 mit echten Daten
└── Nutzer: Echte Mitarbeiter/Kunden

DEVELOPMENT (Test System):
├── Computer: Dein Mac
├── Domain: http://localhost:3000
├── Ordner: ~/Desktop/dify-custom
├── Läuft: Nur wenn du testest
└── Nutzer: Nur du zum Testen
🎨 DESIGN ÄNDERUNGEN MACHEN
Schritt 1: GitHub Fork erstellen (EINMALIG)
bash
# 1. Gehe zu: https://github.com/langgenius/dify
# 2. Klick oben rechts auf "Fork"
# 3. Wähle deinen GitHub Account
# 4. Dein Fork ist dann: https://github.com/DEIN-USERNAME/dify
Schritt 2: Auf Mac einrichten (EINMALIG)
Terminal auf Mac öffnen:

bash
# Projekt Ordner erstellen
cd ~/Desktop
git clone https://github.com/DEIN-USERNAME/dify.git dify-custom
cd dify-custom
Schritt 3: Test-Umgebung auf Mac starten
Terminal auf Mac:

bash
cd ~/Desktop/dify-custom
cd docker
cp .env.example .env

# Docker starten (Test-Version)
docker-compose up -d

# Öffne Browser: http://localhost:3000
# Das ist deine TEST Umgebung!
🖼️ LOGO ÄNDERN
Auf dem Mac (Development):
bash
# Terminal auf Mac
cd ~/Desktop/dify-custom

# Logo Dateien finden
ls web/public/logo/
# Hier sind die Logos:
# - logo-site.png (Header Logo)
# - logo-embedded-chat-avatar.png (Chat Avatar)

# Ersetze mit deinen Logos (gleiche Dateinamen!)
cp ~/Desktop/mein-logo.png web/public/logo/logo-site.png
Lokal testen:
bash
# Terminal auf Mac
cd ~/Desktop/dify-custom/docker
docker-compose restart web

# Browser öffnen: http://localhost:3000
# Siehst du dein neues Logo? Gut!
Zu GitHub pushen:
bash
# Terminal auf Mac
cd ~/Desktop/dify-custom
git add .
git commit -m "Neues EIC Logo"
git push origin main
Auf Production deployen:
bash
# Terminal auf Mac - SSH zum Server
ssh root@162.55.188.206

# Auf Server
cd /opt/dify-custom  # Falls nicht existiert, siehe unten
git pull
cd web
docker build -t custom-dify-web .

# Production updaten
cd /opt/dify/docker
docker-compose stop web
docker tag custom-dify-web langgenius/dify-web:1.7.2
docker-compose up -d web
🎨 FARBEN ÄNDERN
Auf dem Mac:
bash
# Terminal auf Mac
cd ~/Desktop/dify-custom

# Hauptfarben-Datei öffnen
code web/app/styles/globals.css
# Oder mit nano:
nano web/app/styles/globals.css

# Suche nach:
# - primary (Hauptfarbe)
# - blue-600 (Standard Blau)
# Ersetze mit deinen Farben
Beispiel Farben ändern:
css
/* Vorher (Blau): */
--color-primary: #3B82F6;

/* Nachher (EIC Grün): */
--color-primary: #00A859;
Testen & Deployen:
Gleicher Prozess wie beim Logo (testen lokal, push zu GitHub, deploy auf Server)

📝 TEXTE/ÜBERSETZUNGEN ÄNDERN
bash
# Terminal auf Mac
cd ~/Desktop/dify-custom

# Deutsche Übersetzungen
ls web/i18n/de-DE/
# Hier sind alle deutschen Texte

# Beispiel: Login-Seite anpassen
nano web/i18n/de-DE/login.ts
🚀 DEPLOYMENT WORKFLOW (Zusammenfassung)
Immer dieser Ablauf:
Mac (Development): Code ändern
Mac (Test): Lokal testen mit Docker
GitHub: Push änderungen
Server (Production): Pull & Deploy
Komplettes Beispiel:
bash
# 1. AUF MAC - Änderung machen
cd ~/Desktop/dify-custom
# ... Dateien ändern ...

# 2. AUF MAC - Lokal testen
cd docker
docker-compose up -d
# Browser: http://localhost:3000

# 3. AUF MAC - Zu GitHub
git add .
git commit -m "Beschreibung der Änderung"
git push

# 4. AUF MAC - SSH zum Server
ssh root@162.55.188.206

# 5. AUF SERVER - Deployen
cd /opt/dify-custom
git pull
./deploy.sh  # Siehe Script unten
🔧 WARTUNG & MANAGEMENT
Production Server Commands:
bash
# SSH zum Server
ssh root@162.55.188.206

# Zum Dify Ordner
cd /opt/dify/docker

# Status checken
docker-compose ps

# Logs anschauen (letzte 100 Zeilen)
docker-compose logs -f --tail=100

# Einen Service neu starten
docker-compose restart web     # Frontend
docker-compose restart api     # Backend
docker-compose restart nginx   # Webserver

# Alles neu starten
docker-compose down
docker-compose up -d

# Speicherplatz checken
df -h

# Docker aufräumen
docker system prune -a
Backup erstellen:
bash
# Auf Server
cd /opt/dify/docker

# Datenbank Backup
docker-compose exec db pg_dump -U postgres dify > backup-$(date +%Y%m%d).sql

# Files Backup
tar -czf storage-backup-$(date +%Y%m%d).tar.gz storage/
📋 EINMALIGE SETUP SCRIPTE
Deploy Script erstellen (auf Server):
bash
# SSH zum Server
ssh root@162.55.188.206

# Custom Repository clonen (falls noch nicht gemacht)
cd /opt
git clone https://github.com/DEIN-USERNAME/dify.git dify-custom

# Deploy Script erstellen
cat > /opt/deploy-dify.sh << 'EOF'
#!/bin/bash
echo "🚀 Deploying Dify Updates..."

# Pull latest
cd /opt/dify-custom
git pull

# Build Web
echo "📦 Building Web..."
cd web
docker build -t custom-dify-web .

# Build API
echo "📦 Building API..."
cd ../api
docker build -t custom-dify-api .

# Update Production
echo "🔄 Updating Production..."
cd /opt/dify/docker
docker-compose stop web api
docker tag custom-dify-web langgenius/dify-web:1.7.2
docker tag custom-dify-api langgenius/dify-api:1.7.2
docker-compose up -d

echo "✅ Deployment Complete!"
echo "🌐 Check: https://ai.eic-insurance.de"
EOF

chmod +x /opt/deploy-dify.sh
⚠️ WICHTIGE HINWEISE:
NIEMALS direkt auf Production ändern!
IMMER erst lokal testen!
BACKUP vor großen Änderungen!
GitHub als Zwischenschritt für Versionskontrolle!
🆘 TROUBLESHOOTING:
Website down:
bash
ssh root@162.55.188.206
cd /opt/dify/docker
docker-compose ps          # Check Status
docker-compose up -d        # Neu starten
docker-compose logs nginx   # Logs checken
Änderungen nicht sichtbar:
bash
# Browser Cache leeren
# CTRL+SHIFT+R

# Docker Cache leeren
docker-compose down
docker-compose build --no-cache
docker-compose up -d
SSL Probleme:
bash
# SSL Zertifikat erneuern
certbot renew
docker-compose restart nginx
📞 SUPPORT KONTAKTE:
Server: Hetzner Support
Domain: Strato Support
Dify: https://github.com/langgenius/dify/issues
Diese Anleitung speichern und immer griffbereit haben!

Hast du noch Fragen zu einem bestimmten Teil?

