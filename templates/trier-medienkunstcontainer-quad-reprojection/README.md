# Medienkunstcontainer – TouchDesigner-Boilerplate

Vorlage, um Inhalte für den Medienkunstcontainer zu gestalten, aus Sicht der Betrachtenden zu prüfen und als fertigen Video-Strip für das Mapping zu exportieren.

Das Repository ist ein **Projekt-Template**, keine einbindbare `.tox`: Ordner kopieren oder klonen, `medienkunstcontainer.toe` öffnen und darin arbeiten.

Der Container ist ein 20-ft-Seecontainer mit einer verglasten Längsseite und einer verglasten Stirnseite. Wer davor steht, soll durch beide Fenster in einen scheinbar dreidimensionalen Raum blicken. Das gelingt, weil der Innenraum aus genau einem Blickpunkt, dem **Sweet Spot**, auf die Fensterflächen projiziert wird.

Das Projekt ist damit auch ein kompaktes, praxisnahes Beispiel für die **Quad-Reprojection-Technik** in TouchDesigner (Anamorphose bzw. Forced Perspective): Kameras am Blickpunkt rendern über die Camera-COMP-Parameter *Quad Reproject* direkt auf beliebig liegende Flächen, ohne nachträgliches Corner-Pinning. Wie das hier umgesetzt ist, steht unter [So funktioniert die Reprojektion](#so-funktioniert-die-reprojektion).

```
Gestalten (design) ──► Vorschau aus dem Sweet Spot (preview)
        │
        └────────────► Strip 3850×1100 (strip) ──┬─► HAP-Video (render) ──► MadMapper
                                                   └─► live per Syphon/Spout ──► MadMapper
```

## Voraussetzungen

- **TouchDesigner** 2025 (erstellt mit Build 2025.31550)
- **Lizenz ohne Auflösungsgrenze**, zum Beispiel Educational, Commercial oder Pro. TouchDesigner Non-Commercial ist auf 1280×1280 Pixel begrenzt und reicht für den 3850 Pixel breiten Strip nicht aus. Getestet wurde die Vorlage bisher nur mit einer Pro-Lizenz.

## Schnellstart

1. `medienkunstcontainer.toe` in TouchDesigner öffnen.
2. Die grüne Base `design` öffnen. Dort wird gestaltet, eine kurze `anleitung` liegt direkt im Netzwerk.
3. Einen eigenen TOP an `surface_backwall` anschließen oder eine Geometry COMP in `objects` legen.
4. Das Ergebnis in `preview` ansehen: die Kachel oder den Operator `null_preview`.
5. Das Video exportieren, siehe [Export](#export).

## Aufbau

Alle Bases liegen in `/project1`.

| Base | Aufgabe | Ändern? |
|---|---|---|
| `config` | Maße, Sweet Spot, Auflösung, Codec. Die Kachel zeigt die wichtigsten Werte live. | bei Bedarf |
| `design` | Arbeitsbereich: Boden, Wände und eigene Objekte | **ja, hier wird gestaltet** |
| `preview` | Ansicht aus dem Sweet Spot mit Fensterflächen, Labels und Container-Outline | nein |
| `strip` | Quad Reprojection beider Fenster und Zusammensetzen zum Strip. Enthält auch die Geometrie der Fensterflächen und den Live-Ausgang `syphonspout_strip`. | nein |
| `render` | Video-Export mit dem Node `moviefileout_strip` | nur dessen `File` und `Length` |

`strip` und `render` funktionieren ohne `preview`. Umgekehrt braucht `preview` die Fensterflächen aus `strip`.

## Gestalten

### Flächen (2D)

Jede sichtbare Innenfläche hat einen eigenen Eingang in `design`:

| Eingang | Fläche | Testfarbe |
|---|---|---|
| `surface_floor` | Boden | Blau |
| `surface_backwall` | lange Rückwand | Grün |
| `surface_left_backwall` | linke Stirnwand | Magenta |

Einen beliebigen TOP an den Eingang anschließen. Er ersetzt das Testbild und wird unverzerrt eingepasst. Passt das Seitenverhältnis nicht zur Fläche, entstehen schwarze Balken.

Die Testfarben und Labels sind normale Inhalte: Solange an einem Eingang noch das Testbild hängt, erscheint es auch im Strip und im Export.

### Raum (3D)

Geometry COMPs und Lights in die Base `design/objects` legen. Sie erscheinen automatisch in Vorschau und Strip.

- Eine neu angelegte Geometry COMP enthält standardmäßig einen Torus, der ersetzt oder gelöscht werden sollte.
- Für beleuchtete Materialien (zum Beispiel Phong) wird ein Light in `objects` gebraucht. Die Flächen selbst sind unbeleuchtet.
- Sehr große Polygone unter flachem Blickwinkel können bei Quad Reprojection Artefakte erzeugen. In dem Fall die Geometrie feiner unterteilen.

### Koordinaten und Grenzen

Einheit ist Millimeter, Y zeigt nach oben, der Ursprung liegt in der Mitte des Containers.

| Achse | Bereich | Bedeutung |
|---|---|---|
| x | −3029 bis +3029 | Länge, +x ist die verglaste Stirnseite |
| y | −1295,5 bis +1295,5 | Höhe, −1295,5 ist der Boden |
| z | −1219 bis +1219 | Breite, +z ist die verglaste Längsseite |

Innerhalb dieser Grenzen ist die Gestaltung frei. Die Grenzen werden nicht erzwungen, die Container-Outline in der Vorschau dient als Orientierung. Objekte außerhalb werden gerendert, sobald sie durch ein Fenster sichtbar sind. Vor den Fensterflächen (z > 1219 oder x > 3029) sollte nichts stehen, weil dort im echten Container kein Raum zum Bespielen ist.

## Vorschau lesen

| Element | Darstellung |
|---|---|
| Container | weiße Outline |
| `front wall` (lange Fensterfläche) | roter Rahmen, transparent |
| `right frontwall` (kurze Fensterfläche) | gelber Rahmen, transparent |

Outline, Rahmen und ihre Labels sind nur Orientierungshilfen und erscheinen nicht im Export. Die Farben stammen aus der Mapping-Vorlage.

## Export

Exportiert wird von Hand mit dem Node `moviefileout_strip` in der Base `render`. Die gleiche Anleitung liegt dort auch als `anleitung` im Netzwerk.

Vor dem Start in `moviefileout_strip` prüfen:

| Parameter | Bedeutung |
|---|---|
| File | Zielpfad, relativ zum Projektordner, voreingestellt `renders/strip.mov`. Der Ordner wird bei Bedarf angelegt. |
| Length | Länge in Frames: Sekunden × 60, voreingestellt 600 (10 Sekunden) |

**Achtung:** Eine vorhandene Datei wird überschrieben. Wähle deshalb für jeden Export einen neuen Namen.

1. Das **Realtime**-Flag oben in TouchDesigner ausschalten, damit kein Frame fehlt.
2. Die Timeline auf den Anfang setzen, damit Animationen von vorn beginnen.
3. In `moviefileout_strip` **Record** einschalten. Nach der eingestellten Länge schreibt der Node nicht weiter.
4. **Record** wieder ausschalten und **Realtime** wieder einschalten.

Wird Record vor Ablauf der Länge ausgeschaltet, endet das Video an dieser Stelle.

- **Format:** HAP in `.mov`, 3850×1100, ohne Alpha, in der Bildrate des Projekts (voreingestellt 60 fps).
- **Speicherplatz:** HAP erzeugt große Dateien. Schon flache Testfarben ergeben rund 7,5 MB pro Sekunde, echte Inhalte deutlich mehr.

## Live-Ausgabe (Syphon/Spout)

Der Strip wird zusätzlich live über `strip/syphonspout_strip` ausgegeben, auf macOS per Syphon, auf Windows per Spout. In MadMapper als Quelle den Sender **Medienkunstcontainer** wählen (3850×1100).

Das ist vor allem zum Einmessen vor Ort gedacht: Änderungen am Sweet Spot oder am Inhalt sind sofort auf dem Container zu sehen, ohne neu zu exportieren. Wird der Ausgang nicht gebraucht, kann an `syphonspout_strip` der Parameter `Active` ausgeschaltet werden.

## Sweet Spot vor Ort einmessen

Der Container wird an wechselnden Orten aufgestellt. Der voreingestellte Sweet Spot (5500 / 5500 / 1650) ist deshalb nur ein Startwert und muss an jedem Standort neu gemessen und angepasst werden.

1. TouchDesigner am Container starten und den Strip per Syphon/Spout an MadMapper geben, siehe oben.
2. Den Standort festlegen, von dem aus das Publikum hauptsächlich schaut, zum Beispiel am Weg oder am Eingang.
3. Von der Containerecke zwischen den beiden Fenstern aus messen (Maßband oder Laser-Entfernungsmesser), jeweils rechtwinklig zur Containerwand:
   - **a**: wie weit der Standort seitlich über die verglaste Stirnseite hinausliegt (negativ, wenn er noch neben der Längsseite liegt),
   - **b**: wie weit der Standort vor der verglasten Längsseite liegt,
   - **h**: Augenhöhe über der Containerunterkante.
4. In `config` unter **Sweet Spot Viewer** eintragen: Wert 1 = 3029 + a, Wert 2 = 1219 + b, Wert 3 = h (alles in mm). Die Werte zählen ab der Containermitte, deshalb die halbe Länge bzw. Breite dazu. Das Bild auf dem Container ändert sich sofort.
5. Am Standort prüfen, ob Kanten über die Ecke hinweg stimmig wirken, und die Werte bei Bedarf nachstellen. Am einfachsten zu zweit: eine Person schaut, die andere ändert die Werte.
6. Das Projekt speichern und die Videos mit den neuen Werten exportieren. Ein Export passt nur zu dem Sweet Spot, mit dem er gerendert wurde.

Beispiel: Der Startwert 5500 / 5500 / 1650 entspricht einem Standort 2471 mm seitlich hinter der Stirnseite, 4281 mm vor der Längsseite und 1650 mm Augenhöhe. Die Kachel `config` zeigt die aktuell verwendeten Werte. Den Bildausschnitt in `preview` muss man danach gegebenenfalls nachstellen, siehe [Konfiguration](#konfiguration).

## Konfiguration

Die Parameter liegen auf der Base `config`:

| Parameter | Wert | Bedeutung |
|---|---|---|
| Sweet Spot Viewer | 5500 / 5500 / 1650 | x, z und Augenhöhe über der Containerunterkante in mm. Startwert, vor Ort neu einmessen, siehe [Sweet Spot vor Ort einmessen](#sweet-spot-vor-ort-einmessen) |
| Resolution Output Video | 3850 × 1100 | Größe des Strips |
| Strip Split X | 2406 | Pixel, an dem der Strip zwischen Front und Stirnseite geteilt ist |
| Video Codec | HAP | Codec des Exports |
| Amount of Projectors | 3 | Information |

Container- und Fenstermaße stehen in den Constant CHOPs `container_size`, `window_left` und `window_right` innerhalb von `config`. Sie sind in Millimetern angelegt, alle Flächen folgen ihnen automatisch. Nur die Lage der Front-Fensterfläche nutzt zusätzlich einen festen Randabstand von 276 mm (in `strip/transform_front_wall`).

Die Vorschau hat einen fest eingestellten Bildausschnitt (`fov`, `winx`, `winy` an `preview/sweet_spot_viewer`). Wird der Sweet Spot verschoben, stimmt der Strip weiterhin, der Ausschnitt der Vorschau muss aber eventuell nachgestellt werden.

## So funktioniert die Reprojektion

Für jede Fensterfläche steht in `strip` eine Kamera am Sweet Spot. Über die Camera-COMP-Parameter **Quad Reproject SOP** und **Quad Reproject Points** rendert sie genau den Ausschnitt des Raums, den man durch dieses Fenster sieht, und füllt damit das gesamte Bild. Es wird also direkt in der Zielauflösung gerendert, ohne nachträgliches Corner-Pinning.

| Strip-Bereich | Fläche | Maße real | Pixel |
|---|---|---|---|
| links | `front_wall` | 5539 × 2325 mm | 2406 × 1100 |
| rechts | `right_frontwall` | 2110 × 2325 mm | 1444 × 1100 |

### Warum sieht der Strip verzerrt aus?

Die Pixel pro Millimeter unterscheiden sich zwischen den beiden Bereichen und zur Höhe. Im flachen Strip wirkt das Bild deshalb gestreckt. Beim Mapping auf die Fenster hebt sich das wieder auf. Beurteilt wird das Ergebnis deshalb in `preview`, nicht im Strip.

### Warum ist an der Teilung eine Stufe?

Das ist kein Fehler. Zwischen den beiden Fenstern liegt die Containerecke: Die Front endet 243 mm vor der Ecke, die Stirnseite beginnt 164 mm danach. Aus dem Sweet Spot verdeckt diese Ecke rund 525 mm der Rückwand. Im Strip stoßen beide Bereiche direkt aneinander, deshalb springt das Bild dort. Am Container sitzt der Sprung genau an der Ecke, und aus dem Sweet Spot passen beide Fenster zusammen.

Die beiden 47 mm breiten Stege innerhalb der Front werden dagegen mitbespielt. Sie sind in den 5539 mm enthalten (3 × 1815 + 2 × 47).

## Bekannte Grenzen

- Die perspektivische Wirkung stimmt nur aus dem Sweet Spot.
- Die Wiedergabe des Exports und des Syphon/Spout-Ausgangs in MadMapper ist noch nicht getestet.
- Die schwarze Schrift `right frontwall` ist in der Vorschau vor dunklen Motiven schlecht lesbar.
- Eine Warnung bei Objekten außerhalb der Grenzen gibt es nicht.

## Unterlagen im Ordner `assets`

| Datei | Inhalt |
|---|---|
| `container-masse.pdf` | Maßzeichnung des Containers |
| `container-technische-ausstattung.docx` | Projektoren und Mapping-Software (MadMapper v5) |
| `vorlage-mapping-madmapper-ansicht-außen.png` | Mapping-Vorlage in 3850×1100, Ansicht von außen |

## Lizenz

Frei nutzbar für alle unter der [MIT-Lizenz](LICENSE): verwenden, verändern und weitergeben, auch kommerziell. Nur der Lizenzhinweis muss erhalten bleiben.

Wenn dir die Vorlage hilft und du etwas zurückgeben möchtest, freue ich mich über einen Kaffee auf [Ko-fi](https://ko-fi.com/axelzander).
