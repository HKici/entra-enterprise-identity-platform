# ADR-0005: Source of Authority für Identitäten und Attribute

- Status: Proposed
- Datum: 2026-10-03

## Kontext

Die Nordstern Handelsgruppe arbeitet in einer hybriden Identity-Landschaft. Interne Mitarbeitende sind an Zentrale Hamburg, Lager Nord, Lager Süd und Filialen tätig; zusätzlich bestehen externe, privilegierte und Emergency-Access-Identitäten. Ohne Attribut-Ownership können HR-System, On-Premises Active Directory und Microsoft Entra ID widersprüchliche Daten und fehlerhafte Lifecycle- oder Berechtigungsentscheidungen erzeugen.

Die bisherigen Gruppen- und AU-Entwürfe benötigen verlässliche Angaben zu Beschäftigungsart und Standort, dürfen daraus aber keine impliziten Zugriffsrechte ableiten. Die Synchronisationstechnologie ist noch nicht ausgewählt.

## Entscheidung

Als Arbeitsmodell wird eine Attribut-Source-of-Authority vorgeschlagen:

- Ein generisches HR-System ist für interne Person-, Beschäftigungs- und Organisationsattribute sowie, soweit vorhanden, Standortattribute führend.
- On-Premises AD ist, falls ein AD-Konto benötigt wird, für dessen technisch notwendige Verzeichnisattribute authoritative, solange kein späteres Cloud-first-SoA-Modell diese Autorität ablöst.
- Microsoft Entra ID ist für cloud-spezifische Identitäts-, Zugriffs-, Gruppen-, AU- und Rollenobjekte führend.
- Ein noch nicht konkret benanntes Vertrags-/Sponsor-System führt für externe Dienstleister Auftrag, Sponsor und Laufzeit; Entra führt das technische externe Zugriffsobjekt.
- Separate privilegierte und Emergency-Access-Identitäten sind cloud-only und werden in Entra geführt. Privilegierte Identitäten bleiben an eine aktive beziehungsweise genehmigte Workforce-Funktion gekoppelt.

Für jedes Attribut gibt es eine Schreibautorität. Abweichungen werden an die führende Quelle zurückgegeben; „Last writer wins“ und unkontrollierte Rückschreibungen sind ausgeschlossen.

Diese Entscheidung legt weder Entra Connect noch Cloud Sync, kein Attribut-Mapping, keine Korrelationstechnik und keine Automatisierung fest.

## Begründung

Das Modell trennt fachliche Stammdaten von technischen Verzeichnis- und Cloud-Zugriffsdaten. Standortwerte können damit Site Groups und die direkte AU-Mitgliedschaft prüfbar unterstützen, ohne selbst App-, Ressourcen- oder Entra-Administratorrechte zu verleihen.

Cloud-only privilegierte und Emergency-Access-Identitäten bleiben vom normalen Workforce-Lifecycle und von On-Premises-Abhängigkeiten getrennt. Privilegierte Identitäten werden dennoch gegen die aktive beziehungsweise genehmigte Workforce-Funktion geprüft. Das senkt das Risiko, dass fachliche Änderungen oder lokale Verzeichnisprobleme diese hochsensiblen Konten unkontrolliert beeinflussen.

## Alternativen

### On-Premises AD als einzige führende Quelle

Verworfen, weil AD keine fachliche Autorität für Beschäftigung, Auftrag oder Standort ist und cloud-only Identitäten nicht angemessen abbildet.

### Microsoft Entra ID als einzige führende Quelle

Verworfen für die aktuelle Hybridlandschaft, weil damit HR- und On-Premises-Fachverantwortung ignoriert würde. Eine spätere SoA-Übertragung ist eine separate Entscheidung.

### Mehrere Systeme schreiben dieselben Attribute

Verworfen, weil Überschreibungen, Datenkonflikte und nicht nachvollziehbare Lifecycle-Entscheidungen entstehen.

## Konsequenzen

### Positiv

- nachvollziehbare Attributverantwortung und weniger Datenkonflikte,
- belastbare Grundlage für Gruppen-, AU- und Lifecycle-Prüfungen,
- Trennung von Workforce-, externen, privilegierten und Notfallidentitäten,
- geringeres Risiko impliziter Berechtigungen durch Standort- oder HR-Attribute.

### Negativ

- technische Korrelation, Synchronisation und Ausnahmeprozesse benötigen ein nachfolgendes Detaildesign,
- mehrere spezialisierte Quellen erfordern Ownership, Datenqualitätskontrollen und Monitoring,
- externe und privilegierte Lifecycles benötigen getrennte Governance-Prozesse.

## Security-Auswirkungen

Die Entscheidung unterstützt Least Privilege und Auditierbarkeit, weil keine Quelle allein aus Status- oder Standortdaten Berechtigungen erzeugt. Sie reduziert das Risiko verbleibender privilegierter Zugriffe bei Workforce-Austritten durch einen separaten Entzugscheck. Falsch gepflegte führende Daten oder fehlende Korrelation bleiben kritische Risiken und müssen vor einer Automatisierung validiert werden.

## Betriebsauswirkungen

Vor einer Annahme müssen Quellen, Attributqualität, Korrelation, technische Synchronisation, Ownership, JML-Fristen und Monitoring konkretisiert werden. Anzahl, Verwahrung und konkrete Schutzmaßnahmen für Emergency Access bleiben `CA-003` vorbehalten. Die Entscheidung bleibt bis zum Architektur- und Security-Review `Proposed`.
