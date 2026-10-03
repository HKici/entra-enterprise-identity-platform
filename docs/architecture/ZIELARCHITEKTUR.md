# Zielarchitektur

## Leitprinzip

Microsoft Entra ID wird als zentrale Cloud-Identity- und Access-Control-Plane verwendet.

Die Zielarchitektur wird schrittweise entwickelt. Dieses Dokument beschreibt zunächst die Architekturprinzipien, nicht die finale technische Implementierung.

## Architekturprinzipien

### 1. Identity as a Platform

Identity wird nicht als Sammlung einzelner Konfigurationen betrachtet, sondern als gemeinsam genutzte Unternehmensplattform.

### 2. Zero Trust

Zugriff wird anhand von Identität, Authentifizierungsstärke, Gerätezustand, Risiko und Ressource bewertet.

### 3. Least Privilege

Benutzer, Administratoren, Anwendungen und Automationen erhalten nur die minimal erforderlichen Rechte.

### 4. Policy as Code

Wo sinnvoll, werden Konfigurationen versioniert, überprüft und reproduzierbar ausgerollt.

### 5. Controlled Rollout

Neue sicherheitsrelevante Policies werden stufenweise eingeführt:

1. Design
2. Review
3. Report-only
4. Pilot
5. kontrollierter Rollout
6. Enforcement
7. Monitoring

### 6. Separation of Identities

Privilegierte Tätigkeiten werden mit separaten administrativen Identitäten durchgeführt.

## Noch offene Architekturentscheidungen

- Source-of-Authority-Modell (in ADR-0005 mit Status `Proposed`; technische Konkretisierung offen)
- Auswahl zwischen Entra Connect Sync und Cloud Sync (ADR-0006, Status `Proposed`)
- Ausgestaltung Administrative Units
- Modell für Lager-/Filialidentitäten
- gruppenbasierte vs. attributbasierte Zuweisungen
- App-only Authentication für Automatisierung
