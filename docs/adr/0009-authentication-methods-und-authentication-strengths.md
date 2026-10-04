# ADR-0009: Authentication Methods und Authentication Strengths

- Status: Proposed
- Datum: 2026-10-04

## Kontext

Die Nordstern Handelsgruppe benötigt ein einheitliches fachliches Modell für Authentifizierung, das die Unterschiede zwischen regulärer Workforce, Warehouse Shared Devices, privilegierter Administration, externen Dienstleistern und Emergency Access abbildet. Ohne eine klare Trennung würden schwache Übergangsverfahren, Geräteabhängigkeiten oder Recovery-Flows unbeabsichtigt für privilegierte und Notfallzugriffe verwendet.

Conditional Access kann Authentication Strengths zur Ressourcenkontrolle verwenden, während die Authentication Methods Policy die grundsätzlich nutzbaren Methoden steuert. Die CA-Baseline verlangt MFA für Workforce und externe Identitäten sowie stärkeren Schutz für privilegierte Administration; konkrete Methoden, Device-Standards, externe Trusts und Recovery-Prozesse waren bisher offen.

## Entscheidung

Als Arbeitsmodell wird vorgeschlagen:

- Die integrierten Kategorien Multifactor authentication, Passwordless MFA und Phishing-resistant MFA bilden als von Microsoft verwaltete Methodenkombinationen die konzeptionellen Authentication Strengths. Eine Custom Strength wird nicht mit dieser Entscheidung erstellt oder festgelegt. Die jeweils aktuelle Microsoft-Methodenzuordnung wird vor Enforcement geprüft; diese ADR schreibt keine dauerhaft enthaltenen Methoden fest.
- Reguläre Workforce-Identitäten erfüllen mindestens MFA. Passwordless-Verfahren werden für geeignete Personen-, Geräte- und Anwendungsszenarien bevorzugt, aber nicht pauschal vorausgesetzt.
- Privilegierte Administratoridentitäten benötigen einen phishing-resistenten Anmeldeweg. CA-BL-002 bleibt bis zur Methoden-, Device-, Rollen- und Scope-Validierung `report-only`.
- Passkeys (FIDO2), Windows Hello for Business und CBA sind mögliche phishing-resistente Verfahren. Ihre technische Verfügbarkeit, Geräte- und Anwendungseignung sowie Lizenzierung müssen vor Einsatz validiert werden.
- Microsoft Authenticator ist für Workforce-MFA zulässig; Push- und Codeverfahren sind allein nicht ausreichend für den privilegierten phishing-resistenten Zielzustand. Passkeys in Authenticator können bei validierter Unterstützung anders eingeordnet werden.
- TAP kann als kontrolliertes, zeitlich begrenztes Bootstrap- oder Recovery-Credential verwendet werden. Es ist weder ein gewünschtes dauerhaftes Authentifizierungsverfahren noch eine eigene Ziel-Authentication-Strength. SMS und Voice bleiben ausschließlich befristete, dokumentierte Workforce-Übergangs- oder Recovery-Verfahren und sind für privilegierte sowie Emergency-Access-Identitäten ausgeschlossen.
- Warehouse Shared Devices und Scanner behalten personenbezogene Identitäten, erhalten aber mit dieser Entscheidung kein Device-, Sitzungs- oder spezifisches Faktor-Modell.
- Externe Identitäten erhalten erst nach B2B-/Cross-Tenant- und Kollaborationsentscheidung eine verbindliche Methoden- oder Strength-Zuordnung.
- Emergency Access bleibt ein separates, cloud-only Modell mit separat validiertem Methoden- und Faktor-Scope sowie unabhängig von regulären Administratoridentitäten getesteten phishing-resistenten Faktoren. Es verwendet keine normalen Workforce-Bootstrap- oder Recovery-Flows. Die Konten bleiben technisch von Microsoft Entra Authentication und den für ihren Scope zugelassenen Methoden abhängig, jedoch nicht von AD, Hybrid Sync, Föderation, PIM oder regulären Workforce-Faktoren.

## Begründung

Die dreistufige Strength-Ausrichtung ermöglicht eine MFA-Baseline, eine kontrollierte Passwordless-Weiterentwicklung und einen klaren phishing-resistenten Schutz für besonders risikoreiche Administrationszugriffe. Sie vermeidet zugleich eine unzulässige Annahme, dass jede Anwendung, jedes Gerät oder jeder externe Identity Provider FIDO2, Passkeys oder Windows Hello for Business unterstützt.

TAP erlaubt einen kontrollierten Übergang zur Registrierung geeigneter starker Verfahren, ohne SMS oder Voice als dauerhaften Standard zu etablieren. Der separat validierte Methoden- und Faktor-Scope für Emergency Access verhindert, dass reguläre Workforce- oder Administrator-Bootstrap- und Recovery-Flows den Recovery-Weg mitbetreffen.

## Alternativen

### Eine einheitliche Methode für alle Personas sofort erzwingen

Verworfen. Geräte, Anwendungen, Warehouse-Scanner und externe Kollaborationswege sind noch nicht ausreichend validiert. Ein einheitlicher Zwang würde Lockouts oder Umgehungsdruck erzeugen.

### Push-MFA, SMS oder Voice als dauerhaften Standard für privilegierte Administration

Verworfen. Diese Verfahren erfüllen den phishing-resistenten Zielzustand nicht und können von Geräte-, Mobilfunk- oder Zustimmungsabhängigkeiten betroffen sein.

### Passwordless ohne Bootstrap- und Recovery-Modell ausrollen

Verworfen. Ohne kontrollierte Erstregistrierung, Verlustbehandlung und Faktorwechsel können Benutzer und Administratoren ausgesperrt werden.

### Emergency Access ohne separaten Methoden- und Faktor-Scope betreiben

Verworfen. Ein ungeprüfter gemeinsamer Scope oder gemeinsame AD-, Föderations- oder Workforce-Abhängigkeiten reduzieren die Notfallresilienz. Emergency Access bleibt dabei technisch von Entra Authentication und den für seinen Scope zugelassenen Methoden abhängig.

## Konsequenzen

### Positiv

- Privilegierte Administration erhält einen klaren phishing-resistenten Zielzustand.
- Workforce kann schrittweise zu passwordless Verfahren migrieren, ohne Shared Devices oder externe Identitäten zu übergehen.
- Schwächere Mobilfunkverfahren werden eingegrenzt und nicht zu einem dauerhaften Hochrisiko-Standard.
- TAP, CA-BL-004 und Risiko-Policies erhalten einen gemeinsamen fachlichen Bootstrap- und Recovery-Rahmen.

### Negativ

- Methoden-, Geräte-, Anwendungs- und Lizenzvalidierungen erzeugen vor Enforcement zusätzlichen Aufwand.
- Unterschiedliche Personas benötigen unterschiedliche Registrierungs- und Supportpfade.
- Bis zur Validierung bleiben einzelne CA-Strength-Enforcements im `report-only`-Status.

## Security-Auswirkungen

Das Modell senkt das Risiko phishinganfälliger privilegierter Anmeldungen und begrenzt SMS/Voice auf kontrollierte Ausnahmen. Es erhält den Schutz von Emergency Access durch unabhängige Faktoren und verhindert, dass Shared Devices automatisch schwächere Identitätskontrollen erhalten.

Fehlerhafte Methodenscopes, ungetestete Strength-Kombinationen, unzureichende Bootstrap-Prozesse oder nicht aufgelöste externe Trust-Annahmen bleiben Security-Risiken. Sie müssen vor Enforcement durch Pilotierung, Sign-in-Auswertung, Recovery-Tests und Security-Review behandelt werden.

## Betriebsauswirkungen

Vor Annahme müssen Methodenscopes, TAP-Ausgabe und -Audit, Registrierungs- und Recovery-Prozesse, Geräte- und Anwendungsunterstützung, CBA-Abhängigkeiten, Lizenzierung, externe Trusts, CA-Scopes und Monitoring konkretisiert werden. Es werden mit dieser ADR keine Entra Policies, Schlüssel, Zertifikate, Passkeys oder Secrets erzeugt.

Die Entscheidung bleibt bis zum Architektur- und Security-Review `Proposed`.
