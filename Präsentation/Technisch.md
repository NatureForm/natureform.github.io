# ThreeJS
## Generell:
> [!abstract] Overview
> Wir benutzen ThreeJS um im Web 3d Render anzeigen zu können. ThreeJS dient dabei als Abstraktion von WebGL.

## Backend
>[!info]
>**Generell:**
> - WebGL (Web Graphics Library) ist eine auf OpenGL ES bassierte Javascript API
> - Sie erlaubt es Grafiken direkt im Web zu rendern aufgrund  eines direkten Zugriffs auf die GPU
> 
> **Render-Pipeline:**
> 1. Input Assembler
> 	- Geometrische Daten (Punkte/Vertices, Farben, Textur-Koordinaten) werden in **Puffer** (Buffers) geladen und an die GPU geschickt.
> 2. Vertex Shader
> 	- Berechnet die Position der 3d Punkte auf der 2d Ausgabefläche mit hilfe von Matrix Multiplikationen (-> GPU Wichitig)
> 3. Rasterisierung
> 	- GPU verbindet Punkte zu Dreiecken und ermittelt welche Pixel von Dreiecken abgedeckt werden
> 4. Fragment Shader
> 	- Für jeden gerenderten Pixel läuft der Fragment Shader
> 	- Dieser berechnet Lichtberechnungen, Schatten und Texturen
> 5. Frame Buffer
> 	- Das Fertige Bild wird in den Frame buffer geschrieben
> 	- Dabei wird von WebGL der Z-Buffer (Tiefenbuffer) geprüft um sicherzustellen das die Objekte in der richtigen Reihenfolge gerendert werden
> 	- Das heißt, das Objekte im Hintergrund nicht vor Objekte im Vordergrund angezeigt werden


---

# CAD to ThreeJS
## Ansatz 1: GLTF + JSON
### Theorie:
>[!Idea]
>1.  Export GLB/GLTF (Dateiformate für 3d Modelle) und Parameter in einer JSON mittels eines Skriptes
> (ThreeJS supported leider nicht alle Datentypen, deshalb keine STEP dateien)
>2. Lade GLB in ThreeJS
>3. Mappe JSON Parameter auf das model
### Probleme:
>[!failure]
>**Mapping Schwierigkeiten**
>- GLB und GLTF beinhalten nur die Form an sich ohne Konzepte wie Seiten, Kanten, Bohrungen etc.
>- Mappen wäre dadurch schwierig aber konzeptuell mit Koordinaten nicht unmöglich
>- Die Kernlogic von Konzepten wie einer Bohrung etc. müsste auch manuell einprogrammiert werden, da sich sonst die Umformung nicht wie erwartet verhält (z.b. Lochbohrung ändert bei änderung des Lochdurchmessers nur einer der seiten)
>
>**Das Wirkliche Problem:**
>- Parameter bearbeiten nicht die Ecken und Kanten direkt bei Benutzung von GLB
>- Stattdessen kann man damit nur die Kanten bewegen (aka. das ganze model  strecken)
>- Dadurch zerfällt die Integrität des 3d Modells welches zu Fehlern und Visuellen Bugs führen kann
>- Es erlaubt auch nicht ein Parametrisches Modell zu erstellen da man keine neuen Elemente hinzufügen oder entfernen kann sondern nur strecken und stauchen


## Ansatz 2: Feature Tree Transpiler
### Theorie:
> [!Idea]
> **Grundlegend:**
> 1. Feature Tree:
> 	- Bei CAD gibt es etwas Namens Feature Tree
> 	- Ein Feature Tree ist nichts anderes als eine Reihenfolge an Arbeitsschritten (Bohrungen, Kürzungen etc.) welche ausgeführt werden muss um das endgültige CAD Modell zu erhalten
> 	- Dieser wird auch benutzt um z.B. Maschinenpfade zu erstellen
> 2. Manifold3d:
> 	- Manifold3d ist ein Open Source in C++ geschriebener CAD Kernel welcher es dir erlaubt im Web auf Client Seite CAD berechnungen durchzuführen
> 	- Ein CAD Kernel ist im Endeffekt das backend eines CAD Programmes, welcher die ganzen logic Elemente und Abhängigkeiten beinhaltet. 
> 	- Im fall von Manifold3d ist dieser sehr minimal (Difference, Intersection und Union) um eine sehr kleine Auswirkung auf die Performance zu haben.
> 	- In Manifold3d werden CAD modelle mittels code erstellt
> 	- Es ist wichtig das dies auf Client Side passiert um Web traffic, Serverkosten und Delay niedrig zu halten
> 
> **Umsetzung:**
> 1. Frontend:
> 	- Man kann mit Hilfe von Skripten den Feature Tree in den meisten CAD Systemen exportieren.
> 	- Da dieses nicht einheitlich zwischen Systemen ist muss man spezifisch für sein System ein solches Skript erstellen und kann dieses nicht generisch erstellen
> 2. Intermediate Representation:
> 	- Dieser ist meistens nicht direkt für Menschen leserlich und sollte daher für Einfachheit erstmal in eine IR (Intermediate Representation) übersetzt werden
> 3. Parser:
> 	- Man muss nun diese IR schritte in Manifold übersetzen. Dies ist leider nicht so einfach, da Manifold3d sehr minimal ist und Kommerzielle CAD Systeme um einiges mehr Features haben ( Vergleichbar mit X86 und Mips Assembly )
> 	- Heißt nicht das eins mächtiger ist als das andere, man kann halt nur nicht immer die schritte aus einem größeren CAD System innerhalb von einem Schritt in Manifold darstellen. Praktisch wie Pseudobefehle in X86, welche eigentlich mehr als einen Schritt beinhalten.
> 	- Deshalb müssen diese Konstrukte erstmal in Manifold gebaut werden um diese dann dort benutzen zu können ( in diesem Fall am einfachsten mit Funktionen )
> 4. Code Generierung:
> 	 - Jetzt muss nur noch diese Reihenfolge an Schritten aus der IR stück für stück in Manifold code mit Hilfe unserer Selbstgebauten Funktionen übersetzt werden um das Modell in Manifold wieder herzustellen
> 	 - Es muss auch eine Logik gebaut werden um Abhängigkeiten und Formeln von dem CAD Programm in Manifold wieder anwenden zu können ( evtl. durch einen DAG )
> 	 - Ein DAG  (Directed Acyclic Graph) ist ein graph in dem es keine Kreisläufe gibt

>[!example]
>**Marktbeispiel**
>Hoops Exchange bietet dieses Modell als Service an. Hier wurden Transpiler für die meisten großen CAD Systeme schon gebaut, welche dann in einen von Hoops erstellten CAD Kernel übersetzt werden. Da diese Übersetzung bei denen in beide Richtungen geht, kann man hiermit zum beispiel zwischen verschiedenen CAD Systemen wechseln ohne die Modelle alle manuell zu migrieren und auf das neue System einzustellen.

>[!Failure]
>Es wäre einfach viel zu Aufwändig einen solchen Transpiler zu entwickeln.
>Selbst wenn man Ihn entwickelt ist man an ein einzelnes CAD System gebunden da dieser nicht generisch entworfen werden kann.
>Hoops Exchange ist leider auch keine Lösung, da bei diesem die Preise bei 33k/Jahr Anfangen.

## Ansatz 3: Faking it
> [!idea]
> - Statt ein Parametrisches model zu benutzen faked man den Parametrischen Ansatz im Web Optisch.
> - Die Wichtigen Parametrischen Daten werden gesammelt und dann ans CAD Backend übergeben, ohne das komplette CAD model im Web aufbauen zu müssen

> [!Failure]
> Umgeht leider die Problemstellung die ich mir initial gesetzt habe 
> Ist nicht Generisch und nicht super Skalierbar

## Ansatz 4: Manifold3d
> [!Idea]
> CAD model direkt innerhalb von Manifold3d erstellen

>[!Failure]
>Genau wie der erste Ansatz umgeht dieser leider die Problemstellung
>**Aber** es ist Generisch und Skalierbar

## Fazit:
> [!tldr] Fazit
> Leider hatte ich nicht die Zeit oder Resourcen um dieses Problem auf die Gedachte art und Weise zu Lösen im Rahmen dieser Seminararbeit. Für den Rest des Projektes habe ich mich beschlossen denoch weiter zu machen und mit Ansatz 4: Manifold3d fortzufahren, da ich denke das auch mit dem nicht Erreichen der Problemstellung noch viel Wissenswertes aus diesem Thema Rauszuholen ist. Ich werde mich aber in Zukunft weiterhin mit dem Thema beschäftigen und eventuell als Bachelorarbeit einen solchen Transpiler bzw. ein Framework zum erstellen solcher Transpiler entwickeln.


---

# Optimierung
## Manifold3d
### Toggle
> [!abstract] Generell
> - Wir brauchen booleans um im Manifold code bestimmte Objekte zu rendern oder nicht zu rendern. 
> - Dies ist Wichtig um die Anzahl der Dreiecke im endgültigen Render zu verringern ohne die Integrität des models zu zerstören und damit die Performance zu verbessern. 
> - Bei manchen Gegenständen sind eventuell Performantere Alternativen fürs web Notwendig, zum beispiel bei den meisten Runden formen wie Füße.
### Batch Operations
> [!Abstract] Generell
> - Anstatt jede Operation einzeln zu machen (z.B. jede Bohrung einzeln abzuziehen) erstellt man ein Großes Assembly an Operationen die auf einmal ausgeführt werden (alle Bohrungen in einem Assembly)
> - Dadurch wird die anzahl der Subtraktionen von N auf 1 reduziert
> - Das hinzufügen (Union) von Gegenständen ist um einiges billiger als ein Subtract
### Compose vs Union
> [!Abstract] Generell
> - Union Operationen sind sehr mathematisch anspruchsvoll, da hier zwei Objekte zu einen zusammengeführt werden und ein komplett neues mesh (inkl intersections etc) berechnet werden muss
> - Compose gruppiert nur die schon vorhandenen meshes zusammen. Diese Operation ist mathematisch viel schneller und reduziert in unserem Use-Case sogar die Anzahl an Dreiecken (Nicht immer der fall)
### Symmetrie
>[!Abstract] Generell
>- da einige Teile des Modells Symmetrisch sind, kann man diese Komponenten Cachen und an allen stellen laden wo sie gebraucht werden.
### Shader
>[!Abstract] Generell
>- Das Detail das Verloren wird durch die Simplifizierung des Objektes reduzieren auch die Visuelle Qualität des Objektes.
>- Um manche Features wie z.B. die Grooves beizubehalten den Performance Overhead eines Detaillierten 3d Modells benutzen wir einen Shader um diese zu Faken.

> [!example] Anwendung
> #### **Alpha Clipping:**
> - Mit Alpha Clipping kann man der GPU sagen, welche Pixel eines Meshes nicht gezeigt werden sollen, dadurch kann man visuelle löcher im Mesh erzeugen
> - Die Idee die Lücken zwischen den Brettern damit zu erstellen wäre zwar sehr Performant, aber leider nicht Umsetzbar, da man dadurch in das Objekt reinschauen kann
> 
> #### **Parallax Occlusion Mapping (POM):**
> - Eine Methode um Tiefen auf flachen Oberflächen zu simulieren.
> - Benutzt Raymarching um die Texturkoorinaten basierend auf dem Blickwinkel der Kamera zu verschieben
> - Faked damit den 3 dimensionalen look der Grooves
> 
> #### **Vertex Shader:**
> - Mit einem Vertex Shader kann man die einzelnen Positionen der Kanten der Dreiecke bearbeiten ohne das model anzupassen. 
> - Die Idee damit die Grooves zu machen ist leider auch nicht möglich, da man dafür ein recht hochauflösendes model bräuchte und damit den ganzen sinn der Optimierung verliert.
> - **Aber!** Immer noch nützlich für unseren use case um anhand des Meshes die Richtung der Grooves zu bestimmen und die Position des Objektes zu tracken um zu wissen wo welcher Shader angewendet werden muss bzw. wie dieser angewendet werden muss
> 
> #### **Fragment Shader:**
> - Ist dafür Zuständig die Farbe der einzelnen Pixel eines meshes zu generieren. 
> - Wichtig für uns um die Parallax Occlusion Mapping logic auf das Objekt zu Projezieren und die Illusion zu erstellen

