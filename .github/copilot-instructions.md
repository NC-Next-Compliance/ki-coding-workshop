# ASP.NET/SignalR Tool Management System - Copilot Instructions

## System Overview
- **Framework**: ASP.NET Core 8.0 mit SignalR
- **Architecture**: Domain-Driven Design mit REST APIs und Protobuf-Schnittstellen
- **Testing**: MSTest Framework
- **UI Components**: Colibri Library (interne Komponenten)

## Core Principles
1. **Maintain Consistency**: Alle Erweiterungen müssen dem bestehenden Architektur-Muster folgen
2. **Frontend**: Verwende IMMER Colibri-Komponenten über MCP "Colibri-dev" extension für das Frontend
3. **Test Coverage**: Jede neue Funktionalität benötigt entsprechende MSTest-Implementierungen
4. **Documentation**: Architekturänderungen erfordern Mermaid-Diagramme und Markdown-Dokumentation


## Domain Context
### Existing Structure
- **Tools**: Werkzeugverwaltung für Schleifmaschinen
- **TemplateTools**: Vorlagen für verschiedene Werkzeugtypen
- **Communication**: REST API + SignalR für Echtzeit-Updates + Protobuf für effiziente Serialisierung

## Coding Standards

### Backend (ASP.NET/SignalR)
- Verwende async/await für alle I/O-Operationen
- SignalR Hubs folgen dem Pattern: `{Entity}Hub`
- REST Endpoints: `/api/v1/{entity}/{action}`
- Protobuf-Messages in separatem `/Contracts` Ordner
- Domain Events für SignalR-Broadcasts nutzen

### Frontend (Colibri Components)
- Konsultiere IMMER Colibri-dev MCP für verfügbare Komponenten
- Nutze bestehende Tool-Management UI als Vorlage
- Formulare müssen Colibri-Validierung verwenden
- Dark Mode kompatibel (siehe Screenshot)

### Testing
- Unit Tests: Eine Test-Klasse pro Service
- Integration Tests: Verwende WebApplicationFactory
- SignalR Tests: Nutze TestHub-Pattern
- Minimum Coverage: 80% für neue Features

## Analysis Mode Instructions
Bei Code-Analyse IMMER:
1. Identifiziere bestehende Patterns in `/Tools` und `/TemplateTools`
2. Prüfe REST-Endpunkt-Konventionen
3. Analysiere SignalR Hub-Implementierungen
4. Dokumentiere Abhängigkeiten zwischen Komponenten 
5. Erstelle Mermaid-Diagramme für Architektur-Übersicht
6. Nutze die context7 mcp extension für aktuelle SDK-Informationen und Beispiele