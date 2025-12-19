## Marktanalyse Ikea
### Welche Auswirkungen hat der Ikea Konfigurator 
#### Annahme
- **Psychologisch**: Erhöht Kaufbereitschaft durch Integration des  Kunden in den Designprozess (Ikea Effect)
- **Kommerziell**: Vereinfacht den Kaufprozess und erhöht Produktangebot.
- **Operational**: Senkt Arbeit für Ikea angestellte und senkt dadurch ausgaben.
- **Informativ:** Bietet Datensammlung für "Demand Sensing" Algorithmen.

#### Beweis (TODO: Ausarbeiten)
- **Psychologisch**: 
	- [[Ikea#**II. The Configurator as IKEA's Core Engine for Value Co-Creation**|Co-Creation]]
	- [[Ikea#**III. The Psychological Architecture of Digital Co-Creation The "IKEA Effect"**|Ikea Effect]] 
- **Kommerziell:** 
	- [[Ikea#**IV. Technological Evolution From Blank Pages to AI-Driven Mixed Reality**|Blank Page]]
	- [[Ikea#**Phase 4 The AI-Powered Synthesis (IKEA Kreativ)**|Ikea Kreativ]]
- **Operational:** 
	- [[Ikea#**The Planner as a Labor Optimization Tool**|Arbeit Auslagerung]]
- **Informativ:** 
	- [[Ikea#**The Planner as a Data-Capture Engine**|Daten Sammlung]]

### Probleme  (TODO: Ausarbeiten)
- [[Ikea#**Technical Failures The "Last Foot" Problem**|Retouren durch Genauigkeit]]
- [[Ikea#**User-Adoption Barriers The "Touch and Trust" Deficit**|Touch and Trust Defizite]]

### Verkaufsdaten  (TODO: Ausarbeiten)
- [[Ikea#**Direct Sales, Conversion, and Pipeline**|Conversion Rate]]
- [[Ikea#**Customer Confidence and Risk Reduction**|Confidence]]
- [[Ikea#**Customer Satisfaction The Finnish Market Anomaly**|Zufriedenheit]]

## Prototypenentwicklung
>[!Summary]
Ich habe mich entschieden einen Prototypen für diese Art von Technologie zu entwickeln um die Möglichkeiten und Schwierigkeiten die diese bietet zu erforschen.
### Prototypendefinition
>[!Definition]
> Der Prototyp soll folgende Sachen erreichen:
> - Ein Workflow von CAD zu Web mittels ThreeJS
> - Parametrische Bearbeitung der CAD Modelle innerhalb der WebApp mittels der CAD Parameter
> - Export der Parametrischen daten um mittels CAD sachen wie BOM und Maschinenschritte zu erstellen
> - Hochautomatisierte Architektur -> Wenige bis garkeine Menschliche Interaktion erforderlich
> 
> Konkreter Prototyp:
> - Ein online Konfigurator für Parametrische Gartenmöbel 

### Vorteile
- Hohe Skalierbarkeit und Flexibilität
- Hohe Kundenintegration
- Größerer Produktkatalog und Individualität aufgrund von Anpassbarkeit
- Gesenkte Prozesskosten

### Nachteile
- Hohe kosten und Komplexität Up-Front
- Nicht jeder will die Wahl haben es selber zu Konfigurieren
- Unternehmensstruktur muss sich mit dem Konzept im Hinterkopf entwickeln (Kann Fortschritt verlangsamen)
- Schwierig in schon vorhandenen Unternehmensstrukturen zu Implementieren aufgrund von Tech-Debt

### Preemptive Technische Schwierigkeiten
- Komplexes System einfach für den Kunden gestalten
- CAD Software hat viele Hürden (z.B. Proprietäre Dateiformate)
- Performance im Web