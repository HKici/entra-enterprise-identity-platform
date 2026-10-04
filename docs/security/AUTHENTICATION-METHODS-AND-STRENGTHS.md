# Authentication Methods & Authentication Strengths

Status: **Entwurf (CA-004)**

## Ziel und Abgrenzung

Dieses Dokument definiert das fachliche Zielmodell für Authentifizierungsmethoden und Authentication Strengths der Nordstern Handelsgruppe. Es beschreibt zulässige und bevorzugte Verfahren je Persona sowie Bootstrap-, Recovery- und Rollout-Anforderungen. Es erzeugt keine Authentication Methods Policy, keine Authentication Strength, keine Conditional-Access-Policy und keine Zugangsmittel.

Authentication Methods Policy und Authentication Strengths sind getrennte Steuerungsebenen: Die Methods Policy bestimmt, welche Methoden Personen grundsätzlich registrieren und verwenden können; eine Authentication Strength beschränkt im Conditional Access, welche Methoden oder Kombinationen für einen Ressourcenzugriff genügen. [Microsoft Learn: Authentication Methods verwalten](https://learn.microsoft.com/en-us/entra/identity/authentication/concept-authentication-methods-manage), [Microsoft Learn: Authentication Strengths](https://learn.microsoft.com/en-us/entra/identity/authentication/concept-authentication-strength-how-it-works)

Das Modell respektiert die in [ADR-0007](../adr/0007-conditional-access-baseline-modell.md) vorgeschlagene CA-Baseline und hält Emergency Access gemäß [ADR-0008](../adr/0008-emergency-access-break-glass-konzept.md) getrennt. Die Architekturentscheidung ist in [ADR-0009](../adr/0009-authentication-methods-und-authentication-strengths.md) mit Status `Proposed` dokumentiert.

## Grundsätze

1. **Personengebundene Authentifizierung:** Jede interaktive Workforce-Identität authentifiziert sich personenbezogen; Shared Devices und Scanner rechtfertigen keine geteilten Benutzeridentitäten oder eine Abschwächung der Identity Controls.
2. **Passwordless bevorzugen:** Passwordless und phishing-resistente Verfahren werden für geeignete Benutzer- und Geräteszenarien gegenüber passwort- oder zustimmungsbasierten Verfahren bevorzugt.
3. **Starke Authentifizierung für privilegierte Zugriffe:** Privilegierte Administratoridentitäten benötigen phishing-resistente Authentifizierung; eine reine Push-, Einmalcode-, SMS- oder Voice-MFA genügt dafür nicht.
4. **Schwache Verfahren begrenzen:** SMS und Voice sind nur befristete Übergangs- oder Recovery-Verfahren für reguläre Workforce-Identitäten, nicht für privilegierte oder Emergency-Access-Identitäten.
5. **Bootstrap und Recovery sind kontrollierte Prozesse:** Temporary Access Pass (TAP) ist ein kontrolliertes Bootstrap- oder Recovery-Credential. Es ist kein gewünschtes dauerhaftes Authentifizierungsverfahren, keine eigene Ziel-Authentication-Strength und ersetzt keine Identitätsprüfung.
6. **Emergency Access bleibt separat:** Die initial zwei Emergency-Access-Konten besitzen einen separat validierten Methoden- und Faktor-Scope. Sie verwenden keine normalen Workforce-Bootstrap- oder Recovery-Prozesse; ihre Faktoren und deren Lifecycle sind von regulären Administratoridentitäten getrennt. Die Kontenanzahl bleibt Security-Review-pflichtig. Die Konten bleiben technisch von Microsoft Entra Authentication und den für ihren Scope zugelassenen Methoden abhängig, jedoch nicht von AD, Hybrid Sync, Föderation, PIM oder regulären Workforce-Faktoren.
7. **Keine ungetestete Produktannahme:** FIDO2/Passkeys, Windows Hello for Business, Microsoft Authenticator, CBA und Strengths werden nur dort eingesetzt, wo Geräte, Anwendungen, Browser, Betriebssysteme, Lizenzierung und Betriebsprozesse dies validiert unterstützen.

## Konzeptionelle Authentication Strengths

Microsoft Entra stellt die integrierten Strengths **Multifactor authentication**, **Passwordless MFA** und **Phishing-resistant MFA** bereit. Sie sind von Microsoft verwaltete Methodenkombinationen. Diese drei Kategorien bilden die fachliche Grundlage; eine kundenspezifische Strength wird mit CA-004 nicht beschlossen. Die jeweils aktuelle Microsoft-Methodenzuordnung sowie ihre Mandanten-, Geräte- und Anwendungseignung müssen vor jedem Enforcement geprüft werden. Dieses Dokument legt keine dauerhaft enthaltenen Methoden fest. [Microsoft Learn: Authentication Strengths](https://learn.microsoft.com/en-us/entra/identity/authentication/concept-authentication-strength-how-it-works)

| Konzeptionelle Strength | Zweck und geeignete Methodenfamilien | Persona- und Einsatzbezug | Status im Zielmodell |
| --- | --- | --- | --- |
| Multifactor authentication | Von Microsoft verwaltete Kombination für MFA-Anforderungen. | Mindestniveau für reguläre Workforce-Identitäten und – vorbehaltlich des externen Kollaborationsmodells – externe Dienstleister. | Für CA-BL-001 und CA-BL-007 als Baseline vorgesehen; die aktuelle Methodenzuordnung und Enforcement-Reife sind getrennt zu prüfen. |
| Passwordless MFA | Von Microsoft verwaltete Kombination für passwordlose MFA-Anforderungen. | Bevorzugte Weiterentwicklung für geeignete reguläre Workforce-Identitäten; für Shared Devices nur nach individuellem Geräte- und Anmeldetest. | Kein pauschaler Rollout und keine Geräteannahme mit CA-004; aktuelle Methodenzuordnung vor Enforcement prüfen. |
| Phishing-resistant MFA | Von Microsoft verwaltete Kombination für phishing-resistente MFA-Anforderungen. | Zwingend für privilegierte Administratoridentitäten; Emergency Access erfüllt einen separaten, unabhängig getesteten phishing-resistenten Weg. | Für CA-BL-002 nach Methoden-, Device- und Scope-Validierung vorgesehen. Für Emergency Access nicht über dessen CA-Policy, sondern über das separate Design nachweisen. |

Eine Strength ist keine Berechtigung und kein Ersatz für die Methods Policy. Mehrere gleichzeitig zutreffende CA-Policies können kumulativ wirken; deshalb müssen Strength-, CA- und Methodenscope gemeinsam getestet werden. Bei jeder Enforcement-Entscheidung ist die aktuelle, von Microsoft verwaltete Zuordnung der Methoden zur Strength erneut zu prüfen. [Microsoft Learn: Funktionsweise von Authentication Strengths](https://learn.microsoft.com/en-us/entra/identity/authentication/concept-authentication-strength-how-it-works)

## Methodenbewertung

| Methode | Fachliche Einordnung | Zulässigkeit und Grenzen |
| --- | --- | --- |
| Passkeys (FIDO2), einschließlich FIDO2-Sicherheitsschlüssel | Passwordless und phishing-resistent, wenn die jeweilige Entra-, Geräte- und Anwendungsunterstützung validiert ist. | Bevorzugt für geeignete Workforce-Szenarien und zulässig für privilegierte Administration. Nicht pauschal für Shared Devices, Scanner, jede Anwendung oder jeden externen Identity Provider voraussetzen. |
| Windows Hello for Business | Passwordless und phishing-resistent auf personengebundenen, geeigneten Windows-Geräten. | Bevorzugte Option für geeignete persönliche oder besonders geschützte Windows-Arbeitsplätze. Kein Standard für gemeinsam genutzte Geräte, solange Benutzerprofil-, Geräte- und Sitzungsmodell offen sind. |
| Microsoft Authenticator | Unterstützt App-basierte MFA, Passwortlos-Anmeldung und Passkeys. | Push- oder Code-MFA ist für Workforce zulässig, aber nicht phishing-resistent. Passkeys in Authenticator können bei validierter Unterstützung als passwordless und phishing-resistente Option bewertet werden. Nicht als alleiniger Notfallfaktor für Emergency Access verwenden. |
| Temporary Access Pass | Zeitlich begrenzter Bootstrap- und Recovery-Passcode für die Registrierung geeigneter Methoden. | Zulässig nur kontrolliert, kurzlebig, zweckgebunden und auditierbar. Kein Dauerverfahren und kein Ersatz für einen bereits eingerichteten starken Faktor. |
| Certificate-based Authentication | Potenziell phishing-resistente Sonderoption, etwa bei nachgewiesenem Smartcard- oder Zertifikats-Use-Case. | Nicht als allgemeines Workforce-Standardverfahren beschlossen. Vor Einsatz Vertrauenskette, Zertifikats-Lifecycle, Sperrung, Browser-/App-Unterstützung, CA-Interaktion und unabhängige Notfallverfügbarkeit prüfen. |
| SMS / Voice | Schwächere, nicht phishing-resistente Verfahren. | Ausschließlich befristet für reguläre Workforce-Bootstrap- oder Recovery-Ausnahmen nach dokumentierter Risikoprüfung. Nicht zulässig für privilegierte oder Emergency-Access-Identitäten und nicht geeignet, eine phishing-resistente Strength zu erfüllen. |
| Passwort | Basismerkmal eines möglichen Anmeldewegs, aber kein starker Faktor. | Nicht allein ausreichend für MFA, Passwordless MFA oder Phishing-resistant MFA. Passwortabhängigkeiten sind bei Bootstrap, Recovery und Emergency Access jeweils explizit zu bewerten. |

Microsoft führt unter den phishing-resistenten Methoden unter anderem Windows Hello for Business, Passkeys (FIDO2), FIDO2-Sicherheitsschlüssel und CBA auf. Microsoft Entra Passkeys auf Windows und Windows Hello for Business sind dabei unterschiedliche Credentials und müssen getrennt auf Eignung und Richtlinienwirkung geprüft werden. [Microsoft Learn: Authentication overview](https://learn.microsoft.com/en-us/entra/identity/authentication/concept-authentication-methods), [Microsoft Learn: Passkeys auf Windows](https://learn.microsoft.com/en-us/entra/identity/authentication/how-to-authentication-entra-passkeys-on-windows)

## Persona-Modell

| Persona | Grundsätzlich zulässige Methoden | Bevorzugt oder zwingend | Ausgeschlossen bzw. nur begrenzt |
| --- | --- | --- | --- |
| Reguläre Workforce – Zentrale Hamburg | Microsoft Authenticator, geeignete Passkeys (FIDO2), Windows Hello for Business auf geeigneten persönlichen Windows-Geräten, TAP für Bootstrap/Recovery; CBA nur als bestätigte Sonderoption. | Passwordless für geeignete Szenarien bevorzugt; mindestens MFA gemäß CA-BL-001 vorgesehen. | SMS/Voice nur befristet. Kein Kennwort als alleiniger Nachweis für geschützte Zugriffe. |
| Warehouse User – Lager Nord und Lager Süd | Personenbezogene Methoden wie für Workforce; TAP für kontrollierte Erstregistrierung oder Recovery. | MFA bleibt erforderlich. Passkeys oder Authenticator nur nach Test mit tatsächlichem Geräte-, Schicht- und Anmeldeablauf. | Kein gemeinsamer Faktor oder gemeinsames Konto. Windows Hello for Business, FIDO2/Passkeys und mobile Verfahren sind nicht automatisch für Scanner oder Shared Devices geeignet. |
| Store User – Filialen | Personenbezogene Workforce-Methoden; TAP für kontrollierte Erstregistrierung oder Recovery. | Passwordless für geeignete persönliche bzw. personengebundene Geräte bevorzugt. | Keine standortbezogene Ausnahme. SMS/Voice nur befristet; Shared-Device-Eignung getrennt prüfen. |
| Privileged Administrator | Geeignete phishing-resistente Passkeys (FIDO2), Windows Hello for Business auf besonders geschütztem Arbeitsweg oder CBA als validierte Sonderoption; TAP nur für kontrolliertes Bootstrap/Recovery. | Phishing-resistant MFA zwingend. Die separate administrative Identität bleibt an eine aktive bzw. genehmigte Workforce-Funktion gekoppelt. | Authenticator-Push/Code, SMS und Voice sind allein nicht ausreichend. Keine Abhängigkeit von einem regulären Workforce-Faktor als einziger Recovery-Weg. |
| External Contractor | Abhängig von künftigem B2B-, Cross-Tenant- oder anderem externen Kollaborationsmodell. | Starke MFA gemäß CA-BL-007 als fachliche Mindestanforderung; eine spezifische Strength erst nach Validierung des externen Authentifizierungswegs. | Keine Annahme, dass Nordstern Methoden im Home Tenant des Dienstleisters steuert. TAP für externe Gäste nicht als Standard-Bootstrap voraussetzen. |
| Emergency Access Administrator | Ausschließlich die im Emergency-Access-Design separat validierten, unabhängig verwahrten Faktoren innerhalb eines eigenen Methoden- und Faktor-Scopes. | Pro Konto mindestens ein getesteter phishing-resistenter Weg, unabhängig vom jeweils anderen Konto und von regulären Administratoridentitäten. | Keine normalen Workforce-Bootstrap-, SMS-, Voice-, Authenticator-Push- oder PIM-abhängigen Recovery-Flows; weiterhin technische Abhängigkeit von Entra Authentication und den für den Scope zugelassenen Methoden. |

## Bootstrap und Erstregistrierung

Für interne Workforce-Identitäten beginnt Bootstrap nach einer verifizierten Joiner- bzw. Aktivierungsentscheidung im führenden Prozess. Der fachliche Ablauf lautet:

1. Person, Beschäftigungsstatus und Zielpersona werden geprüft; daraus entsteht keine Berechtigung, aber die kontrollierte Bereitstellung einer Workforce-Identität.
2. Für Personen ohne registrierten starken Faktor kann ein kurzlebiger, zweckgebundener TAP ausgegeben werden.
3. Der TAP ermöglicht die Registrierung mindestens eines geeigneten personengebundenen Verfahrens, etwa Passkey (FIDO2), Windows Hello for Business auf einem geeigneten Gerät oder Microsoft Authenticator.
4. Die Registrierung wird in einem kontrollierten Ablauf nach CA-BL-004 getestet und protokolliert; der TAP wird nach Zweckabschluss nicht als dauerhafter Anmeldeweg behalten.
5. Für privilegierte Administration erfolgt die Registrierung der separaten Admin-Identität zusätzlich zum Workforce-Bootstrap und erst nach bestätigter Berechtigung; sie muss einen phishing-resistenten Weg bereitstellen.

TAP kann die Registrierung passwordloser Methoden und Windows Hello for Business unterstützen und kann bei föderierten Domains den Entra-Anmeldeweg verwenden. Er bleibt dabei ein kontrolliertes Bootstrap- oder Recovery-Credential, keine eigene Ziel-Authentication-Strength und kein gewünschtes dauerhaftes Authentifizierungsverfahren. Die konkrete Laufzeit, Einmal- oder Mehrfachnutzung, Ausgabe- und Identitätsprüfungsprozess werden mit CA-004 nicht festgelegt. [Microsoft Learn: Temporary Access Pass](https://learn.microsoft.com/en-us/entra/identity/authentication/howto-authentication-temporary-access-pass)

Für externe Dienstleister wird kein TAP- oder Erstregistrierungsmodell festgelegt. Die Unterstützung hängt vom späteren Typ der Guest-/External-Identität, ihrem Home Tenant und der Cross-Tenant-Konfiguration ab.

## Recovery und Methodenwechsel

Ein verlorener, defekter oder kompromittierter Faktor ist ein Security- und Lifecycle-Ereignis. Vor Recovery wird die Person über einen getrennten, dokumentierten Identitätsprüfungsprozess verifiziert. Anschließend wird der betroffene Faktor geprüft, gesperrt oder entfernt, der Zugang auditiert und ein geeigneter Ersatz registriert.

Für reguläre Workforce-Identitäten kann ein kurzlebiger TAP ein kontrollierter Recovery-Bootstrap sein, sofern die Identitätsprüfung, Ausgabe, Laufzeit und Nutzung dokumentiert sind. SMS oder Voice dürfen diese Recovery nicht dauerhaft ersetzen und sind keine Lösung für privilegierte oder Emergency-Access-Identitäten.

Für privilegierte Administratoridentitäten erfolgt Recovery über einen gesonderten Security- und Identity-Prozess. Ein Ersatz muss erneut phishing-resistent sein; ein schwächerer Faktor darf nicht als dauerhafter Rückfallweg etabliert werden. Für Emergency Access gelten ausschließlich die Verfahren bei Verlust oder Kompromittierung in [EMERGENCY-ACCESS.md](EMERGENCY-ACCESS.md); kein normaler TAP- oder Workforce-Recovery-Prozess darf diese Konten wiederherstellen.

Bei jedem Methodenwechsel sind verbliebene alte Methoden, nicht mehr benötigte Gerätebindungen und offene Sessions risikobasiert zu prüfen. Der genaue technische Lösch-, Token- und Sitzungsablauf bleibt ein Implementierungs- und Incident-Response-Thema.

## Warehouse, Shared Devices und Scanner

Das Modell verlangt individuelle Authentifizierung, entscheidet jedoch kein Device-Management-, Shared-Windows-, Scanner-, Browser- oder Sitzungsmodell. Warehouse- und Filialmitarbeitende bleiben im Workforce-CA-Scope; deren Gerätekontext begründet keine dauerhafte Ausnahme aus CA-BL-001, CA-BL-003, CA-BL-005 oder CA-BL-006.

Vor einem passwordlosen Rollout in Lager Nord oder Lager Süd sind mindestens Personenzuordnung, Schichtwechsel, sichere Faktoraufbewahrung, Scanner-/Anwendungsunterstützung, Offline-Verhalten, Wiederanmeldung und betriebliche Verfügbarkeit mit repräsentativen Geräten zu testen. Ein persönlicher Passkey oder Windows Hello for Business darf nicht als gemeinsamer Gerätefaktor interpretiert werden. Der Pilotstandort Lager Süd dient nur als kontrollierte Auswertungsgruppe, nicht als Architekturentscheidung für alle Lager.

## Externe Identitäten

CA-BL-007 fordert für externe Dienstleister konzeptionell MFA, aber das B2B-, Cross-Tenant-Trust- oder andere Kollaborationsmodell ist noch offen. Bis zur Entscheidung wird nicht angenommen, dass Nordstern im Home Tenant des Dienstleisters FIDO2, Passkeys, Windows Hello for Business, Authenticator oder Recovery-Methoden steuern kann.

Vor Enforcement sind mindestens Identitätstyp, Home- bzw. Resource-Tenant-Authentifizierungsweg, Zulässigkeit und Wirkung von MFA- bzw. Device-Claims, Strength-Unterstützung, Gast-Registrierung sowie die Abdeckung aller Guest-/External-Identitäten zu validieren. Cross-Tenant Access Settings können MFA- und Device-Claims aus vertrauenswürdigen Entra-Tenants berücksichtigen, sind hier aber ausdrücklich nicht entschieden. [Microsoft Learn: External ID und Conditional Access](https://learn.microsoft.com/en-us/entra/external-id/authentication-conditional-access)

## Wechselwirkungen mit Conditional Access und Emergency Access

| Steuerung | Wechselwirkung mit diesem Modell |
| --- | --- |
| CA-BL-001 – Require MFA Workforce | Mindestniveau für reguläre Workforce. Das Modell bevorzugt Passwordless MFA, setzt aber keine einzige Methode für alle Geräte oder Personas voraus. |
| CA-BL-002 – Require MFA Privileged Admins | Zielzustand ist Phishing-resistant MFA für die abgedeckten privilegierten Rollen. Built-in-, Custom- und AU-scoped Rollen benötigen weiterhin ihren separat validierten CA-Scope. |
| CA-BL-004 – Protect Security Info Registration | Bootstrap, Methodenwechsel und Windows-Hello-for-Business- bzw. macOS-Platform-SSO-Registrierung müssen vor Enforcement produktionsreif und getestet sein. |
| CA-BL-005 – Require MFA Risky SignIns | Benötigt vor Enforcement eine ausreichende registrierte MFA-Basis; Passwordless und phishing-resistente Verfahren werden nach Strength- und Methodenkompatibilität bewertet. |
| CA-BL-006 – Require Risk Remediation | Die Eignung des durch den Control ausgelösten Strength- und Recovery-Flows muss mit den zulässigen Methoden, Lizenzierung und Supportprozess getestet werden. |
| CA-BL-007 – Require MFA External Contractors | Keine konkrete externe Methode oder Strength, bis B2B/Cross-Tenant und Authentifizierungsweg validiert sind. |
| CA-003 – Emergency Access | Aus CA-Ausschlüssen folgt keine Schwächung: Emergency Access verwendet separat getestete phishing-resistente Faktoren in einem eigenen Methoden- und Faktor-Scope, aber keine normalen Workforce-Bootstrap- oder Recovery-Flows. Die Konten bleiben technisch von Entra Authentication und den für den Scope zugelassenen Methoden abhängig. |

CA-BL-003 bleibt zusätzlich erforderlich, damit Legacy Authentication keine modernen Methoden- oder Strength-Anforderungen umgeht.

## Offene Produkt- und Architekturfragen

- Lizenz- und Editionsvoraussetzungen für die vorgesehenen Methods Policies, Authentication Strengths, FIDO2/Passkeys, Windows Hello for Business, CBA, TAP und Reporting.
- Anwendungskompatibilität für Passwordless, Passkeys, Windows Hello for Business, CBA und Browser-/Betriebssystemkombinationen.
- Geräte-, Shared-Device-, Scanner-, Offline- und Sitzungsmodell für Lager und Filialen.
- Konkrete TAP-Lebensdauer, Einmal-/Mehrfachnutzung, Ausgabeberechtigungen, Identitätsprüfung und Audit-Nachweise.
- Endgültige Strength-Zuordnung zu Ressourcen und CA-Policies sowie Behandlung von Custom Roles und AU-scoped Rollen.
- CBA-Vertrauenskette, Zertifikats-Lifecycle, Sperrung und mögliche Abhängigkeit von On-Premises-PKI oder gemeinsamen Vertrauensankern.
- B2B-, Cross-Tenant-Trust- und Guest-Authentifizierungsmodell einschließlich MFA- und Device-Claim-Vertrauen.
- Recovery- und Incident-Runbooks, insbesondere für verlorene Methoden, kompromittierte Geräte und offene Sessions.
