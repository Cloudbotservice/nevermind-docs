# 🎮 Nevermind FiveM Web-Panel

Ein modernes, leistungsstarkes Web-Panel zur Verwaltung, Steuerung und Überwachung deines FiveM (FXServer) Roleplay-Servers unter Linux.

---

## 🌟 Features

- **🚀 Server-Management:** Start, Stop, Neustart, Status-Monitor und geplante automatische Restarts.
- **🪄 Auto-Discovery:** Automatische Erkennung der FiveM-Startdatei (`run.sh` / Alpine) und des Roleplay-Datenordners (mit `resources` & `server.cfg`).
- **🛡️ panel_resmon Schutz:** Integrierter Schutz vor dem Überschreiben sensibler Server-Ressourcen bei Artifact-Updates.
- **📦 FiveM Artifacts Auto-Updater:** Artifacts direkt über die Weboberfläche mit einem Klick auf die neueste Version aktualisieren.
- **📄 server.cfg Live-Editor & Backups:** Direkte Bearbeitung im Browser inklusive automatischer Backups vor jeder Änderung.
- **🤖 Discord Bot & OAuth2 Integration:** Whitelist / Allowlist Prüfung, Join/Leave-Webhooks und automatisches Rollen-Mapping.
- **📊 Echtzeit-Statistiken:** Spielerübersicht, CPU-, RAM- und Speichernutzung des Hostsystems und des FXServers.

---

## 📋 Voraussetzungen

- **Betriebssystem:** Ubuntu (20.04 / 22.04 / 24.04) oder Debian (11 / 12 / 13)
- **Node.js:** Version 18 oder 20 LTS *(oft vorinstalliert, prüfe es mit `node -v`)*
- **Pakete:** `curl`, `tar`, `xz-utils`

---

## 🛠️ Schritt-für-Schritt Installationsanleitung

### 1. Vorbereitung & PM2 installieren

Prüfe im SSH-Terminal, ob Node.js installiert ist:
```bash
node -v
```

Installiere anschließend den PM2-Prozessmanager, damit das Panel 24/7 dauerhaft online bleibt:
```bash
sudo npm install -g pm2
```

*(Sollte Node.js auf einem ganz frischen Server noch fehlen, installiere es kurz mit: `curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash - && sudo apt install -y nodejs`)*

---

### 2. Panel-Dateien bereitstellen

Erstelle das Zielverzeichnis und setze die Zugriffsrechte:
```bash
sudo mkdir -p /var/www/html
sudo chown -R $USER:$USER /var/www/html
```

Kopiere alle Dateien des Panels in den Ordner `/var/www/html`:
- `server.js`
- `backend/`
- `components/`
- `pages/`
- `dist/`
- `css/`, `js/`, `assets/`, `img/`

---

### 3. Panel starten (24/7 Dauerbetrieb mit PM2 & direkter Port 80 Zugriff)

Führe einfach diesen 1-Zeiler aus. Er gibt Port 80 frei (stoppt evtl. vorinstalliertes Apache2) und startet das Panel als dauerhaften Hintergrunddienst:

```bash
systemctl stop apache2 2>/dev/null; systemctl disable apache2 2>/dev/null; cd /var/www/html && pm2 start server.js --name custom-panel && pm2 save
```

*(Optional: Damit PM2 nach einem kompletten Server-Neustart automatisch wieder hochfährt, einmal `pm2 startup` ausführen).*

**Nützliche PM2-Befehle:**
- `pm2 status` – Status des Panels prüfen
- `pm2 logs custom-panel` – Live-Konsole / Logs ansehen
- `pm2 restart custom-panel` – Panel neu starten
- `pm2 stop custom-panel` – Panel anhalten

---

### 4. Masteraccount direkt im Browser erstellen

Du musst **keine Dateien mehr per Hand bearbeiten!**

1. Öffne im Browser:
   ```text
   http://DEINE-SERVER-IP
   ```
2. Beim allerersten Start erscheint automatisch der **Ersteinrichtungs-Assistent**.
3. Gib deinen gewünschten **Master-Benutzernamen**, dein **Passwort** und optional deine **Discord ID** ein.
4. Klicke auf **„Masteraccount erstellen & einloggen“** – du erhältst sofort alle Rechte und wirst direkt ins Dashboard weitergeleitet!

---

### 5. Web-Panel aufrufen & FiveM-Pfade automatisch erkennen

1. Öffne im Browser einfach deine Server-IP (kein Port mehr nötig!):
   ```text
   http://DEINE-SERVER-IP
   ```
2. Melde dich mit deinem Discord-Account an.
3. Klicke links auf **⚙️ Einstellungen** und wähle den Tab **Server**.
4. Klicke oben rechts auf:
   > 🪄 **`Server & run.sh automatisch erkennen`**
5. Das Panel findet selbstständig:
   - Die FiveM-Startdatei (**`run.sh` / Alpine**)
   - Den Roleplay-Ordner (mit **`resources` und `server.cfg`**)
6. Klicke unten auf **💾 Server Settings speichern** – fertig!

---

## 🚫 txAdmin wird NICHT benötigt (und kann gelöscht werden)

Da dieses Web-Panel das komplette Server-Management, Live-Monitoring, CFG-Backups und die Steuerung übernimmt, wird das vorinstallierte **txAdmin (monitor)** überhaupt nicht gebraucht und sollte deaktiviert bzw. gelöscht werden.

### Warum und wie das Panel txAdmin automatisch blockiert:
1. **Kein automatischer txAdmin-Start:** Unser Panel startet den FiveM Server direkt über `run.sh +exec server.cfg`. Dadurch wird txAdmin gar nicht erst mitgestartet und blockiert keine unnötigen Ressourcen oder Ports.
2. **Automatischer Schutz bei Updates:** Wenn du über das Panel im Reiter *FiveM Artifacts* ein Update installierst, schließt das Panel `monitor` (txAdmin) automatisch vom Entpacken aus und löscht es selbstständig aus dem System.

### txAdmin manuell entfernen (Speicherplatz & Übersicht sparen):
Falls du txAdmin (`monitor`) aus deiner bestehenden FXServer-Installation manuell löschen möchtest, führe folgenden Befehl im Terminal aus:

```bash
# Löscht den txAdmin (monitor) Ordner aus dem FiveM Alpine System
rm -rf /home/FiveM/server/alpine/opt/cfx-server/citizen/system_resources/monitor
```
*(Hinweis: Passe den Pfad `/home/FiveM/server` an, falls dein Server in einem anderen Verzeichnis liegt, z. B. `.../alpine/opt/cfx-server/citizen/system_resources/monitor`)*

Auch der alte txAdmin Datenordner (`txData/`) kann bereinigt werden, wenn du deine Server-Dateien (z. B. `resources` und `server.cfg`) darin weiter als normalen Server-Ordner nutzt.

---

## 🔒 Optional: Eigene Domain & HTTPS (Nginx Reverse Proxy)

Wenn das Panel über eine Domain wie `https://panel.deinedomain.de` mit SSL erreichbar sein soll:

1. **Nginx & Certbot installieren:**
   ```bash
   sudo apt install -y nginx certbot python3-certbot-nginx
   ```

2. **Port im Panel anpassen (z. B. 3000 oder 8080), damit Port 80 für Nginx frei ist:**
   Trage in `/var/www/html/dist/config.json` z. B. `"port": 8080` ein.

3. **Nginx Konfiguration anlegen:**
   Erstelle die Datei `/etc/nginx/sites-available/panel`:
   ```nginx
   server {
       listen 80;
       server_name panel.deinedomain.de;

       location / {
           proxy_pass http://127.0.0.1:8080;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection "upgrade";
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       }
   }
   ```

4. **Aktivieren & SSL-Zertifikat holen:**
   ```bash
   sudo ln -s /etc/nginx/sites-available/panel /etc/nginx/sites-enabled/
   sudo nginx -t && sudo systemctl reload nginx
   sudo certbot --nginx -d panel.deinedomain.de
   ```
