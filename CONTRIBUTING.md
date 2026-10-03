# Mitwirken

## Grundsatz

Änderungen sollen klein, nachvollziehbar und überprüfbar sein.

## Workflow

1. Issue oder klaren Arbeitsauftrag definieren.
2. Branch von `main` erstellen.
3. Änderung implementieren.
4. Dokumentation und Tests aktualisieren.
5. Sicherheitsauswirkungen prüfen.
6. Commit mit Conventional-Commit-Präfix erstellen.
7. Pull Request mit Motivation, Auswirkungen und Testnachweis erstellen.
8. Nach Review nach `main` mergen.

## Pull-Request-Checkliste

- [ ] Änderung ist fachlich begründet.
- [ ] Keine realen Tenant-Daten enthalten.
- [ ] Keine Secrets enthalten.
- [ ] Graph-Permissions sind minimal.
- [ ] Sicherheitsauswirkungen dokumentiert.
- [ ] Dokumentation aktualisiert.
- [ ] Tests / Validierung durchgeführt.
- [ ] ADR ergänzt, falls Architekturentscheidung betroffen.
