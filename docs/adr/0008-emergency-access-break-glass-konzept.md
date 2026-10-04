# ADR-0008: Emergency-Access- / Break-Glass-Konzept

- Status: Proposed
- Datum: 2026-10-04

## Kontext

Reguläre Administratoridentitäten der Nordstern Handelsgruppe sind cloud-only, getrennt von Workforce-Identitäten und werden künftig durch Conditional Access, PIM sowie weitere Sicherheitskontrollen geschützt. Diese Kontrollen, ein Ausfall regulärer Authentifizierungswege oder eine fehlerhafte Tenant-Konfiguration können jedoch dazu führen, dass kein regulärer Administrator den Entra-Tenant sicher verwalten kann.

Emergency Access muss solche Zugriffsblockaden überwinden können, ohne selbst von On-Premises AD, Hybrid Sync, Föderation, einem externen Identity Provider, dem PIM-Aktivierungsweg oder demselben Zugangsmittel abhängig zu sein. Ein bloßer Ausschluss aus Conditional Access wäre kein ausreichender Schutz.

## Entscheidung

Als Arbeitsmodell werden mindestens zwei voneinander getrennte cloud-only Emergency-Access-Konten vorgeschlagen; Nordstern betreibt initial zwei. Jede Änderung der Kontenanzahl erfordert einen separaten Security-Review:

- Beide Konten werden ausschließlich in Microsoft Entra ID geführt und nicht aus HR, AD oder Hybrid Sync bereitgestellt oder verändert.
- Beide Konten verwenden keinen föderierten oder externen Anmeldeweg und erhalten Global Administrator dauerhaft aktiv, nicht nur PIM-eligible.
- Jedes Konto erhält mindestens einen separat getesteten phishing-resistenten Anmeldeweg. EA-01 und EA-02 dürfen kein gemeinsames Zugangsmittel, Gerät, Mobilfunkabhängigkeit, alleinigen Custodian, Verwahrort oder ungeprüften Vertrauensanker haben.
- Die konkrete Faktorkombination wird erst nach Kompatibilitäts-, Unabhängigkeits- und Ausfalltests entschieden. Diese ADR legt keine allgemeine Authentication Strength für Workforce- oder reguläre Administratorkonten fest.
- Die Konten werden bei späterem Enforcement direkt aus einschränkenden Conditional-Access-Policies ausgeschlossen. Diese Ausnahme wird durch getrennte Zugangsmittel, sichere Verwahrung, Vier-Augen-Freigabe, Alerting, Tests und Post-Mortem kompensiert.
- Jede Nutzung benötigt einen dokumentierten Incident- oder Recovery-Auslöser, Freigabe durch zwei unabhängige berechtigte Personen und unverzügliche Nachbereitung. Ist eine Wiederherstellung unmittelbar nötig und trotz dokumentierter Eskalation keine zweite berechtigte Person rechtzeitig verfügbar, darf ein organisatorischer Emergency Override ohne technischen PIM- oder Tenant-Approval-Workflow erfolgen. Er verlangt sofortiges Alerting, vollständige Protokollierung sowie nachträgliche Bestätigung durch Identity und Security.
- Jedes Konto wird mindestens alle 90 Tage einzeln auf Anmeldung, Recovery-Fähigkeit, CA-Ausschlüsse, Alarmierung, Verwahrung und Verantwortlichkeiten geprüft. Der Test bestätigt außerdem den cloud-only Status, eine `*.onmicrosoft.com`-Anmeldung sowie das Fehlen einer Föderations-, AD- oder Hybrid-Sync-Abhängigkeit.

Die Konten sind kein Ersatz für reguläre privilegierte Administration, PIM, Administrative Units oder einen Notfallzugang zu On-Premises-Systemen.

## Begründung

Zwei unabhängig verwahrte Konten reduzieren das Risiko eines vollständigen Lockouts durch Verlust, Defekt oder Kompromittierung eines einzelnen Zugangsmittels. Cloud-only und nicht föderierte Identitäten vermeiden Abhängigkeiten von AD, Hybrid Sync und externen Identity Providern.

Die dauerhafte aktive Global-Administrator-Rolle ist für den Notfall begrenzt gerechtfertigt: Ein PIM- oder Rollenaktivierungsfehler kann gerade Teil des Incidents sein. Das Vier-Augen-Prinzip, die strikte Zweckbindung, getrennte Verwahrung, Alarmierung und häufige Tests mindern das Risiko dieser Dauerberechtigung.

## Alternativen

### Ein einzelnes Break-Glass-Konto

Verworfen. Ein einzelner Faktor-, Verwahr- oder Kontofehler kann zum vollständigen Verlust der Recovery-Fähigkeit führen.

### Emergency Access aus On-Premises AD oder über Hybrid Sync bereitstellen

Verworfen. AD-, Synchronisations- oder Föderationsausfälle dürfen den Entra-Recovery-Weg nicht blockieren.

### Emergency Access über PIM-Eligible Rollen aktivieren

Verworfen. PIM, die erforderliche Aktivierung oder die reguläre MFA- bzw. CA-Kette können selbst beeinträchtigt sein.

### Ausschluss aus Conditional Access ohne weitere Kontrollen

Verworfen. Eine CA-Ausnahme allein würde ein unvertretbares Risiko hochprivilegierter, dauerhaft verfügbarer Konten erzeugen.

### Gleiche Zugangsmittel oder Verwahrung für beide Konten

Verworfen. Gemeinsame Faktoren, Geräte, Mobilfunkwege oder Custodians schaffen einen Single Point of Failure.

## Konsequenzen

### Positiv

- Wiederherstellungsweg bleibt von AD, Hybrid Sync, Föderation und regulärem PIM getrennt.
- Zwei unterschiedliche Konten und Faktoren erhöhen die Resilienz gegen Verlust und Kompromittierung.
- Direkte CA-Ausschlüsse verhindern Lockouts durch restriktive Policies.
- Monitoring, Vier-Augen-Prinzip und Tests machen jede Nutzung und Fehlkonfiguration prüfbar.

### Negativ

- Zwei dauerhaft aktive Global-Administrator-Konten erhöhen die Angriffsfläche und verlangen strikte betriebliche Kontrollen.
- Verwahrung, Test, Faktor-Lifecycle, Alarmierung und Post-Mortem erzeugen kontinuierlichen Betriebsaufwand.
- Ein vollständiger Entra- oder Internet-Ausfall bleibt außerhalb der Reichweite dieses Konzepts.

## Security-Auswirkungen

Das Konzept reduziert das Risiko eines Tenant-Lockouts, ohne Emergency Access als ungeschützten Sonderzugang zu behandeln. Phishing-resistente, getrennt verwahrte Faktoren, getrennte Custodians, kritisches Alerting und regelmäßige Tests kompensieren die notwendige CA-Ausnahme und die dauerhafte Global-Administrator-Rolle.

Eine nicht getestete Faktorabhängigkeit, ein fehlender CA-Ausschluss, unzureichende Alarmierung oder unkontrollierte Nutzung bleiben kritische Risiken. Der Verlust eines Kontos ist ein Security Incident und darf nicht dazu führen, dass beide Konten dasselbe Ersatzverfahren nutzen.

## Betriebsauswirkungen

Vor Annahme müssen die konkrete Faktorwahl, sichere Verwahrung, geschützte Notfallarbeitsplätze, Alarmierungsziel, Logaufbewahrung, unabhängige Kommunikationswege, Incident-Rollen und Testverfahren festgelegt und getestet werden. Der Zugang zu konkreten Kontodaten und Zugangsmitteln erfolgt ausschließlich außerhalb dieses Repositories über ein kontrolliertes Register.

Die Entscheidung bleibt bis zum Architektur- und Security-Review `Proposed`.
