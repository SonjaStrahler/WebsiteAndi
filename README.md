# Website Nord-Copter — Workflow & Dokumentation

## 📍 Projektübersicht
- **Live-Website:** https://nord-copter.de *(sobald Domain verbunden)*
- **Repository:** https://github.com/SonjaStrahler/WebsiteAndi
- **Server:** Proxmox Container *(wird eingerichtet)*

## 📂 Wo liegt das Projekt?

**Lokaler Arbeitsordner:**
```
~/Documents/Website_NordCopter
```
Arbeite IMMER nur in diesem Ordner!

**Quick-Check:**
```bash
pwd
# Sollte zeigen: /Users/Sonja/Documents/Website_NordCopter

git remote -v
# Sollte zeigen: https://github.com/SonjaStrahler/WebsiteAndi.git
```

---

## 📁 Ordnerstruktur
```
Website_NordCopter/
├── index.html              ← Die Hauptseite (Coming Soon)
├── images/
│   ├── wind.png            ← Drohne über Windpark (Andi1)
│   ├── agrar.png           ← Agrardrohne über Weinberg (Andi2)
│   └── pilot.png           ← Pilot am Flughafen (Andi3)
├── css/
│   └── style.css           ← Falls CSS ausgelagert wird (optional)
├── .gitignore
└── README.md               ← Diese Datei
```

---

## 🌳 Branch-Struktur

```
Startbahn  →  Flugzeug  →  Flughafen
 (Arbeiten)     (LIVE)       (Backup)
```

| Branch | Bedeutung | Merkhilfe |
|--------|-----------|-----------|
| **Startbahn** | Hier wird gebaut & getestet | 🛫 Vorbereitung vor dem Abheben |
| **Flugzeug** | Das ist LIVE — was hier ist, ist online! | ✈️ Abgehoben = sichtbar |
| **Flughafen** | Backup — tägliche Sicherung | 🛬 Sicher gelandet |

**Branch wechseln:**
```bash
git checkout Startbahn      # Zum Arbeiten
git checkout Flugzeug        # Live-Branch
git checkout Flughafen       # Backup
git branch                   # Aktuellen Branch anzeigen (* = aktiv)
```

---

## 🚀 Täglicher Workflow (Copy & Paste Ready!)

### 1. Morgens — Arbeit beginnen
```bash
cd ~/Documents/Website_NordCopter
git checkout Startbahn
git pull origin Startbahn
git status
```

### 2. Während der Arbeit — regelmäßig speichern
Nach jedem Arbeitsblock (alle 30–60 Min):
```bash
git status
git add .
git commit -m "Beschreibung was du gemacht hast"
git push origin Startbahn
```

### 3. Live schalten — SCHRITT FÜR SCHRITT!

⚠️ **WICHTIG: Kein All-in-One Befehl! Immer Schritt für Schritt!**

**Schritt 1:** Zu Flugzeug wechseln
```bash
git checkout Flugzeug
```

**Schritt 2:** Aktualisieren
```bash
git pull origin Flugzeug
```

**Schritt 3:** Startbahn mergen
```bash
git merge Startbahn
```

Falls Konflikt:
```bash
git checkout --theirs [dateiname]
git add [dateiname]
git commit -m "Merge Konflikt gelöst"
```

**Schritt 4:** Zu GitHub pushen
```bash
git push origin Flugzeug
```

**Schritt 5:** Server aktualisieren *(wenn Proxmox-Container eingerichtet)*
```bash
# Auf Proxmox Host:
pct enter 102

# Im Container:
cd /var/www/nordcopter
git pull origin Flugzeug
exit
```

**Schritt 6:** Zurück zum Startbahn
```bash
git checkout Startbahn
```

### 4. Abends — Backup in Flughafen
```bash
git checkout Flugzeug
git pull origin Flugzeug
git checkout Flughafen
git pull origin Flughafen
git merge Flugzeug
git push origin Flughafen
git checkout Startbahn
```

---

## ⚡ Quick-Merge (wenn du sicher bist, dass alles sauber ist)

```bash
cd ~/Documents/Website_NordCopter || exit 1
set -e

# 1) Startbahn pushen
git checkout Startbahn
git pull --ff-only origin Startbahn
git add -A
git commit -m "Update $(date +%Y-%m-%d_%H:%M)" || true
git push origin Startbahn

# 2) In Flugzeug mergen
git checkout Flugzeug
git pull --ff-only origin Flugzeug
git merge --no-ff Startbahn -m "Merge Startbahn into Flugzeug"
git push origin Flugzeug

# 3) In Flughafen mergen
git checkout Flughafen
git pull --ff-only origin Flughafen
git merge --no-ff Flugzeug -m "Merge Flugzeug into Flughafen"
git push origin Flughafen

# 4) Zurück
git checkout Startbahn
git status
```

---

## 🆘 Häufige Probleme & Lösungen

### Problem 1: Merge Conflict
**Symptom:** `CONFLICT (content): Merge conflict in [dateiname]`

**Lösung — deine Version nehmen (meistens richtig):**
```bash
git checkout --theirs [dateiname]
git add [dateiname]
git commit -m "Merge Konflikt gelöst"
```

### Problem 2: "Your local changes would be overwritten"
```bash
git add .
git commit -m "Speichere aktuelle Änderungen"
```
Dann nochmal den Merge/Pull versuchen.

### Problem 3: Website zeigt alte Inhalte trotz Push
```bash
# Server aktualisieren:
pct enter [CONTAINER-ID]
cd /var/www/nordcopter
git pull origin Flugzeug
```
Browser: **CMD + Shift + R** (Hard Refresh)

### Problem 4: Ich bin im falschen Branch!
```bash
git branch          # Der aktive hat ein *
git checkout Startbahn
```

---

## 📦 Bilder hinzufügen

### Schritt 1: Bilder in Ordner legen
Im Finder: `Documents → Website_NordCopter → images`

### Schritt 2: Zu Git hinzufügen
```bash
git status
git add images/
git commit -m "Füge Bilder für [Bereich] hinzu"
git push origin Startbahn
```

### Bildgrößen Empfehlung
- **Hero-Bild (breit):** 1920 × 800 px
- **Service-Bilder:** 800 × 600 px
- **Format:** WebP oder komprimiertes JPG, max. 500 KB

---

## 🔍 Git-Befehle Cheat Sheet

```bash
# Status & Info
pwd                          # Wo bin ich?
git status                   # Was hat sich geändert?
git branch                   # Welche Branches? (* = aktuell)
git log --oneline -5         # Letzte 5 Commits

# Branch wechseln
git checkout [branch-name]

# Änderungen speichern
git add .                    # Alle Änderungen
git commit -m "Nachricht"    # Commit erstellen
git push origin [branch]     # Zu GitHub hochladen

# Änderungen holen
git pull origin [branch]     # Neueste Version holen

# Mergen
git merge [branch-name]

# Rückgängig
git checkout -- [datei]      # Datei zurücksetzen
git reset --hard HEAD        # Alles zurücksetzen
```

---

## ✅ Checkliste vor dem Live-Schalten

- [ ] Alle Änderungen committed und gepusht?
- [ ] `git status` zeigt "nothing to commit"?
- [ ] Lokal getestet (Live Server in VS Code)?
- [ ] Keine offensichtlichen Fehler?
- [ ] Bereit für Live? → Schritt für Schritt zu Flugzeug mergen!

---

## 🎯 Wichtige Regeln

1. **IMMER Schritt-für-Schritt mergen** — Keine All-in-One-Befehle!
2. **Nur in Startbahn arbeiten** — Keine extra Feature-Branches!
3. **Nach jedem Arbeitsblock pushen** — Nichts geht verloren!
4. **Flugzeug = Live** — Was dort ist, ist online!
5. **Flughafen = Backup** — Täglich mergen!

---

## 📞 Projekt-Farben (Flight Deck Branding)
```
Accent Orange:  #e8622a
Dark Slate:     #0d1117
Card BG:        #1c2028
Text Light:     #e6edf3
Text Muted:     #8b949e
Blue Accent:    #3b82f6
```
