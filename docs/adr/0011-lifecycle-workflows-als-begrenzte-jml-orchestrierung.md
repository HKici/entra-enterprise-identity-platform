# ADR-0011: Lifecycle Workflows als begrenzte JML-Orchestrierung

- Status: Proposed
- Datum: 2026-10-04

## Kontext

GOV-001 definiert den fachlichen Lebenszyklus für Workforce-, externe, privilegierte und Emergency-Access-Identitäten. Microsoft Entra Lifecycle Workflows kann ausgewählte Aufgaben innerhalb von Entra orchestrieren, wenn Benutzerobjekte und benötigte Attribute dort vorhanden sind. Ohne eine Abgrenzung könnten technische Workflow-Aufgaben jedoch fälschlich als Ersatz für HR, Sponsorprozesse, Hybrid-Provisioning, PIM oder vollständiges Offboarding verstanden werden.

ADR-0005 bestimmt die Attributautorität, ADR-0006 hält Hybrid-Synchronisation offen, ADR-0007 und ADR-0009 regeln Conditional-Access- und Authentication-Leitplanken, ADR-0008 trennt Emergency Access. Das Workflow-Modell muss diese Entscheidungen respektieren.

## Entscheidung

Als Arbeitsmodell wird vorgeschlagen:

- Microsoft Entra Lifecycle Workflows wird ausschließlich als gezielter Entra-interner Orchestrierungsbaustein nach einem fachlich bestätigten JML-Ereignis bewertet; GOV-001 bleibt die fachliche Quelle.
- Geeignete Kandidaten sind Benachrichtigungen, klar begrenzte Aufgaben für unterstützte nicht privilegierte Benutzerkonten, ausgewählte statische Cloud-Gruppen sowie fachlich genehmigte Lizenzaufgaben. Jede Aufgabe benötigt einen dokumentierten Zielzustand, Owner, Scope und Fehlerpfad.
- Joiner- und Leaver-Zeitpunkte können nur verarbeitet werden, wenn qualitätsgesicherte Attribute auf dem Entra-Objekt verfügbar sind. Zeitplanung und Synchronisationslatenz sind vor Einsatz zu validieren.
- Mover-Workflows dürfen ausgewählte Entzüge oder Benachrichtigungen unterstützen, aber weder Access Groups allein aus HR-Attributen zuweisen noch Permission Accumulation ersetzen. Direkte AU-Mitgliedschaften bleiben separat.
- Für Leaver werden Sign-in Block und unterstützte ausgewählte Entzüge als mögliche Aufgaben bewertet. Sessions/Tokens, Authentication Methods, Mailbox, Daten, Geräte, Ownership und Retention bleiben außerhalb regulärer Lifecycle Workflows.
- Externe Dienstleister, privilegierte Identitäten und Emergency Access erhalten keine pauschale Lifecycle-Workflow-Automatisierung. Sponsorprozesse, PIM und das externe Kollaborationsmodell bleiben separat; Emergency Access wird niemals durch reguläre Workflows deaktiviert, gelöscht oder in seinen Methoden verändert.
- Custom Task Extensions, Logic Apps und weitere externe Orchestrierung sind nicht Bestandteil dieser Entscheidung und benötigen bei Bedarf ein eigenes Security- und Integrationsdesign.
- Jeder spätere Workflow benötigt Pilotierung, Audit-Auswertung, manuelle Eskalation für kritische Fehler und einen idempotenten Zielzustandsabgleich. Kritische Entzüge dürfen nicht still als erfolgreich gelten.

## Begründung

Das Modell nutzt Lifecycle Workflows dort, wo Entra dokumentierte Standardaufgaben für klar abgegrenzte Benutzer und Objekte anbietet. Es vermeidet zugleich, dass fachliche Entscheidungen, nicht unterstützte Hybridaufgaben oder hochprivilegierte Identitäten in eine ungeeignete Workflow-Automatisierung gezogen werden.

Die Begrenzung bewahrt die Gruppen- und AU-Semantik aus ADR-0003 und ADR-0004, die Attributautorität aus ADR-0005 sowie die getrennten Sicherheitsmodelle für Administration und Emergency Access.

## Alternativen

### Vollständigen JML-Prozess mit Lifecycle Workflows automatisieren

Verworfen. Lifecycle Workflows ersetzen weder führende Fachquellen noch komplexe Hybrid-, App-, PIM-, Daten- oder Emergency-Access-Prozesse.

### Lifecycle Workflows grundsätzlich nicht einsetzen

Nicht vorgeschlagen. Für klar abgegrenzte, unterstützte Entra-Aufgaben können sie Auditierbarkeit und Wiederholbarkeit verbessern; die konkrete Einführung bleibt jedoch nachgelagert.

### Privilegierte Identitäten und Emergency Access in reguläre Workflows aufnehmen

Verworfen. Privilegierte Rollen und PIM benötigen einen separaten Security-Prozess; Emergency Access darf nicht von regulären Workforce-Lifecycle-Abläufen abhängig sein.

### Fehlende Aufgaben sofort über Custom Task Extensions erweitern

Verworfen. Zusätzliche externe Orchestrierung erhöht Berechtigungs-, Verfügbarkeits- und Fehlerbehandlungsrisiken und benötigt eine eigene Architekturentscheidung.

## Konsequenzen

### Positiv

- begrenzte, nachvollziehbare Entra-Orchestrierung für geeignete JML-Teilschritte;
- keine Vermischung von fachlicher Datenautorität und technischer Ausführung;
- geringeres Risiko automatischer Berechtigungsausweitung bei Movern;
- bessere Nachweisbarkeit durch Workflow-Historie und Audit-Logs nach einer späteren Einführung.

### Negativ

- Datenqualität, Synchronisationslatenz, Lizenzierung, Scope und Fehlerszenarien müssen vor Nutzung geprüft werden;
- viele JML-Aufgaben bleiben bewusst in externen oder manuellen Prozessen;
- keine kurzfristige vollständige Automatisierung von Hybrid- oder Offboarding-Szenarien.

## Security-Auswirkungen

Die Begrenzung verhindert, dass Lifecycle Workflows implizit Access Groups, privilegierte Rollen oder Emergency-Access-Änderungen vergeben. Sie stärkt die Nachvollziehbarkeit wiederholbarer, unterstützter Entzüge, sofern Auditierung und Eskalation überprüft werden.

Falsche Scopes, fehlerhafte Attribute, unklare Hybridzustände oder still fehlgeschlagene kritische Aufgaben bleiben Risiken. Sie müssen durch Pilotierung, Monitoring, JML-Abschlusskontrolle und manuelle Eskalation behandelt werden.

## Betriebsauswirkungen

Vor Annahme müssen Lizenzierung, Attributverfügbarkeit, Synchronisationslatenzen, Aufgabensupport, Owner, Fehler- und Retry-Verhalten, Auditierung, Pilotierung sowie die Schnittstellen zu HR, Sponsorprozessen und Zielsystemen bewertet werden. Diese ADR erzeugt keine Lifecycle Workflows, APIs, Graph- oder PowerShell-Automatisierung.

Die Entscheidung bleibt bis zum Architektur- und Security-Review `Proposed`.
