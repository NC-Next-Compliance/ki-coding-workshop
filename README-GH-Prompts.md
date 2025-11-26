# GitHub Copilot Professional Prompt Templates

> **Stand: November 2025**  
> Professionelle, wiederverwendbare Prompt-Templates für Enterprise-Workflows

---

## Inhaltsverzeichnis

1. [Quick Reference](#quick-reference)
2. [Prompt 1: Component Documentation Generator](#prompt-1-component-documentation-generator)
3. [Prompt 2: Feature Implementation für Komponente](#prompt-2-feature-implementation-für-komponente)
4. [Prompt 3: Codebase Refactoring Planner](#prompt-3-codebase-refactoring-planner)
5. [Supporting Files](#supporting-files)
6. [Installation & Setup](#installation--setup)

---

## Quick Reference

### Header-Felder für .prompt.md

```yaml
---
name: 'slash-command'           # /slash-command im Chat
description: 'Kurzbeschreibung' # Wird im UI angezeigt
argument-hint: 'Hinweistext'    # Placeholder im Input
agent: 'agent'                  # ask | edit | agent | plan
model: 'Claude Sonnet 4'        # LLM-Modell
tools: ['codebase', 'search']   # Verfügbare Tools
---
```

### Variablen

| Variable | Beschreibung |
|----------|-------------|
| `${input:name:placeholder}` | Benutzereingabe mit Hinweis |
| `${file}` | Aktuelle Datei |
| `${selection}` | Markierter Code |
| `${workspaceFolderBasename}` | Projektname |
| `#tool:toolname` | Tool im Body referenzieren |
| `#file:path/to/file` | Datei referenzieren |

### Verfügbare Tools

```
codebase, search, fetch, runCommands, runTasks, 
findTestFiles, usages, changes, testFailure, 
githubRepo, think, todos
```

---

## Prompt 1: Component Documentation Generator

**Datei:** `.github/prompts/document-component.prompt.md`

```markdown
---
name: 'doc-component'
description: 'Generiert vollständige, kompakte Dokumentation für eine Komponente'
argument-hint: 'Pfad zur Komponente, z.B. src/components/auth'
agent: 'agent'
model: 'Claude Sonnet 4'
tools:
  - 'codebase'
  - 'search'
  - 'usages'
  - 'findTestFiles'
  - 'fetch'
---

# Component Documentation Generator

## Ziel
Erstelle eine vollständige, kompakte Dokumentation für die angegebene Komponente.

## Input

**Komponenten-Pfad:** ${input:componentPath:z.B. src/components/auth oder src/services/payment}

## Analyse-Phase

### 1. Komponenten-Struktur erfassen
Analysiere alle Dateien im Verzeichnis `${componentPath}`:
- Hauptdateien (index.ts/js, main exports)
- Typdefinitionen
- Utilities und Helpers
- Tests

### 2. Dependencies ermitteln
Nutze #tool:usages um zu identifizieren:
- Interne Dependencies (andere Projekt-Module)
- Externe Dependencies (npm packages)
- Wo diese Komponente verwendet wird

### 3. Test-Coverage prüfen
Nutze #tool:findTestFiles für:
- Existierende Tests
- Coverage-Bereiche
- Fehlende Test-Szenarien

## Output-Format

Erstelle: `${componentPath}/README.md`

```markdown
# [Komponenten-Name]

> [Einzeiler: Was macht diese Komponente?]

## Overview

| Aspekt | Details |
|--------|---------|
| **Pfad** | `${componentPath}` |
| **Typ** | [Service/Component/Hook/Utility] |
| **Status** | [Stable/Beta/Deprecated] |
| **Maintainer** | [Team/Person] |

## Architektur

```
${componentPath}/
├── index.ts          # Public API
├── types.ts          # TypeScript Definitions
├── [name].ts         # Hauptlogik
├── utils/            # Interne Utilities
└── __tests__/        # Unit Tests
```

## Public API

### Exports

| Export | Typ | Beschreibung |
|--------|-----|--------------|
| `[Name]` | [Function/Class/Type] | [Kurzbeschreibung] |

### Verwendung

```typescript
import { [Export] } from '[package-path]';

// Basis-Beispiel
const result = [Export]([params]);

// Erweitertes Beispiel mit Optionen
const result = [Export]({
  option1: value1,
  option2: value2,
});
```

## Props / Parameters

### [Hauptfunktion/Komponente]

| Parameter | Typ | Required | Default | Beschreibung |
|-----------|-----|----------|---------|--------------|
| `param1` | `string` | ✅ | - | [Beschreibung] |
| `param2` | `number` | ❌ | `10` | [Beschreibung] |

## Dependencies

### Intern
- `[ModulName]` - [Zweck]

### Extern
- `[package]@[version]` - [Zweck]

## Verwendung im Projekt

```
Verwendet von:
├── src/pages/[page].tsx
├── src/components/[component]/
└── src/hooks/use[Hook].ts
```

## Error Handling

| Error | Code | Ursache | Lösung |
|-------|------|---------|--------|
| `[ErrorName]` | `[CODE]` | [Wann tritt auf] | [Wie beheben] |

## Testing

```bash
# Unit Tests ausführen
npm test -- --testPathPattern=${componentPath}

# Mit Coverage
npm test -- --coverage --collectCoverageFrom='${componentPath}/**/*.ts'
```

### Test-Szenarien

- ✅ [Getestetes Szenario 1]
- ✅ [Getestetes Szenario 2]
- ⚠️ [Fehlender Test - TODO]

## Configuration

```typescript
// Optionale Konfiguration
interface [Name]Config {
  [option]: [type]; // [Beschreibung]
}
```

## Changelog

| Version | Datum | Änderung |
|---------|-------|----------|
| 1.0.0 | [Date] | Initial Release |

---
*Generiert am: [ISO-Datum] | Generator: doc-component*
```

## Qualitätskriterien

Vor Abschluss prüfen:
- [ ] Alle public exports dokumentiert
- [ ] Mindestens 2 Code-Beispiele
- [ ] Dependencies vollständig
- [ ] Error-Handling beschrieben
- [ ] Test-Kommandos funktionieren
```

---

## Prompt 2: Feature Implementation für Komponente

**Datei:** `.github/prompts/implement-feature.prompt.md`

```markdown
---
name: 'implement-feature'
description: 'Implementiert ein neues Feature für eine bestehende Komponente'
argument-hint: 'Komponente und Feature-Beschreibung'
agent: 'agent'
model: 'Claude Sonnet 4'
tools:
  - 'codebase'
  - 'search'
  - 'usages'
  - 'findTestFiles'
  - 'runCommands'
  - 'testFailure'
  - 'changes'
  - 'think'
---

# Feature Implementation Agent

## Ziel
Implementiere ein neues Feature für eine bestehende Komponente unter Beibehaltung aller Coding-Standards und mit vollständiger Test-Coverage.

## Input

**Ziel-Komponente:** ${input:targetComponent:z.B. src/services/auth oder src/components/DataTable}

**Feature-Beschreibung:** ${input:featureDescription:z.B. "OAuth2 PKCE Flow hinzufügen" oder "Spalten-Sortierung implementieren"}

**Akzeptanzkriterien (optional):** ${input:acceptanceCriteria:z.B. "1. Unterstützt Google/GitHub OAuth 2. Tokens werden sicher gespeichert"}

## Phase 1: Analyse & Planung

### 1.1 Komponenten-Kontext verstehen
Nutze #tool:think für tiefgehende Analyse:

```
Analysiere ${targetComponent}:
1. Aktuelle Architektur und Patterns
2. Existierende Public API
3. Interne State-Management-Logik
4. Error-Handling-Patterns
5. Bestehende Tests und deren Struktur
```

### 1.2 Impact Assessment

Nutze #tool:usages um zu identifizieren:
- Alle Stellen, die diese Komponente verwenden
- Potenzielle Breaking Changes
- Abhängige Module die angepasst werden müssen

### 1.3 Implementierungsplan erstellen

```markdown
## Feature: ${featureDescription}

### Betroffene Dateien
| Datei | Änderungstyp | Beschreibung |
|-------|--------------|--------------|
| [path] | NEW/MODIFY | [Was wird gemacht] |

### Neue Dependencies
- [ ] [package] - [Begründung]

### API-Änderungen
- [ ] Neue Exports: [Liste]
- [ ] Geänderte Signaturen: [Liste]
- [ ] Breaking Changes: [Ja/Nein + Details]

### Migrations-Bedarf
[Falls Breaking Changes: Migrationspfad beschreiben]
```

## Phase 2: Implementation

### 2.1 Types/Interfaces zuerst

```typescript
// Erstelle/erweitere Types in ${targetComponent}/types.ts

/**
 * [Feature-Name] Configuration
 * @since [version]
 */
export interface [FeatureName]Options {
  // Neue Options dokumentieren
}

/**
 * [Feature-Name] Result
 */
export type [FeatureName]Result = {
  // Return-Type dokumentieren
};
```

### 2.2 Implementierung

Befolge diese Prinzipien:
- **Single Responsibility**: Jede Funktion macht genau eine Sache
- **Backward Compatibility**: Bestehende API nicht brechen (wenn möglich)
- **Error Handling**: Alle Edge Cases abfangen
- **Logging**: Wichtige Operationen loggen

```typescript
/**
 * [Feature-Beschreibung]
 * 
 * @param options - Configuration options
 * @returns [Was wird zurückgegeben]
 * @throws {[ErrorType]} [Wann wird geworfen]
 * 
 * @example
 * ```typescript
 * const result = await [featureName]({
 *   option1: 'value',
 * });
 * ```
 * 
 * @since [version]
 */
export async function [featureName](
  options: [FeatureName]Options
): Promise<[FeatureName]Result> {
  // Implementation
}
```

### 2.3 Export registrieren

```typescript
// ${targetComponent}/index.ts
export { [featureName] } from './[feature-file]';
export type { [FeatureName]Options, [FeatureName]Result } from './types';
```

## Phase 3: Testing

### 3.1 Test-Datei erstellen/erweitern

```typescript
// ${targetComponent}/__tests__/[feature].test.ts

describe('[FeatureName]', () => {
  describe('Success Cases', () => {
    it('should [erwartetes Verhalten] when [Bedingung]', async () => {
      // Arrange
      const options: [FeatureName]Options = { /* ... */ };
      
      // Act
      const result = await [featureName](options);
      
      // Assert
      expect(result).toMatchObject({ /* ... */ });
    });

    it('should handle [edge case]', async () => {
      // ...
    });
  });

  describe('Error Cases', () => {
    it('should throw [ErrorType] when [Bedingung]', async () => {
      // Arrange
      const invalidOptions = { /* ... */ };
      
      // Act & Assert
      await expect([featureName](invalidOptions))
        .rejects.toThrow([ErrorType]);
    });
  });

  describe('Integration', () => {
    it('should work with existing [related feature]', async () => {
      // ...
    });
  });
});
```

### 3.2 Tests ausführen

```bash
npm test -- --testPathPattern=${targetComponent} --coverage
```

Nutze #tool:testFailure bei Fehlern zur automatischen Korrektur.

## Phase 4: Validierung

### 4.1 Code-Qualität

```bash
npm run typecheck
npm run lint -- --fix
```

### 4.2 Dokumentation aktualisieren

Aktualisiere `${targetComponent}/README.md`:
- Neuen Export dokumentieren
- Usage-Beispiel hinzufügen
- Changelog-Eintrag

### 4.3 Checkliste

- [ ] Feature implementiert gemäß Beschreibung
- [ ] Alle Akzeptanzkriterien erfüllt
- [ ] Types vollständig definiert
- [ ] JSDoc für alle public APIs
- [ ] Unit Tests mit >80% Coverage
- [ ] Keine TypeScript-Fehler
- [ ] Keine Lint-Fehler
- [ ] README aktualisiert
- [ ] Keine Breaking Changes (oder dokumentiert)

## Output-Zusammenfassung

```markdown
# Feature Implementation Report

## Feature: ${featureDescription}
## Komponente: ${targetComponent}

### Änderungen
| Datei | Status | Zeilen |
|-------|--------|--------|
| [path] | ✅ Created/Modified | +X / -Y |

### Neue API
- `[exportName]([params]): [returnType]`

### Test Coverage
- Statements: X%
- Branches: X%
- Functions: X%

### Nächste Schritte
1. Code Review
2. [Weitere Schritte]
```
```

---

## Prompt 3: Codebase Refactoring Planner

**Datei:** `.github/prompts/refactoring-planner.prompt.md`

```markdown
---
name: 'refactor-plan'
description: 'Erstellt detaillierten Refactoring-Plan mit Roadmap, Arbeitspaketen und Aufwandsschätzung'
argument-hint: 'Refactoring-Ziel beschreiben'
agent: 'plan'
model: 'Claude Sonnet 4'
tools:
  - 'codebase'
  - 'search'
  - 'usages'
  - 'findTestFiles'
  - 'fetch'
  - 'githubRepo'
  - 'think'
---

# Codebase Refactoring Planner

> **Hinweis:** Dieser Agent erstellt ausschließlich Planungsdokumente. 
> Keine Code-Änderungen werden vorgenommen.

## Ziel
Erstelle einen vollständigen, umsetzbaren Refactoring-Plan basierend auf den Benutzer-Vorgaben.

## Input

**Refactoring-Ziel:** ${input:refactoringGoal:z.B. "Migration von JavaScript zu TypeScript" oder "Monolith zu Microservices"}

**Scope:** ${input:scope:z.B. "Gesamte Codebase" oder "src/services/" oder "nur Backend"}

**Constraints:** ${input:constraints:z.B. "Kein Breaking Change der Public API" oder "Budget: 3 Monate, 2 Entwickler"}

**Priorität:** ${input:priority:z.B. "Performance > Maintainability" oder "Schnelle Lieferung > Perfektion"}

## Phase 1: Ist-Analyse

### 1.1 Codebase-Inventur

Nutze #tool:think und #tool:codebase für tiefgehende Analyse:

```markdown
## Ist-Zustand: ${workspaceFolderBasename}

### Technologie-Stack
| Kategorie | Aktuell | Version |
|-----------|---------|---------|
| Runtime | | |
| Framework | | |
| Database | | |
| Testing | | |

### Codebase-Metriken
| Metrik | Wert |
|--------|------|
| Gesamte Dateien | |
| Lines of Code | |
| Test-Coverage | |
| Technische Schulden | |

### Architektur-Übersicht
```
[Verzeichnisstruktur mit Beschreibung]
```
```

### 1.2 Problem-Identifikation

Analysiere und dokumentiere:
- Code Smells und Anti-Patterns
- Performance-Bottlenecks
- Sicherheitslücken
- Wartbarkeitsprobleme
- Technische Schulden

### 1.3 Abhängigkeits-Analyse

Nutze #tool:usages um kritische Pfade zu identifizieren:
- Hochgradig gekoppelte Module
- Circular Dependencies
- Verwaiste Code-Bereiche
- Kritische Pfade für Refactoring-Reihenfolge

## Phase 2: Soll-Konzept

### 2.1 Ziel-Architektur definieren

```markdown
## Ziel-Zustand

### Architektur-Vision
[Beschreibung der Ziel-Architektur]

### Strukturelle Änderungen
```
Vorher:                    Nachher:
src/                       src/
├── components/            ├── modules/
├── services/      →       │   ├── auth/
├── utils/                 │   ├── payment/
└── ...                    │   └── ...
                           ├── shared/
                           └── ...
```

### Technologie-Änderungen
| Aktuell | Ziel | Begründung |
|---------|------|------------|
| [Tech] | [Tech] | [Warum] |
```

### 2.2 Risiko-Assessment

```markdown
## Risiken & Mitigationen

| ID | Risiko | Wahrsch. | Impact | Mitigation |
|----|--------|----------|--------|------------|
| R1 | [Risiko] | Hoch/Mittel/Niedrig | Hoch/Mittel/Niedrig | [Maßnahme] |
| R2 | | | | |

### Kritische Erfolgsfaktoren
1. [Faktor 1]
2. [Faktor 2]

### Abbruchkriterien
- [Wann wird das Projekt gestoppt?]
```

## Phase 3: Roadmap

### 3.1 High-Level Roadmap

```markdown
## Refactoring Roadmap

```
Timeline:     M1        M2        M3        M4        M5        M6
              │         │         │         │         │         │
Phase 1:      ██████████│         │         │         │         │
Foundation    │         │         │         │         │         │
              │         │         │         │         │         │
Phase 2:      │    █████████████████        │         │         │
Core Refact.  │         │         │         │         │         │
              │         │         │         │         │         │
Phase 3:      │         │         │    ██████████████████       │
Integration   │         │         │         │         │         │
              │         │         │         │         │         │
Phase 4:      │         │         │         │         █████████████
Stabilization │         │         │         │         │         │
```

### Phasen-Übersicht

| Phase | Zeitraum | Fokus | Deliverables |
|-------|----------|-------|--------------|
| 1 - Foundation | M1-M2 | [Fokus] | [Deliverables] |
| 2 - Core | M2-M4 | [Fokus] | [Deliverables] |
| 3 - Integration | M4-M5 | [Fokus] | [Deliverables] |
| 4 - Stabilization | M5-M6 | [Fokus] | [Deliverables] |
```

## Phase 4: Arbeitspakete

### 4.1 Work Breakdown Structure

```markdown
## Arbeitspakete (Work Packages)

### WP-1: [Name]
| Attribut | Wert |
|----------|------|
| **ID** | WP-1 |
| **Phase** | 1 - Foundation |
| **Priorität** | P0 (Blocker) / P1 (High) / P2 (Medium) |
| **Aufwand** | [X] Personentage |
| **Abhängigkeiten** | - (keine) |
| **Risiko** | Niedrig |

**Beschreibung:**
[Was wird in diesem Arbeitspaket erledigt?]

**Scope:**
- Betroffene Verzeichnisse: `[paths]`
- Betroffene Dateien: ~[X] Dateien
- Geschätzte LOC-Änderungen: +[X] / -[Y]

**Akzeptanzkriterien:**
- [ ] [Kriterium 1]
- [ ] [Kriterium 2]
- [ ] Alle Tests grün
- [ ] Code Review abgeschlossen

**Technische Details:**
```
[Konkrete technische Schritte]
```

---

### WP-2: [Name]
| Attribut | Wert |
|----------|------|
| **ID** | WP-2 |
| **Phase** | 1 - Foundation |
| **Priorität** | P1 (High) |
| **Aufwand** | [X] Personentage |
| **Abhängigkeiten** | WP-1 |
| **Risiko** | Mittel |

[... weitere Details ...]

---

[Weitere Arbeitspakete nach gleichem Schema]
```

### 4.2 Abhängigkeits-Graph

```markdown
## Abhängigkeiten

```
WP-1 (Foundation)
  │
  ├──► WP-2 (Types Migration)
  │      │
  │      ├──► WP-4 (Service Layer)
  │      │      │
  │      │      └──► WP-6 (Integration Tests)
  │      │
  │      └──► WP-5 (Component Layer)
  │             │
  │             └──► WP-6 (Integration Tests)
  │
  └──► WP-3 (Build Setup)
         │
         └──► WP-7 (CI/CD Update)
```

### Kritischer Pfad
`WP-1 → WP-2 → WP-4 → WP-6` (geschätzt: [X] Tage)
```

## Phase 5: Milestones

```markdown
## Milestones & Gates

### M1: Foundation Complete ⬜
**Zieldatum:** [Datum]
**Status:** Not Started

**Gate-Kriterien:**
- [ ] Build-System modernisiert
- [ ] Basis-Types definiert
- [ ] CI/CD Pipeline aktualisiert
- [ ] Team-Onboarding abgeschlossen

**Arbeitspakete:** WP-1, WP-3

**Review-Meeting:** [Datum]

---

### M2: Core Migration Complete ⬜
**Zieldatum:** [Datum]
**Status:** Not Started

**Gate-Kriterien:**
- [ ] [X]% der Services migriert
- [ ] Test-Coverage ≥ 80%
- [ ] Keine kritischen Bugs
- [ ] Performance-Baseline erreicht

**Arbeitspakete:** WP-2, WP-4, WP-5

**Review-Meeting:** [Datum]

---

### M3: Integration Complete ⬜
**Zieldatum:** [Datum]
**Status:** Not Started

**Gate-Kriterien:**
- [ ] Alle Module integriert
- [ ] E2E Tests grün
- [ ] Staging-Deployment erfolgreich
- [ ] Performance-Tests bestanden

**Arbeitspakete:** WP-6, WP-7

**Review-Meeting:** [Datum]

---

### M4: Production Ready ⬜
**Zieldatum:** [Datum]
**Status:** Not Started

**Gate-Kriterien:**
- [ ] Security Audit bestanden
- [ ] Load Tests bestanden
- [ ] Rollback-Plan getestet
- [ ] Dokumentation vollständig
- [ ] Stakeholder Sign-off

**Go-Live Decision Meeting:** [Datum]
```

## Phase 6: Aufwandsschätzung

```markdown
## Aufwandsschätzung

### Zusammenfassung

| Kategorie | Optimistisch | Realistisch | Pessimistisch |
|-----------|--------------|-------------|---------------|
| **Gesamtaufwand** | [X] PT | [Y] PT | [Z] PT |
| **Dauer** | [X] Wochen | [Y] Wochen | [Z] Wochen |
| **Team-Größe** | [X] FTE | [Y] FTE | [Z] FTE |

*PT = Personentage, FTE = Full-Time Equivalent*

### Aufwand pro Phase

| Phase | Aufwand (PT) | Dauer | Team |
|-------|-------------|-------|------|
| 1 - Foundation | [X] | [X] Wo. | [X] Dev |
| 2 - Core | [X] | [X] Wo. | [X] Dev |
| 3 - Integration | [X] | [X] Wo. | [X] Dev |
| 4 - Stabilization | [X] | [X] Wo. | [X] Dev |
| **Gesamt** | **[X]** | **[X] Wo.** | - |

### Aufwand pro Arbeitspaket

| WP | Name | Aufwand | Konfidenz |
|----|------|---------|-----------|
| WP-1 | [Name] | [X] PT | 🟢 Hoch |
| WP-2 | [Name] | [X] PT | 🟡 Mittel |
| WP-3 | [Name] | [X] PT | 🟢 Hoch |
| WP-4 | [Name] | [X] PT | 🔴 Niedrig |
| ... | | | |

### Ressourcen-Anforderungen

| Rolle | Anzahl | Zeitraum | Skills |
|-------|--------|----------|--------|
| Senior Developer | [X] | Gesamt | [Skills] |
| Mid-Level Developer | [X] | Phase 2-3 | [Skills] |
| DevOps Engineer | [X] | Phase 1, 4 | [Skills] |
| QA Engineer | [X] | Phase 3-4 | [Skills] |

### Kosten-Schätzung (optional)

| Position | Berechnung | Kosten |
|----------|------------|--------|
| Personal | [X] PT × [Y] €/Tag | [Z] € |
| Tooling/Lizenzen | | [X] € |
| Cloud/Infrastruktur | | [X] € |
| Buffer (20%) | | [X] € |
| **Gesamt** | | **[X] €** |

### Annahmen & Einschränkungen

**Annahmen:**
- Team ist zu 80% verfügbar (20% BAU)
- Keine größeren Scope-Änderungen
- Dependencies werden rechtzeitig geliefert

**Nicht enthalten:**
- Feature-Entwicklung (nur Refactoring)
- Hardware-Kosten
- Training/Schulung
```

## Output-Dokument

Erstelle: `docs/refactoring/[goal]-refactoring-plan.md`

```markdown
# Refactoring Plan: ${refactoringGoal}

**Erstellt:** [ISO-Datum]
**Version:** 1.0
**Status:** Draft / Review / Approved
**Owner:** [Name]

---

## Executive Summary

[2-3 Absätze: Was, Warum, Wie, Wann, Kosten]

---

[Alle Phasen 1-6 hier einfügen]

---

## Anhänge

### A. Glossar
| Begriff | Definition |
|---------|------------|

### B. Referenzen
- [Link zu relevanten Dokumenten]

### C. Änderungshistorie
| Version | Datum | Autor | Änderung |
|---------|-------|-------|----------|
| 1.0 | [Datum] | [Name] | Initial Draft |

---

*Dieses Dokument wurde mit dem Refactoring Planner generiert.*
*Review und Freigabe erforderlich vor Umsetzung.*
```

## Qualitätskriterien

Vor Abschluss validieren:
- [ ] Alle Input-Variablen berücksichtigt
- [ ] Ist-Analyse vollständig
- [ ] Alle Arbeitspakete haben Aufwandsschätzung
- [ ] Abhängigkeiten sind konsistent
- [ ] Milestones haben klare Gate-Kriterien
- [ ] Risiken sind identifiziert und mitigiert
- [ ] Aufwandsschätzung enthält Puffer
- [ ] Dokument ist selbsterklärend
```

---

## Supporting Files

### Instructions für konsistente Dokumentation

**Datei:** `.github/instructions/documentation.instructions.md`

```markdown
---
description: 'Standards für generierte Dokumentation'
applyTo: '**/README.md, **/docs/**/*.md'
---

# Documentation Standards

## Sprache
- Technische Dokumentation: Englisch
- Interne Kommentare: Deutsch erlaubt
- Code-Beispiele: Immer lauffähig

## Struktur
- Überschriften: Title Case
- Tabellen für strukturierte Daten
- Code-Blöcke mit Syntax-Highlighting
- Relative Links innerhalb des Repos

## Pflichtfelder für Komponenten-Docs
- Overview mit Einzeiler
- Installation/Setup
- Mindestens 1 Code-Beispiel
- API-Dokumentation für public exports
- Test-Kommandos

## Formatierung
- Maximale Zeilenlänge: 100 Zeichen
- Listen: Bullet Points für ungeordnet, Nummern für Schritte
- Emojis: Sparsam, nur für Status-Indikatoren
```

---

### Custom Agent: Documentation Reviewer

**Datei:** `.github/agents/doc-reviewer.agent.md`

```markdown
---
name: 'DocReviewer'
description: 'Prüft generierte Dokumentation auf Vollständigkeit und Qualität'
argument-hint: 'Pfad zur Dokumentation'
model: 'Claude Sonnet 4'
tools:
  - 'codebase'
  - 'search'
handoffs:
  - label: '✏️ Dokumentation korrigieren'
    agent: 'agent'
    prompt: 'Korrigiere die identifizierten Probleme in der Dokumentation.'
    send: false
---

# Documentation Reviewer

Du prüfst Dokumentation auf:

## Checkliste

### Vollständigkeit
- [ ] Alle public exports dokumentiert
- [ ] Mindestens 2 Code-Beispiele
- [ ] Installation/Setup beschrieben
- [ ] Dependencies aufgelistet

### Qualität
- [ ] Code-Beispiele sind lauffähig
- [ ] Keine Placeholder-Texte übrig
- [ ] Links funktionieren
- [ ] Konsistente Formatierung

### Aktualität
- [ ] API-Signaturen stimmen mit Code überein
- [ ] Keine veralteten Informationen
- [ ] Version/Changelog aktuell

## Output

```markdown
# Documentation Review: [Pfad]

## Score: [X]/10

### ✅ Erfüllt
- [Was ist gut]

### ⚠️ Verbesserungsbedarf
- [Was fehlt oder verbessert werden sollte]

### ❌ Kritisch
- [Was dringend gefixt werden muss]
```
```

---

## Installation & Setup

### 1. Dateien kopieren

```bash
# Prompts
mkdir -p .github/prompts
cp document-component.prompt.md .github/prompts/
cp implement-feature.prompt.md .github/prompts/
cp refactoring-planner.prompt.md .github/prompts/

# Instructions
mkdir -p .github/instructions
cp documentation.instructions.md .github/instructions/

# Agents
mkdir -p .github/agents
cp doc-reviewer.agent.md .github/agents/
```

### 2. VS Code Settings

```json
{
  "chat.promptFiles": true,
  "chat.agent.enabled": true
}
```

### 3. Verwendung

```
# Im Copilot Chat:
/doc-component src/components/auth
/implement-feature src/services/payment "Stripe Subscriptions hinzufügen"
/refactor-plan "Migration von REST zu GraphQL"
```

---

## Verzeichnisstruktur

```
.github/
├── prompts/
│   ├── document-component.prompt.md    # Komponenten-Dokumentation
│   ├── implement-feature.prompt.md     # Feature-Implementation
│   └── refactoring-planner.prompt.md   # Refactoring-Planung
├── instructions/
│   └── documentation.instructions.md   # Dokumentations-Standards
├── agents/
│   └── doc-reviewer.agent.md           # Dokumentations-Review
└── copilot-instructions.md             # Projekt-weite Standards
```

---

*Erstellt: November 2025*
