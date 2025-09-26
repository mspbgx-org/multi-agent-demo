# Multi-Agent Demo System

Ein fortschrittliches Multi-Agent-System, das verschiedene spezialisierte KI-Agenten orchestriert, um komplexe Aufgaben zu lösen. Das System basiert auf der Strands-Framework und nutzt AWS Bedrock für die KI-Modelle.

## 🏗️ Architektur

Das System besteht aus mehreren Komponenten:

- **Client Agent**: Lokaler Agent für Benutzerinteraktion
- **Supervisor Agent**: Orchestriert und delegiert Aufgaben an Spezialisten
- **Search Agent**: Spezialisiert auf Web-Suche und Informationsbeschaffung
- **Markdown Agent**: Verarbeitet und erstellt Markdown-Dokumente

## 📁 Projektstruktur

```
├── client.py                  # Lokaler Client-Agent
├── agents/
│   ├── supervisior/
│   │   ├── supervisior_agent.py    # Supervisor-Agent
│   │   ├── Dockerfile
│   │   ├── Makefile
│   │   └── template.yaml           # AWS SAM Template
│   ├── search/
│   │   ├── search_agent.py         # Web-Search-Agent
│   │   ├── Dockerfile
│   │   ├── Makefile
│   │   └── template.yaml
│   └── markdown/
│       ├── markdown_agent.py       # Markdown-Verarbeitungsagent
│       ├── Dockerfile
│       ├── Makefile
│       └── template.yaml
├── files/                     # Lokale Dateien und Dokumente
└── .vscode/                   # VS Code Konfiguration
```

## 🚀 Deployment

Das System ist für AWS App Runner optimiert und nutzt:

- **AWS Bedrock**: KI-Modelle (Claude Sonnet)
- **AWS App Runner**: Container-Hosting
- **AWS ECR**: Container Registry
- **AWS IAM**: Zugriffsberechtigungen

### Voraussetzungen

- AWS CLI konfiguriert
- Docker installiert
- Python 3.11+
- uv (Python Package Manager)

### Agent-Deployment

Jeder Agent kann einzeln deployed werden:

```bash
# Beispiel für Search Agent
cd agents/search
make create-ecr
make push
make deploy-to-aws
```

## 🔧 Konfiguration

### Agent URLs

Die aktuelle Konfiguration verwendet folgende URLs:

```python
# Supervisor Agent
SUPERVISOR_URL = "https://tpe2aw8ekp.eu-central-1.awsapprunner.com/"

# Specialist Agents
SPECIALIST_AGENT_URLS = [
    "https://yz99cpygph.eu-central-1.awsapprunner.com",  # Search Agent
]
```

### AWS Bedrock Modell

```python
bedrock_model = BedrockModel(
    model_id="eu.anthropic.claude-sonnet-4-20250514-v1:0"
)
```

## 💻 Lokale Entwicklung

### Client starten

```bash
python client.py
```

### Agent lokal testen

```bash
cd agents/search
uv install
uv run python search_agent.py
```

## 🛠️ Verfügbare Tools

### Search Agent
- **websearch**: Web-Suche mit DuckDuckGo
  - Parameter: `keywords`, `region`, `max_results`
  - Unterstützt verschiedene Regionen (us-en, uk-en, etc.)

### Supervisor Agent
- Orchestriert andere Agenten
- Delegiert Aufgaben basierend auf Kontext
- Kombiniert Ergebnisse mehrerer Spezialisten

## 📋 Beispiel-Verwendung

```bash
> maximilian sparenberg arbeitet bei materna. suche seine position raus.
```

Das System wird:
1. Die Anfrage analysieren
2. Den Search Agent für Web-Recherche nutzen
3. Relevante Informationen aus den lokalen Dateien extrahieren
4. Eine umfassende Antwort zusammenstellen

## 📊 Verfügbare Dokumente

Das System hat Zugriff auf verschiedene Profile in [`files/`](files/):

- [`maximilian_sparenberg_materna_profil.md`](files/maximilian_sparenberg_materna_profil.md) - Senior DevOps Engineer Profil
- [`michael_hagedorn_materna_ceo_profil.md`](files/michael_hagedorn_materna_ceo_profil.md) - CEO Profil  
- [`Philip_Zweihoff_Recherche.md`](files/Philip_Zweihoff_Recherche.md) - CONET VP Recherche
- [`Recherche_Carsten_Paasch_Materna.md`](files/Recherche_Carsten_Paasch_Materna.md) - Solutions Manager AI Profil
- Weitere Profile und Recherchen

## 🔐 Sicherheit

### AWS IAM Berechtigungen

Jeder Agent hat spezifische IAM-Rollen:

```yaml
Policies:
  - PolicyName: BedrockInvokeModel
    PolicyDocument:
      Statement:
        - Effect: Allow
          Action:
            - bedrock:InvokeModel
            - bedrock:InvokeModelWithResponseStream
          Resource:
            - arn:aws:bedrock:*::foundation-model/amazon.nova-micro-v1:0
            - arn:aws:bedrock:*::foundation-model/anthropic.claude-sonnet-4-20250514-v1:0
```

### Netzwerk

- App Runner Services mit konfigurierbaren VPC-Konnektoren
- HTTPS-Kommunikation zwischen Agenten
- Rate Limiting für externe APIs

## 🔍 Monitoring und Debugging

### Logging

```python
logging.getLogger("strands").setLevel(logging.INFO)
```

### Health Checks

Jeder Agent stellt Health-Check-Endpoints zur Verfügung über das A2A (Agent-to-Agent) Protokoll.

## ⚡ Performance

### Auto Scaling Konfiguration

```yaml
AutoScaling:
  MaxConcurrency: 100
  MinSize: 1
  MaxSize: 1
```

### Resource Limits

```yaml
InstanceConfiguration:
  Cpu: '1024'
  Memory: '2048'
```

## 🤖 Agent-to-Agent Kommunikation

Das System nutzt das Strands A2A-Protokoll für:

- **Service Discovery**: Automatische Erkennung verfügbarer Agenten
- **Tool Proxy**: Dynamische Tool-Generierung für Remote-Agents
- **Load Balancing**: Verteilung von Anfragen
- **Error Handling**: Robuste Fehlerbehandlung

## 📝 Entwicklung neuer Agenten

### Agent-Template

```python
from strands import Agent, tool
from strands.models import BedrockModel
from strands.multiagent.a2a import A2AServer

@tool
def my_tool(parameter: str) -> str:
    """Tool description for the agent."""
    return f"Result: {parameter}"

agent = Agent(
    name="My Agent",
    system_prompt="System prompt for the agent",
    tools=[my_tool],
    model=BedrockModel(model_id="eu.anthropic.claude-sonnet-4-20250514-v1:0")
)

server = A2AServer(agent=agent, http_url="https://your-url.com")
server.serve(host="0.0.0.0", port=443)
```

## 🐳 Docker Deployment

### Build Image

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY agent.py requirements.txt ./
RUN python -m pip install -r requirements.txt
CMD ["python", "agent.py"]
```

### Deploy mit Makefile

```bash
make build
make push
make deploy-to-aws
```

## 🔄 CI/CD

Die Agenten unterstützen automatisches Deployment über:

- **Auto-Deployment**: Aktiviert in App Runner
- **ECR Integration**: Automatischer Pull der neuesten Images
- **SAM Templates**: Infrastructure as Code

## 🎯 Anwendungsfälle

- **Recherche und Analyse**: Kombinierte Web- und Dokumentensuche
- **Content-Erstellung**: Automatische Markdown-Generierung
- **Multi-Source-Integration**: Verknüpfung verschiedener Datenquellen
- **Workflow-Automatisierung**: Mehrstufige Aufgabenverarbeitung

## 🛟 Troubleshooting

### Häufige Probleme

1. **Agent nicht erreichbar**: Überprüfen Sie die URLs in der Konfiguration
2. **AWS Credentials**: Stellen Sie sicher, dass `.envrc` korrekt konfiguriert ist
3. **Rate Limits**: Bei DuckDuckGo-Suche Pausen einlegen
4. **Memory Issues**: App Runner Instanz-Größe anpassen

### Logs überprüfen

```bash
# CloudWatch Logs für App Runner Services
aws logs describe-log-groups --log-group-name-prefix="/aws/apprunner/"
```

## 📄 Lizenz

Dieses Projekt ist für interne Nutzung bei Materna bestimmt.

## 👥 Entwicklerteam

- **Maximilian Sparenberg** - Senior DevOps Engineer bei Materna
- Weitere Informationen in [`files/maximilian_sparenberg_materna_profil.md`](files/maximilian_sparenberg_materna_profil.md)

---

**Hinweis**: Stellen Sie sicher, dass alle AWS-Credentials und API-Keys sicher verwaltet werden und nicht in Version Control eingecheckt werden.