# Anforderungen

Diese Seite beschreibt die grundlegenden Anforderungen an die verschiedenen Modellvarianten der Möbelmodule.  
Ziel ist es, vollständig parametrisierte, industriell automatisierbare und einfach montierbare Möbel zu entwickeln.

---

## 1. Parametrik & Variantenfähigkeit

- Das gesamte CAD-Modell muss **100 % parametrisch** aufgebaut sein.
- Alle relevanten Maße müssen über Parameter steuerbar sein.

### 1.1 User-Parameter
Parameter, die direkt vom Nutzer im Konfigurator verändert werden können:

- Länge
- Breite
- Höhe (Lounge und Esstisch Höhe)
- Winkel der Enden (z. B. bei schiefen Balkonen oder nicht rechtwinkligen Einbausituationen)
- Öffnungsvarianten (z. B. fest, teilweise klappbar, Truhe)

### 1.2 Developer-Parameter (Dev-Parameter)
Interne Parameter zur Steuerung der Konstruktion:

- Brett- und Balkenquerschnitte
- Spaltmaße
- Profilhöhen und -breiten
- Materialstärken
- Mindest- und Maximalabstände
- Fertigungstoleranzen
- ...

---

## 2. Konstruktionsprinzip

- Die Konstruktion darf **ausschließlich aus Balken und Brettern** bestehen.
- Alle Bauteile müssen:
  - mit **standardisierten Querschnitten**
  - und **einfachen Geometrien**
  gefertigt werden können.
- Keine Sonderteile, keine komplexen Freiformflächen.

---

## 3. Automatisierte Fertigung

- Alle Bauteile müssen für eine **100 % automatisierte maschinelle Fertigung** ausgelegt sein.
- Ziel:
  - Minimierung der Fertigungskosten
  - Skalierbarkeit
  - On-Demand-Produktion nach Bestellungseingang
  - Möglichst geringer Lagerbestand

### 3.1 Fertigungsanforderungen

- Zuschnitt vollständig CNC- oder sägebasiert
- Bohrungen, Nuten und Konturen maschinell erzeugbar
- Keine manuelle Nacharbeit notwendig
- Wiederholgenaue und reproduzierbare Geometrie

---

## 4. Montagefreundlichkeit („Idiotensichere Montage“)

Die Montage muss so einfach und fehlertolerant wie möglich sein.

### 4.1 Montageprinzipien

- Eindeutige Bauteilorientierung (kein Vertauschen möglich)
- Klare Montageabfolge
- Keine Spezialwerkzeuge notwendig

### 4.2 Unterstützende Maßnahmen

- **Kennzeichnungen**
  - z. B. Lasergravuren mit Positionsnummern oder Symbolen
- **Positionierungshilfen**
  - z. B. Abstandshalter
  - Anschläge
  - Führungen
- **Vorbereitete Verbindungen**
  - vorgebohrte Löcher
  - Sacklöcher
  - Steckverbindungen
- **Steck- oder Schraubsysteme**, die sich logisch selbst erklären

---

## 5. Modularität

- Alle Modelle müssen modular aufgebaut sein.
- Einzelne Module sollen:
  - unabhängig gefertigt
  - kombiniert
  - ausgetauscht
  werden können.
- Erweiterungen (z. B. zusätzliche Felder) müssen ohne Neukonstruktion möglich sein.

---

## 6. Erweiterbarkeit

- Neue Modellvarianten müssen:
  - auf der bestehenden Parametrik aufbauen
  - ohne grundlegende Strukturänderungen integrierbar sein
- Das System muss zukünftige Anforderungen (z. B. neue Materialien, neue Öffnungsarten) unterstützen.

---

## 7. Dokumentation & Nachvollziehbarkeit

- Alle Parameter müssen:
  - eindeutig benannt
  - logisch gruppiert
  - dokumentiert
  sein.
- Die Konstruktion soll für Dritte (z. B. neue Entwickler) verständlich und wartbar sein.

---
