# Demo Agents - Multi-Agent System

Ein demonstratives Multi-Agent-System mit Strands Framework, bestehend aus spezialisierten Agenten die über A2A (Agent-to-Agent) Kommunikation orchestriert werden.

## 📁 Projektstruktur

```
demo-agents/
├── client.py              # Client-Agent für Benutzerinteraktion
├── supervisior_agent.py   # Supervisor-Agent (orchestriert Spezialisten)
├── search_agent.py        # Web-Such-Agent
├── markdown_agent.py      # Markdown-Datei-Management-Agent
├── requirements.txt       # Python-Abhängigkeiten
├── files/                 # Verzeichnis für Markdown-Dateien
└── __pycache__/          # Python Cache-Dateien
```

## 🏗️ Architektur

Das System besteht aus vier Hauptkomponenten:

1. **Search Agent** (Port 5001) - Spezialisiert auf Web-Suche
2. **Markdown Agent** (Port 5002) - Spezialisiert auf Markdown-Dateien
3. **Supervisor Agent** (Port 5000) - Orchestriert die Spezialisten
4. **Client** - Benutzerinterface für das gesamte System

## 🔧 Voraussetzungen

- Python 3.10+
- AWS Bedrock Zugang (für Claude Sonnet 4)
- Internetverbindung für Web-Suche

## 📦 Installation

1. Repository klonen oder Dateien herunterladen
2. Virtual Environment erstellen (empfohlen):
   ```bash
   python -m venv venv
   source venv/bin/activate  # Auf macOS/Linux
   ```
3. Abhängigkeiten installieren:
   ```bash
   pip install -r requirements.txt
   ```

## 🚀 Startanleitung

**Wichtig:** Die Agenten müssen in der richtigen Reihenfolge gestartet werden!

### Schritt 1: Spezialisierte Agenten starten

Öffne **zwei separate Terminals** und starte:

**Terminal 1 - Search Agent:**
```bash
python search_agent.py
```
Läuft auf: `http://localhost:5001`

**Terminal 2 - Markdown Agent:**
```bash
python markdown_agent.py
```
Läuft auf: `http://localhost:5002`

### Schritt 2: Supervisor Agent starten

**Terminal 3 - Supervisor Agent:**
```bash
python supervisior_agent.py
```
Läuft auf: `http://localhost:5000`

*Der Supervisor erkennt automatisch die verfügbaren Spezialisten-Agenten.*

### Schritt 3: Client starten

**Terminal 4 - Client:**
```bash
python client.py
```

## 💡 Verwendung

Nach dem Start des Clients können Sie komplexe Aufgaben stellen, die mehrere Agenten koordiniert nutzen:

### Beispiele:

1. **Web-Suche mit Markdown-Dokumentation:**
   ```
   Search for the latest news on AI hardware and create a summary in a file named ai_news.md
   ```

2. **Markdown-Dateien verwalten:**
   ```
   List all markdown files and show me the content of ai_news.md
   ```

3. **Kombinierte Aufgaben:**
   ```
   Search for information about Python frameworks and create a comparison document
   ```

## 🔍 Agent-Details

### Search Agent
- **Port:** 5001
- **Funktion:** Web-Suche mit DuckDuckGo
- **Tools:**
  - `websearch()` - Suche im Web mit Keywords

### Markdown Agent
- **Port:** 5002
- **Funktion:** Markdown-Datei-Management im `files/` Verzeichnis
- **Tools:**
  - `read_markdown_file()` - Dateien lesen
  - `create_markdown_file()` - Neue Dateien erstellen
  - `edit_markdown_file()` - Bestehende Dateien bearbeiten
  - `list_markdown_files()` - Dateien auflisten

### Supervisor Agent
- **Port:** 5000
- **Funktion:** Orchestrierung und Koordination der Spezialisten
- **Features:**
  - Automatische Tool-Entdeckung
  - Intelligente Aufgaben-Delegation
  - Multi-Step Workflow-Execution

### Client
- **Funktion:** Benutzerinterface für das gesamte System
- **Features:**
  - Interaktive Konsolen-Schnittstelle
  - Natürliche Sprachverarbeitung
  - Automatische Agent-Orchestrierung

## 🛠️ Technische Details

- **Framework:** Strands mit A2A (Agent-to-Agent) Kommunikation
- **KI-Modell:** AWS Bedrock Claude Sonnet 4
- **Kommunikation:** HTTP REST API zwischen Agenten
- **Datenformat:** JSON für Agent-Kommunikation

## 🚨 Troubleshooting

### Häufige Probleme:

1. **"Keine Tools entdeckt"**
   - Sicherstellen, dass alle vorherigen Agenten laufen
   - Ports 5001 und 5002 müssen verfügbar sein

2. **"Verbindung fehlgeschlagen"**
   - Reihenfolge beim Starten beachten
   - Firewall-Einstellungen prüfen

3. **AWS Bedrock Fehler**
   - AWS-Credentials konfigurieren
   - Berechtigungen für Bedrock Claude prüfen

### Logs und Debugging:

Jeder Agent gibt detaillierte Logs aus. Bei Problemen:
- Console-Ausgaben in jedem Terminal prüfen
- Logging-Level in den .py-Dateien anpassen

## 🔄 Beenden

Um das System ordnungsgemäß zu beenden:
1. Client beenden: `exit` eingeben oder Ctrl+C
2. Supervisor beenden: Ctrl+C im Terminal 3
3. Agenten beenden: Ctrl+C in Terminals 1 und 2

## 📝 Entwicklung

Das System ist modular aufgebaut. Neue spezialisierte Agenten können einfach hinzugefügt werden:

1. Neuen Agent mit `A2AServer` erstellen
2. Agent in `SPECIALIST_AGENT_URLS` im Supervisor eintragen
3. Supervisor neu starten

## 🤝 Beiträge

Dies ist ein Demo-Projekt. Bei Fragen oder Verbesserungsvorschlägen wenden Sie sich an das Entwicklerteam.

---

**Hinweis:** Dieses Projekt dient zu Demonstrationszwecken und zeigt die Möglichkeiten von Multi-Agent-Systemen mit dem Strands Framework.