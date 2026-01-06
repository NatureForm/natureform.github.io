# Vision Board – Referenzanalyse & Inspiration

**Status:** Recherche & Benchmarking  
**Thema:** Entwicklung eines Konfigurators für Balkonmöbel

Dieses Dokument dient als Inspirationsquelle und technische Referenz. Es analysiert bestehende Lösungen hinsichtlich User Experience (UX), technischer Machbarkeit und Design, um Anforderungen für das eigene Projekt abzuleiten.

---

## Referenz 1: Gridfinity Generator
**Link:** [https://gridfinitygenerator.com/en](https://gridfinitygenerator.com/en)

**Beschreibung:** Ein spezialisierter, webbasierter Generator für das "Gridfinity"-Ordnungssystem. Nutzer können Parameter wie Dimensionen, Stapelbarkeit und Fächeraufteilung definieren und erhalten ein druckbares 3D-Modell.

![Screenshot Gridfinity Generator](image-3.png)

**Analyse: Stärken**
* **Fokus auf Parameter:** Die Eingabemasken sind sauber von der 3D-Vorschau getrennt.
* **Performance:** Schnelles Rendering der Geometrie bei Parameteränderungen.
* **Zweckmäßigkeit:** Sehr "werkzeugorientiertes" Design, das effizient zum Ergebnis führt.

**Analyse: Schwächen**
* Visuell sehr nüchtern (Dark Mode Utility-Style), wenig emotionale Ansprache für Möbelkunden.

**Transfer-Potenzial (To-Do):**
* Übernahme der Logik für parametrische Größenänderungen (Breite/Höhe/Tiefe).
* Implementierung einer Echtzeit-Vorschau, die sich instantan aktualisiert.

---

## Referenz 2: Perplexing Labs Gridfinity
**Link:** [https://gridfinity.perplexinglabs.com/](https://gridfinity.perplexinglabs.com/)

**Beschreibung:** Eine alternative Implementierung eines Gridfinity-Konfigurators, der eine andere Herangehensweise an die UI/UX wählt und tiefere Konfigurationsmöglichkeiten bietet.

![Screenshot Perplexing Labs](image-4.png)

**Analyse: Stärken**
* **Umfangreiche Optionen:** Zeigt, wie viele Parameter (bis ins Detail) dem Nutzer angeboten werden können, ohne die Übersicht komplett zu verlieren.
* **Kamerasteuerung:** Intuitive Navigation im 3D-Raum.

**Analyse: Schwächen**
* Für Endkunden im Möbelbereich evtl. zu technisch ("Engineering-Look").

**Transfer-Potenzial (To-Do):**
* Evaluation der verwendeten Bibliotheken für das Handling komplexer Geometrie-Updates.
* Abwägung: Wie viele technische Details (z.B. Wandstärken) muss der Endkunde sehen?

---

## Referenz 3: Parametrische 3D-Assets (Boytchev)
**Link:** [https://boytchev.github.io/3d-assets/](https://boytchev.github.io/3d-assets/)

**Beschreibung:** Demonstration verschiedener geometrischer Objekte, die über Schieberegler in Echtzeit modifiziert werden können.

<video controls src="boytchev.github.io_3d-assets_online_bookshelf.html - Google Chrome 2025-09-02 23-15-39.mp4" title="Demo Boytchev"></video>

**Analyse: Stärken**
* **Intuitive Steuerung:** Parametrische Anpassungen sind sofort verständlich.
* **Tech-Demo:** Exzellenter Proof-of-Concept für browserbasierte Geometrieanpassung.

**Analyse: Schwächen**
* Reine Tech-Demo ohne UX-Konzept für Endanwender.
* Optik wirkt abstrakt und mathematisch.

**Transfer-Potenzial (To-Do):**
* Oberfläche (UI) muss emotionaler gestaltet werden (Materialien, Licht).
* Abstrakte Geometrie muss durch konkrete Möbelkomponenten ersetzt werden.
* Anbindung an eine Datenbank für Preiskalkulation notwendig.

---

## Referenz 4: CSG House (CodeSandbox)
**Link:** [https://codesandbox.io/p/sandbox/csg-house-y52tmt](https://codesandbox.io/p/sandbox/csg-house-y52tmt)

**Beschreibung:** Ein Architektur-Modell, das Constructive Solid Geometry (CSG) nutzt, um Fenster und Türen interaktiv im Baukörper zu verschieben (inkl. Sourcecode).

![CSG House Beispiel](image.png)

**Analyse: Stärken**
* **CSG-Einsatz:** Zeigt sehr gut, wie Boolesche Operationen (Subtraktion von Volumen) im Browser funktionieren.
* **Interaktivität:** Drag & Drop von Elementen am Modell.

**Analyse: Schwächen**
* Modell (Haus) nicht direkt auf Möbel übertragbar.
* Kein visuelles Design ("Rohzustand").

**Transfer-Potenzial (To-Do):**
* Adaption für Möbel: z.B. Ausschnitte für Kabelkanäle oder Griffe.
* Erweiterung um Export-Funktionen (Stücklisten/BOM).

---

## Referenz 5: Bang & Olufsen Konfigurator
**Link:** [https://www.bang-olufsen.com/de/int/composer?page=productselection](https://www.bang-olufsen.com/de/int/composer?page=productselection)

**Beschreibung:** High-End Konfigurator für Lautsprecher, der Design und Funktionalität nahtlos verbindet.

<video controls src="bang-olufsen Konfigurator.mp4" title="B&O Konfigurator"></video>

**Analyse: Stärken**
* **Premium UX:** Hochwertige Inszenierung, flüssige Animationen.
* **Branding:** Das Markenimage wird durch den Konfigurator gestärkt.

**Analyse: Schwächen**
* Hohe Entwicklungskomplexität und Kosten.

**Transfer-Potenzial (To-Do):**
* Zielsetzung für das "Look & Feel" der finalen Anwendung.
* Integration mit Backend-Systemen (Fertigung/Bestellung) ist hier essentiell gelöst.

---

## Referenz 6: Hulo Tischkonfigurator
**Link:** [https://hulo.be/configurator/](https://hulo.be/configurator/)

**Beschreibung:** Ein dedizierter Konfigurator für Tische. Technische Hintergründe werden hier diskutiert: [discourse.threejs.org](https://discourse.threejs.org/t/procedural-parametric-furniture-in-threejs/68760).

![Hulo Tischkonfigurator](image-1.png)

**Analyse: Stärken**
* **Domain-Fit:** Passt thematisch genau (Möbel).
* **Simplicity:** Einfache UX, Fokus auf Maße und Materialien.

**Analyse: Schwächen**
* Auf einen einzigen Möbeltyp (Tisch) beschränkt.
* Teilweise noch etwas technisch in der Anmutung.

**Transfer-Potenzial (To-Do):**
* Erweiterung des Konzepts auf modulare Balkonmöbel (Bänke, Stauraum).
* Optimierung der Usability für mobile Endgeräte.

---

## Referenz 7: IKEA Home Planner
**Link:** [https://www.ikea.com/de/de/planner/](https://www.ikea.com/de/de/planner/)

![IKEA Planer](image-2.png)

**Beschreibung:** Der Industriestandard für Raum- und Möbelplanung im Massenmarkt.

**Analyse: Stärken**
* **Bekanntheit:** Nutzer kennen und verstehen das Bedienkonzept (geringe Lernkurve).
* **E-Commerce:** Nahtlose Integration in den Warenkorb.

**Analyse: Schwächen**
* Feature-Overload: Für einen Nischen-Konfigurator oft zu mächtig und komplex.

**Transfer-Potenzial (To-Do):**
* **Reduktion:** Fokus auf Individualisierung statt Massenplanung.
* Spezialisierung auf den Kontext "Balkon" (Wetterfestigkeit, begrenzter Platz).

---
## Referenz 8: Thuma Beds
**Link:** [https://www.ikea.com/de/de/planner/](https://www.ikea.com/de/de/planner/)

![[Pasted image 20260106142848.png]]

**Beschreibung:** Bett Konfigurator mit einfacher Montage

---

### Technische Basis
Die meisten der analysierten Konfiguratoren basieren auf **three.js** (WebGL). Aus diesem Grund erfolgt im Dokument [Technologie-Stack: three.js](./Software/three.js.md) eine detaillierte Betrachtung des Frameworks.