# ADR-0010: Joiner-/Mover-/Leaver-Lifecycle-Modell

- Status: Proposed
- Datum: 2026-10-04

## Kontext

Die Nordstern Handelsgruppe benötigt einen einheitlichen fachlichen Prozess für den Lebenszyklus interner Workforce-, externer, privilegierter und Emergency-Access-Identitäten. HR, On-Premises AD und Microsoft Entra ID haben unterschiedliche Attribut- und Objektzuständigkeiten. Ohne verbindliche Kontrollpunkte könnten Standort- oder Funktionswechsel zu Permission Accumulation führen, Workforce-Leaver privilegierte Konten zurücklassen oder externe Zugriffe nach Ablauf eines Auftrags fortbestehen.

ADR-0003 trennt Persona-, Site-, Business-Role- und Access Groups. ADR-0004 begrenzt AUs auf delegierte Administration. ADR-0005 legt die Attributautorität vor und ADR-0006 hält Synchronisationsdetails offen. Das Lifecycle-Modell muss diese Entscheidungen respektieren, ohne eine technische Workflow- oder Provisioning-Lösung festzulegen.

## Entscheidung

Als Arbeitsmodell wird vorgeschlagen:

- Ein bestätigtes Ereignis in der führenden Fachquelle eröffnet einen nachverfolgbaren Lifecycle-Fall; interne Workforce-Ereignisse stammen aus HR, externe aus dem noch nicht konkret benannten Vertrags-/Sponsor-System.
- Zukünftige Eintrittsdaten dürfen eine kontrollierte Vorbereitung auslösen, aber keine interaktive Nutzung oder Zugriffsfreigabe vor dem bestätigten Startzeitpunkt. Aktivierung erfordert eine erneute Status-, Persona-, Standort- und Methodenprüfung.
- Persona-, Site- und Business-Role-Zuordnungen werden getrennt von Access Groups und Entra-Rollen geprüft. Access Groups dürfen nicht allein aus HR- oder Standortattributen abgeleitet werden.
- Direkte AU-Mitgliedschaften werden nur als Scope für delegierte Administration geprüft. Sie sind keine Berechtigung und werden nicht aus Security-Group-Mitgliedschaften abgeleitet.
- Jeder Mover prüft alte und neue Zuordnungen. Nicht mehr erforderliche Access Groups, Business Role Groups, direkte AU-Mitgliedschaften und privilegierte Zuweisungen werden vor oder zusammen mit neuen Zugriffsvergaben entzogen; jede zeitliche Überschneidung ist befristet zu begründen.
- Jeder Workforce-Leaver sperrt zum wirksamen Zeitpunkt zuerst die interaktive Workforce-Anmeldung und behandelt Sessions sowie Tokens im vorgesehenen technischen Prozess. Authentication Methods werden danach entsprechend Incident-, Retention- und Forensik-Anforderungen entfernt, gesperrt oder für die Nachweisführung erhalten. Die Prüfung aller verknüpften privilegierten Identitäten, Rollen und Zugriffsgruppen ist zwingend; Löschung folgt erst den separat festzulegenden Retention- und Governance-Prozessen.
- Externe Zugriffe sind an Sponsor, Auftrag und Laufzeit gebunden. Das bestätigte Enddatum ist grundsätzlich das maximale genehmigte Zugriffsende; eine Verlängerung muss vorher bestätigt sein. Ohne diese Bestätigung endet der Zugriff zum Enddatum ohne implizite Grace Period. Eine später benötigte Grace Period ist eine explizite, dokumentierte Governance-Ausnahme mit Owner, Begründung und Ablaufdatum; das technische Kollaborationsmodell wird nicht vorweggenommen.
- Privilegierte Identitäten bleiben cloud-only und zusätzlich zur Workforce-Identität an eine aktive beziehungsweise genehmigte administrative Funktion gebunden. Rollen und PIM folgen einem separaten Design.
- Emergency Access wird nicht durch Workforce-JML deaktiviert oder gelöscht. Custodian- oder Verantwortungswechsel lösen ausschließlich den kontrollierten Emergency-Access-Review aus.
- Eine spätere Automatisierung muss Vorgangskennung und gewünschten Zielzustand verwenden, idempotent arbeiten, Fehler sichtbar behandeln und keine „Last writer wins“-Logik verwenden.

## Begründung

Das Modell nutzt die führenden Fachquellen als Auslöser, ohne technische Verzeichnisobjekte mit Berechtigungen gleichzusetzen. Die getrennte Prüfung bei Movern vermeidet, dass Standort- oder Funktionswechsel alte Zugriffe fortschreiben oder neue Zugriffe implizit verleihen.

Der separate privilegierte Lifecycle und die verpflichtende Prüfung bei Workforce-Leavern begrenzen das Risiko verbliebener Administratorrechte. Die Abgrenzung von Emergency Access erhält dessen Notfallresilienz und verhindert, dass ein reguläres Personalereignis die Recovery-Fähigkeit unkontrolliert beeinflusst.

## Alternativen

### HR- oder Standortattribute direkt als Access-Group-Zuweisung verwenden

Verworfen. Attribute können eine Prüfung auslösen, ersetzen aber keine zielsystembezogene Freigabe und keine Ownership einer Access Group.

### Alle Identitäten über denselben Workforce-JML-Prozess behandeln

Verworfen. Externe benötigen Sponsor- und Laufzeitkontrollen, privilegierte Identitäten einen gesonderten Entzug und Emergency Access einen eigenständigen Kontrollprozess.

### Leaver erst nach vollständigem M365- und Geräte-Offboarding sperren

Verworfen. Die Sperrung interaktiver Zugriffe und der Entzug privilegierter Rechte dürfen nicht von nachgelagerten Mailbox-, Daten-, Ownership- oder Geräteprozessen abhängen.

### Konflikte durch „Last writer wins“ auflösen

Verworfen. Dies würde die in ADR-0005 festgelegte Attributautorität verletzen und unnachvollziehbare Lifecycle- oder Berechtigungsentscheidungen erzeugen.

## Konsequenzen

### Positiv

- klare Lifecycle-Auslöser und Auditierbarkeit über Identitätsarten hinweg;
- geringeres Risiko von Permission Accumulation bei Standort- und Funktionswechseln;
- verpflichtender privilegierter Entzugscheck bei Workforce-Leavern;
- separate, resiliente Behandlung von Emergency Access;
- Grundlage für spätere Lifecycle Workflows, Access Reviews und PIM ohne Produktauswahl.

### Negativ

- fachliche Owner, Datenqualität, Ausnahmen und Nachweisführung erzeugen zusätzlichen Governance-Aufwand;
- mehrere abhängige Zielsysteme benötigen koordinierte Fehlerbehandlung und Eskalation;
- Fristen, technische Umsetzung und Messwerte bleiben vor der Einführung noch zu entscheiden.

## Security-Auswirkungen

Das Modell verbessert Least Privilege und reduziert das Risiko nicht mehr benötigter Zugriffe, insbesondere bei Movern, Workforce-Leavern und administrativen Rollenwechseln. Es verhindert, dass Standort- oder Persona-Gruppen als implizite Berechtigung dienen, und erhält die Trennung von Emergency Access.

Risiken bleiben bei verspäteten oder fehlerhaften Quellereignissen, unklarer Korrelation, fehlenden Owner-Bestätigungen, nicht abgeschlossenen Entzügen und unzureichend getesteten Abhängigkeiten bestehen. Diese Risiken müssen durch Monitoring, Auditierung, manuelle Eskalation und spätere technische Tests behandelt werden.

## Betriebsauswirkungen

Vor Annahme müssen Governance-SLAs, Rollen- und Freigabematrix, HR- und Sponsor-Datenqualität, Retention, Monitoring, Fehler- und Eskalationswege sowie die Zielsystemfähigkeit für Sperrung, Session-/Token-Behandlung und Entzug konkretisiert werden. Diese ADR konfiguriert keine Entra Lifecycle Workflows, Synchronisation, Gruppen, Rollen, Policies oder Automatisierung.

Die Entscheidung bleibt bis zum Architektur- und Security-Review `Proposed`.
