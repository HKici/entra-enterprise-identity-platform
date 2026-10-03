# ADR-0002: Neue Conditional-Access-Policies starten in Report-only

- Status: Accepted
- Datum: 2026-10-03

## Kontext

Fehlerhafte Conditional-Access-Policies können legitime Benutzer und Administratoren aussperren.

## Entscheidung

Neue oder wesentlich geänderte Conditional-Access-Policies werden in diesem Projekt standardmäßig zunächst im Modus `report-only` modelliert.

Enforcement erfolgt erst nach Review, Pilotierung und Auswertung.

## Begründung

Damit werden Auswirkungen sichtbar, bevor eine Policy aktiv Zugriffe blockiert oder zusätzliche Anforderungen erzwingt.

## Alternativen

### Direktes Enforcement

Für neue Policies verworfen.

### Nur manuelles Testen

Nicht ausreichend für komplexe Nutzer- und Gerätekombinationen.

## Konsequenzen

### Positiv

- geringeres Lockout-Risiko
- messbare Auswirkungen
- kontrollierbarer Rollout

### Negativ

- längerer Rollout
- Monitoring erforderlich

## Security-Auswirkungen

Policies schützen während der Report-only-Phase noch nicht aktiv. Bestehende Schutzmaßnahmen müssen bis zum Enforcement erhalten bleiben.

## Betriebsauswirkungen

Pilotgruppen und Auswertung der Sign-in Logs werden Bestandteil des Rollout-Prozesses.
