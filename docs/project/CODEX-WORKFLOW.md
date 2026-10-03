# Codex Workflow

## Ziel

Codex dient als Implementierungs- und Review-Assistent. Fachliche IAM-Entscheidungen werden bewusst zuerst verstanden und dokumentiert.

## Empfohlener Ablauf

### 1. Task auswählen

Beispiel:

```text
Bearbeite ausschließlich IAM-001 aus TASKS.md.
Lies vorher AGENTS.md und die relevanten Architekturdateien.
Ändere keine noch nicht entschiedenen Architekturfragen.
```

### 2. Branch erstellen

Beispiel:

```bash
git checkout -b docs/personas
```

### 3. Änderung umsetzen

Codex soll:

- kleine Änderungen machen,
- vorhandene Begriffe wiederverwenden,
- Doku und Code konsistent halten,
- bei neuen Architekturentscheidungen stoppen und ADR-Bedarf markieren.

### 4. Review

Vor Commit prüfen:

```bash
git status
git diff
```

Zusätzlich fachlich prüfen:

- Ist die IAM-Aussage korrekt?
- Ist die Security-Wirkung verstanden?
- Kann ich die Entscheidung im Interview erklären?
- Ist das Beispiel rein synthetisch?

### 5. Commit

Beispiel:

```bash
git add docs/
git commit -m "docs: define enterprise identity personas"
```

### 6. Pull Request

Auch bei Solo-Projekten ist ein PR-Workflow sinnvoll, wenn das Repository später öffentlich gezeigt wird.

Der PR sollte beantworten:

- Warum wurde die Änderung gemacht?
- Welche Security-Auswirkungen gibt es?
- Welche Alternative wurde verworfen?
- Wie wurde validiert?

## Gute Codex-Aufträge

### Gut

```text
Bearbeite IAM-002. Definiere auf Basis der bestehenden Personas ein Gruppenmodell.
Ändere nur die dafür notwendigen Dokumentationsdateien.
Wenn eine Architekturentscheidung erforderlich ist, lege einen ADR-Entwurf mit Status Proposed an.
```

### Schlecht

```text
Baue das ganze Entra-Projekt fertig.
```

Der zweite Auftrag führt schnell zu einer großen Menge generischen Codes, die fachlich schlechter nachvollziehbar ist.

## Definition of Done

Ein Task ist abgeschlossen, wenn:

- Akzeptanzkriterien erfüllt sind,
- Dokumentation aktuell ist,
- Security-Auswirkungen bewertet wurden,
- Tests oder manuelle Validierung dokumentiert sind,
- ggf. ADR vorhanden ist,
- ein sinnvoller Commit möglich ist.
