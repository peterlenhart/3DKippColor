# DigiKipp 3D - Rastersystem & Geometrie Referenz

## Koordinatensystem (Three.js)

**Achsen-Orientierung:**
- **Y-Achse:** Vertikal (nach oben positiv)
- **X-Achse:** Horizontal Ost-West (+X = Ost, -X = West)
- **Z-Achse:** Tiefe Nord-Süd (+Z = Süd, -Z = Nord)

**Himmelsrichtungen:**
```
        Nord (-Z)
    West (-X) ║ Ost (+X)
         Süd (+Z)
```

---

## Projektionsebenen nach Monge

**Drei Hauptebenen:**
- **π1 (Grundriss):** XZ-Ebene bei Y=0 - horizontale Tischfläche
- **π2 (Aufriss):** XY-Ebene bei Z=0 - senkrechte Front-/Rückansicht
- **π3 (Seitenriss):** YZ-Ebene bei X=0 - senkrechte Seitenansicht

**Risskanten (Schnittlinien):**
- **x12:** X-Achse (Schnitt π1 ∩ π2) bei Z=0
- **y23:** Y-Achse (Schnitt π2 ∩ π3) bei X=0

---

## Quadranten-System im Spielbereich

**WICHTIG: Spielbereich-Mittelpunkt verschoben um Z = 0.1105**

**4 Quadranten (Würfel-Positionen):**

```
        Nord (Z < 0.1105)
    Q0      |      Q1
   X<0,Z<M  |  X>0,Z<M
   NW       |      NO
------------+------------ Zentrum Z=0.1105
    Q3      |      Q2
   X<0,Z>M  |  X>0,Z>M
   SW       |      SO
     Süd (Z > 0.1105)
```

**Quadranten-Definitionen:**
- **Q0 (Nordwest):** X<0, Z<0.1105 - Ecke bei (-3 Zellen, -3 Zellen rel.)
- **Q1 (Nordost):** X>0, Z<0.1105 - Ecke bei (+3 Zellen, -3 Zellen rel.)
- **Q2 (Südost):** X>0, Z>0.1105 - Ecke bei (+3 Zellen, +3 Zellen rel.)
- **Q3 (Südwest):** X<0, Z>0.1105 - Ecke bei (-3 Zellen, +3 Zellen rel.) - **STARTPOSITION**

**Benachbarte Quadranten (für Tap-Counter Anzeige):**
```javascript
const ADJACENT_Q = { 0: [3, 1], 1: [0, 2], 2: [1, 3], 3: [0, 2] };
```

---

## Rasterzellen-System

**Design-Auflösung:** 360×720 Pixel (logisch, nicht physisch)

**Grid-Einteilung:**
- Horizontal: 8 Spalten (A–H)
- Vertikal: 16 Zeilen (1–16)
- **1 Rasterzelle = 45px**

**Maßstab & Umrechnungen:**

| Einheit | Wert | Umrechnung |
|---------|------|------------|
| 1 Rasterzelle | 45px | 23.33mm (real) |
| 1 Rasterzelle | 0.15625 Three.js-Einheiten | - |
| 3 Rasterzellen | 135px | 70mm (real) |
| 3 Rasterzellen | 0.46875 Three.js-Einheiten | Würfelkantenlänge |
| 6 Rasterzellen | 270px | 140mm (real) |
| 6 Rasterzellen | 0.9375 Three.js-Einheiten | Spielfeld-Innenmaß |
| 8 Rasterzellen | 360px | 187mm (real) |
| 8 Rasterzellen | 1.25 Three.js-Einheiten | Spielfeld-Außenmaß |
| 16 Rasterzellen | 720px | 373mm (real) |
| 16 Rasterzellen | 2.5 Three.js-Einheiten | Volle Grid-Höhe |

**Wichtige Formel:**
```
720px = 2.5 Three.js-Einheiten
1px = 2.5/720 = 0.003472 Einheiten
1 Zelle (45px) = 45 × 0.003472 = 0.15625 Einheiten
```

---

## Wichtige Höhen & Positionen

**Y-Koordinaten (Höhen):**
- `Y = 0` → π1 (Grundriss-Ebene, Tischfläche)
- `Y = 0.001` → Grid auf π1 (minimal darüber, Z-Fighting vermeiden)
- `Y = 0.234375` → 1.5 Zellen (Wandmitte bei 3 Zellen Höhe)
- `Y = 0.46875` → 3 Zellen (Oberkante Wände)
- `Y = 2.143` → 13.71 Zellen = 320mm = Augenhöhe Spieler

**Z-Koordinaten (Tiefe) - MIT VERSCHIEBUNG:**
- `Z = 0` → x12 (Risskante, X-Achse) - **NICHT Spielbereich-Mitte!**
- `Z = 0.1105` → **Spielbereich-Mittelpunkt** (verschoben nach Süden)
- `Z = 0.109375` → 0.7 Zellen absolut = Nord-Pivot-Linie im Spielbereich
- `Z = 0.343125` → 2.2 Zellen absolut = Ost/West-Pivot-Linie (Spielbereich-Mitte Z)
- `Z = -0.359375` → Nord-Wand Innenkante (0.1105 - 0.46875)
- `Z = +0.580375` → Süd-Wand Innenkante (0.1105 + 0.46875)
- `Z = -0.376125` → Nordwand-Zentrum
- `Z = +0.597125` → Südwand-Zentrum

**X-Koordinaten (Breite):**
- `X = 0` → y23 (Risskante, Mittellinie)
- `X = ±0.234375` → ±1.5 Zellen
- `X = ±0.46875` → ±3 Zellen (West-/Ost-Wandkanten-Innenmaß)
- `X = ±0.48550` → ±3.1 Zellen (Seitenwand-Positionen)

**KRITISCH:** Spielbereich ist **nach Süden verschoben um 0.1105 Einheiten (≈0.7 Zellen)**

---

## Würfel-Spezifikation

**Größe:** 3×3×3 Zellen = 70×70×70mm (real)

**In Three.js:**
```javascript
const cubeSize = 0.46875; // 3 Zellen
const offset = cubeSize / 2; // 0.234375 = 1.5 Zellen
```

**Startposition (Quadrant 3 - Südwest):**
```javascript
cube.position.set(
  -offset,              // X: -1.5 Zellen (West)
  offset,               // Y: +1.5 Zellen (auf π1 stehend)
  0.109375 + offset     // Z: 0.7 + 1.5 = 2.2 Zellen absolut
);
// = (-0.234375, 0.234375, 0.343125)
```

**Würfel-Zentrum bei Start:**
- X = -0.234375 (-1.5 Zellen - West)
- Y = 0.234375 (+1.5 Zellen - auf π1 stehend)
- Z = 0.343125 (+2.2 Zellen absolut = +1.5 Zellen relativ zum Spielbereich-Zentrum)

**Nord-Bodenkante des Würfels:**
- Liegt auf Z = 0.109375 (Nord-Pivot-Linie)
- Diese Linie ist 0.7 Zellen absolut = Nord-seitig im Spielbereich

---

## Wände-Spezifikation

**Nord-/Süd-Wände:**
- Breite: 6 Zellen = 0.9375 Einheiten
- Höhe: 3 Zellen = 0.46875 Einheiten
- Dicke: 5mm = 0.0335 Einheiten
- Material: Silbergrau (0xc0c0c0), metallic

**West-/Ost-Wände:**
- Tiefe: 6.45 Zellen = 1.00675 Einheiten (schließt Nord-/Süd-Wanddicken ein)
- Höhe: 3 Zellen = 0.46875 Einheiten
- Dicke: 5mm = 0.0335 Einheiten

**Wandpositionen (mit Süd-Verschiebung):**
```javascript
// Südwand
position: (0, 0.234375, 0.597125)
// Mitte bei Z = 0.597125

// Nordwand
position: (0, 0.234375, -0.376125)
// Mitte bei Z = -0.376125

// Westwand
position: (-0.48550, 0.234375, 0.1105)
// Bei X = -0.48550, zentriert auf Spielbereich-Mitte Z = 0.1105

// Ostwand
position: (0.48550, 0.234375, 0.1105)
// Bei X = +0.48550, zentriert auf Spielbereich-Mitte Z = 0.1105
```

**Innenkanten (Spielbereich 6×6 Zellen):**
- Nord: Z = -0.359375 (0.1105 - 0.46875)
- Süd: Z = +0.580375 (0.1105 + 0.46875)
- West: X = -0.46875 (-3 Zellen)
- Ost: X = +0.46875 (+3 Zellen)

**Spielbereich-Zentrum:**
- X = 0
- Z = 0.1105 (verschoben nach Süden!)

---

## Holz-Textur / Tisch-Material

**Tisch-Geometrie:**
```javascript
const tableGeometry = new THREE.PlaneGeometry(5, 5);
```

**Holz-Textur:**
- Format: JPEG Base64-embedded (30KB, 512×512 resized from 1024×1536)
- Qualität: 85
- Variable: `woodBase64`

**Tisch-Material:**
```javascript
const tableMaterial = new THREE.MeshStandardMaterial({
  map: woodTexture,
  roughness: 0.8,
  metalness: 0.1
});
```

**Tisch-Position:**
```javascript
table.rotation.x = -Math.PI/2; // horizontal (π1)
table.position.y = 0;          // auf π1
table.receiveShadow = true;
```

**WICHTIG:** Die Holz-Textur liegt aktuell auf dem gesamten 5×5 table. Für bessere Darstellung sollte sie nur auf dem 6×6 Spielfeld-Innenbereich (0.9375 × 0.9375) angewendet werden.

---

## Kärtchen-System

**Kärtchen-Maße:**
```javascript
const cardWidth = 0.15625;   // 1 Zelle
const cardHeight = 0.3125;   // 2 Zellen
```

**Positionierung:**
```javascript
const cardY = 0.003;                             // auf π1 (0.001 + 0.002)
const cardZ = 0.890625;                          // 4 Zeilen südlich + Kartenmitte
// cardZ = 0.109375 + 4 × 0.15625 + 0.15625
```

**Verteilung:**
- 6 Karten auf 8 Zellen verteilt (totalCardsWidth = 8 × 0.15625 = 1.25)
- Gap zwischen Karten: `(1.25 - 6 × cardWidth) / 5`
- Start X: `-0.625 + 0.078125` (cardWidth/2)
- X-Positionen: `cardStartX + i × (cardWidth + cardGap)` für i=0..5

**Canvas-Textur:**
- Größe: 85×170 Pixel
- Material: `MeshStandardMaterial` mit `DoubleSide`
- Rotation: `-Math.PI/2` (horizontal auf π1)

**Kärtchen-Darstellung:**
- Oberfläche: WebP-Texturen pro Farbe (z.B. `blauzielhit.webp`, `gruenzielhit.webp`, etc.)
- Zahl: Bold 56px Arial, Farbe `#2a1810`, zentriert bei y=42px
- 6/9-Unterscheidung: Punkt nach der Zahl (`6.` bzw. `9.`)

**Schatten-Gradient über Kärtchen:**
```javascript
shadowGradPlane.position.set(0, 0.004, 0.890625); // auf cardZ
// Breite: 1.25 (8 Zellen), Höhe: 0.3125 (2 Zellen)
// Gradient: Nord schwarz (0.5 opacity) → Mitte/Süd transparent
```

---

## Verdecktes Kippziel (3D Mesh)

**Funktion:** Zeigt das nächste aufzudeckende Kippziel als verdecktes Kärtchen in der Kärtchenreihe.

```javascript
const verdecktesMesh = new THREE.Mesh(verdecktesGeo, verdecktesMat);
verdecktesMesh.rotation.x = -Math.PI / 2;
verdecktesMesh.position.set(cardStartX, cardY + 0.001, cardZ);
```

**Textur:** `leernohit.webp` (leeres Kärtchen ohne Treffer-Markierung)
**Canvas:** 85×170px, CanvasTexture
**Zustand:** `verdecktesMesh.userData.revealed` (true/false)

**Positionierung:** Dynamisch auf Position des nächsten Kärtchens (cardStartX + activeCardIdx × Abstand)

---

## Schwarzer Rahmen (Erweitert)

**Rahmen-Dimensionen:**
```javascript
const frameHeight = 0.46875;    // 3 Zellen über π1
const frameThickness = 0.02;    // Minimale Dicke in Y
const frameWidth = 0.15625;     // 1 Zelle breit
```

**Rahmen-Material:**
```javascript
frameMaterial: MeshStandardMaterial({
  color: 0x000000,     // Schwarz
  roughness: 0.9,
  metalness: 0.1
})
```

**Rahmen-Positionen:**
```javascript
// Nord-Rahmen
position: (0, 0.46875, -0.437500)

// Süd-Rahmen
position: (0, 0.46875, 0.658000)

// West-Rahmen (6 Zellen lang = 0.9375)
position: (-0.546875, 0.46875, 0.1105)

// Ost-Rahmen (6 Zellen lang = 0.9375)
position: (0.546875, 0.46875, 0.1105)
```

**Rahmen-Kanten (Linien):**
- Breite: 4px = `4 × (2.5/720)` = 0.01388 Einheiten
- Dicke: 0.002
- Y-Position: `frameHeight + frameThickness/2 + 0.0005` = 0.4788
- Material: Silbergrau (0x606060)

**Äußere Perimeter:**
- Nord: Z = -0.515625 (wallInnerNorth - frameWidth)
- Süd: Z = 0.736125 (wallInnerSouth + frameWidth)
- West: X = -0.625 (wallInnerWest - frameWidth)
- Ost: X = 0.625 (wallInnerEast + frameWidth)

**Innere Kanten:**
- Nord: Z = -0.359375 (wallInnerNorth)
- Süd: Z = 0.579875 (wallInnerSouth)
- West: X = -0.46875 (wallInnerWest)
- Ost: X = 0.46875 (wallInnerEast)

---

## Farbquadrate auf Rahmen

**Farben:**
```javascript
COLORS = {
  violett: 0xa41b85,
  blau:    0x006bb3,
  gruen:   0x00a652,
  orange:  0xe67814
}
```

**Quadrat-Größe:**
- `colorSqSize = 0.15625 × (42/45)` = 0.14583 Einheiten (42px)

**Position:**
- Y: `colorY = frameHeight + frameThickness/2 + 0.001` = 0.4798
- Dicke: 0.002

**Spalten X-Zentren (für A-H):**
```javascript
colX(letter) = -0.625 + cellSize/2 + idx × cellSize
// A=0: -0.54688
// B=1: -0.39063
// C=2: -0.23438
// D=3: -0.07813
// E=4: +0.07813
// F=5: +0.23438
// G=6: +0.39063
// H=7: +0.54688
```

**Zeilen Z-Zentren (für Zeile 3-10):**
```javascript
rowZ(row) = wallInnerNorth + (row - 4) × cellSize + cellSize/2
// Zeile 3: -0.43750
// Zeile 4: -0.28125
// Zeile 5: -0.12500
// Zeile 6: +0.03125
// Zeile 7: +0.18750
// Zeile 8: +0.34375
// Zeile 9: +0.50000
// Zeile 10: +0.65800
```

**Farbquadrat-Verteilung:**

*Nord-Rahmen (Zeile 3):*
- C: Grün, D: Orange, E: Orange, F: Blau
- Z-Position: `-0.437500` (wallInnerNorth - frameWidth/2)

*Süd-Rahmen (Zeile 10):*
- C: Grün, D: Violett, E: Violett, F: Blau
- Z-Position: `0.658000` (wallInnerSouth + frameWidth/2)

*West-Rahmen (Spalte A):*
- Zeile 5: Violett, 6: Blau, 7: Blau, 8: Orange
- X-Position: `-0.546875` (wallInnerWest - frameWidth/2)

*Ost-Rahmen (Spalte H):*
- Zeile 5: Violett, 6: Grün, 7: Grün, 8: Orange
- X-Position: `0.546875` (wallInnerEast + frameWidth/2)

---

## Würfel-Textur & Slot-System

**Würfel-Textur:**
- Format: WebP Base64-embedded (cubeTexImg)
- Sättigung: reduziert dargestellt auf 3×3 Canvas-Grid
- Leere-Würfelflächen-Textur: `leerewfl.webp`

**Deck (Zahlen 1-12, randomisiert):**
```javascript
const deck = shuffle([1,2,3,4,5,6,7,8,9,10,11,12]);
// deck[0-3]  → f0 (Face 0) Slots S1-S4
// deck[4-7]  → f1 (Face 1) Slots S1-S4
// deck[8-11] → f2 (Face 2) Slots S1-S4
```

**Face-Zuordnung (3 Faces mit je 4 sichtbaren Zahlen):**
```javascript
// Face 0: Zellen 7=S1, 4=S2, 2=S3, 3=S4
// Face 1: Zellen 10=S1, 11=S2, 15=S3, 18=S4
// Face 2: Zellen 27=S1, 26=S2, 22=S3, 19=S4
```

**Canvas-Textur (3×3 Grid):**
```javascript
function createFaceTexture(slotMap, imgRotation) {
  canvas: 256×256
  cell: 256/3 = 85.33px
  // Würfel-Textur als Hintergrund (optional rotiert)
  // Slot-Zellen beschriftet mit Farbe #2a1810
  // Font: bold 56px Arial
}
```

**Slot-Map Struktur:**
```javascript
slotMap = { 
  "row,col": { 
    val: number,     // Würfelwert 1-12
    rot: rotation    // Rotation in Radiant
  } 
}
```

**TopFace-Tracking:**
```javascript
let topFace = 'f2'; // Anfangslage (f2 oben bei Start in Q3)

function advanceTopFace() {
  const cw  = { f2: 'f0', f0: 'f1', f1: 'f2' };
  const ccw = { f2: 'f1', f1: 'f0', f0: 'f2' };
  topFace = (kippDirection === 'cw') ? cw[topFace] : ccw[topFace];
}
```

**Material-Index pro Face (BoxGeometry: [+X,-X,+Y,-Y,+Z,-Z]):**
```javascript
const TOP_FACE_MAT_IDX = { f0: 4, f1: 1, f2: 2 };
```

---

## Nachbarfarben-System (Treffererkennung)

**Kernprinzip:** Jeder Slot (S1-S4) eines Faces hat in jedem Quadranten eine eindeutige Nachbarfarbe vom angrenzenden Rahmen-Farbfeld.

**Nachbarfarben-Matrix:**
```javascript
const SLOT_NEIGHBOR_COLORS = {
  0: { S1: 'blau',    S2: 'violett', S3: 'gruen',   S4: 'orange'  },
  1: { S1: 'orange',  S2: 'blau',    S3: 'violett', S4: 'gruen'   },
  2: { S1: 'gruen',   S2: 'orange',  S3: 'blau',    S4: 'violett' },
  3: { S1: 'violett', S2: 'gruen',   S3: 'orange',  S4: 'blau'    }
};
```

**Quadrant-Farbfelder (welches Rahmenfeld gehört zu welchem Quadranten):**
```javascript
const QUADRANT_COLORS = {
  0: ['Nord_C', 'Nord_D', 'West_5', 'West_6'],
  1: ['Nord_E', 'Nord_F', 'Ost_5',  'Ost_6'],
  2: ['Süd_E',  'Süd_F',  'Ost_7',  'Ost_8'],
  3: ['Süd_C',  'Süd_D',  'West_7', 'West_8']
};
```

**Desätturierte Farben (für Nicht-Treffer Kärtchen):**
```javascript
const DESAT_COLORS = {
  violett: '#6b5a6b',
  blau:    '#5a6b7b',
  gruen:   '#5b7b6b',
  orange:  '#7b6b5a'
};
```

**Treffer-Logik (checkTreffer):**
1. Ermittle aktuelle Face (topFace → f0/f1/f2) → Offset im Deck
2. Lese 4 sichtbare Zahlen: `S1=deck[off], S2=deck[off+1], ...`
3. Prüfe: Ist `card.zahl` unter den sichtbaren Zahlen? → Welcher Slot?
4. Hole Nachbarfarbe: `SLOT_NEIGHBOR_COLORS[currentQuadrant][hitSlot]`
5. Vergleich: `neighborColor === card.color.name` → **TREFFER** oder **MISS**

**Treffer-Feedback:**
- **Hit:** Goldener Rahmen (`#cedb25`, CMYK 18/20/85/3), pulsierende Animation
- **Miss:** Roter Rahmen, Zahl wird ausgegraut, Kärtchen bleibt sichtbar

---

## Kippziel-Randomisierung

**Spielfarben:**
```javascript
const GAME_COLORS = [
  { name: 'violett', hex: '#a41b85' },
  { name: 'blau',    hex: '#006bb3' },
  { name: 'gruen',   hex: '#00a652' },
  { name: 'orange',  hex: '#e67814' }
];

const FARB_DISPLAY = {
  'violett': 'Violett', 'blau': 'Blau', 'gruen': 'Grün', 'orange': 'Orange'
};
```

**Randomisierungs-Regeln:**
- 12 Zahlen × 4 Farben = 48 mögliche Kombinationen
- Ausschlusskriterien pro neues Kippziel:
  1. Darf nicht bereits ausgegeben worden sein (`bereitsAusgegeben`)
  2. Darf nicht aktuell vom Würfel erfüllt sein (`getCurrentErfuellteKombis()`)
- Kombination wird als String gespeichert: `"zahl_farbe"` (z.B. `"8_gruen"`)

**Tracking-Sets:**
```javascript
const bereitsGetroffen = new Set();   // erfolgreiche Treffer
const bereitsAusgegeben = new Set();  // alle jemals ausgegebenen Kombis
```

**Kippziel-Generierung:**
```javascript
function generateNeueKombiOhne(ausschlussSet) {
  // Sammelt alle gültigen Kombis (1-12 × 4 Farben minus Ausschlüsse)
  // Wählt zufällig eine aus
  // Gibt {zahl, color} zurück oder null wenn keine verfügbar
}
```

---

## Spielphasen-System (Game Flow)

**Phasen-Überblick:**
```
SPIELZIEL → ENTSCHEIDUNG → DEMO → KIPPEN → SPIELEN → [ENTSCHEIDUNG → ...]
                                                          ↓ (nach 3. Karte)
                                                        PAUSE
                                                          ↓
                                                     [SPIELEN → ...]
                                                          ↓ (nach 6. Karte)
                                                        ENDE
```

### Phase 1: SPIELZIEL (`gamePhase = 'spielziel'`)
- Zeigt Spielerklärung mit Beispiel aus aktueller Würfellage
- Text: "SPIEL ZIEL: Erkippe möglichst viele der **6 Kippziele** (z.B. wie hier: *Zahl* an *Farbe*) mit möglichst wenig **SCHAU**-Sekunden."
- Brain Score Erklärung: 100% = 6 Hits / bis 20 SCHAU-Sek, 12% = 1 Hit / 120 SCHAU-Sek
- CSS: `#spielZielText` (position absolute, top: 421px, left: 66px, width: 284px)
- Weiter-Button zentral unter Text

### Phase 2: ENTSCHEIDUNG (`gamePhase = 'entscheidung'`)
- Verdecktes Kippziel erscheint an erster Kärtchen-Position
- Pulsierendes Auge-Icon (120px breit, `pointer-events: auto`)
  - Positioniert 55px unter verdecktesMesh (3D-Projektion)
  - Horizontal zentriert: `left: 50%; transform: translateX(-50%)`
  - Animation: `eyePulse` (scale 1↔1.12, shadow-Pulse)
- Text: "ENTSCHEIDUNG! Möchtest du dein erstes Kippziel aufdecken?"
- CSS: `#entscheidungText` (gleiche Position wie tutorialText)
- Touch-Events: `stopPropagation()` + `preventDefault()` um Canvas-Klick zu verhindern

### Phase 3: DEMO (`gamePhase = 'demo'`)
- Automatische Kipp-Animation im Uhrzeigersinn (Q3→Q0→Q1→Q2)
- 4 Kipps mit Text-Sequenz ("KIPPEN", "SCHAUEN", "MERKEN")
- Kipp-Animation: 800ms Dauer, easeInOut
- Text-Pausen: 400ms zwischen Phasen
- Abbruch durch Touch → direkt zu KIPPEN-Phase

### Phase 4: KIPPEN / WÜRFELSCHAUEN (`gamePhase = 'kippen'`)
- Überschrift: **WÜRFELSCHAUEN**
- Instructiontext: "Kippe den Würfel in der Box in beliebige Richtung von Ecke zu Ecke..."
- Timer startet bei erstem Kipp (SCHAU-Sekunden)
- Timer-Display: zentriert (180px left), zeigt verstrichene Sekunden
- Swipe-basiertes Kippen (Touch-Drag auf Canvas)
- Kippziel aufdeckbar per Swipe auf verdecktesMesh

### Phase 5: SPIELEN (`gamePhase = 'spielen'`)
- Kippziel aufgedeckt, Spieler muss es erkippen
- Tutorialtext: "ÜBERLEGE! Wie oft, **von 1 bis 6**, und in welche Richtung musst du kippen..."
- Zielscheibe (dunkelbraun, pulsierend) zwischen Zeile 7/8 und Spalte C/D
- **Tap-dann-Swipe Mechanik:**
  1. Spieler tippt 1-6× auf Würfel (Tap-Counter in benachbarten Quadranten)
  2. Spieler swipet in gewünschte Richtung
  3. Auto-Kipp-Sequenz läuft ab (700ms/Kipp, 300ms Pause)
  4. Treffer-Wertung nach Auto-Kipp

**Tap-Counter Anzeige:**
- Silberne Zahlen auf 3D-PlaneGeometry (0.28×0.28)
- In beiden benachbarten Quadranten positioniert
- Metallischer Silber-Gradient mit Schatten

### Phase 6: PAUSE (`gamePhase = 'pause'`)
- Auslöser: Nach 3. Kippziel (activeCardIdx === 3)
- Würfel wird auf Ecke balanciert (`animateAufEcke()`)
- Kärtchen werden ausgeblendet (opacity-Animation 350ms)
- Pause-Lichter eingeschaltet
- Cube-Position/Quaternion/topFace/Quadrant werden gespeichert
- Spieler kann Würfel per Drag drehen (pauseAlpha, pauseBeta)
- "weiter"-Button → `endPausePhase()` → zurück zu SPIELEN

### Phase 7: ENDE (`gamePhase = 'ende'`)
- Brain Score wird berechnet und angezeigt
- Alle UI-Elemente versteckt

---

## Brain Score Berechnung

```javascript
function calculateBrainScore(observationTime, hits) {
  const FREE_TIME = 30;      // Sekunden ohne Abzug (→ jetzt 20 im Display)
  const MAX_TIME = 120;      // Maximum SCHAU-Sekunden
  const k = 1.0;             // Logistik-Steilheit
  const c = 2.5976583;       // Logistik-Wendepunkt

  // Time Factor: S_time = 1 - 0.31 × (E / 90)^0.855
  // Hit Factor:  S_hits = L(hits) / L(6)  [logistische Funktion]
  // Final:       brainScore = 100 × S_time × S_hits

  // 0 Hits → "No Hit, no Score"
  // 100% = 6 Hits ≤ 30 Sek
  // 12% ≈ 1 Hit bei 120 Sek
}
```

---

## Kipp-Mechanik (Pivot-Punkte & Richtungen)

**Alle Pivot-Punkte liegen auf π1 (Y=0) an Würfelkanten:**

**8 Kipp-Richtungen:**
```javascript
// CW-Zyklus (Uhrzeigersinn): Q3 → Q0 → Q1 → Q2 → Q3
'north':          Q3→Q0, pivot(-0.234375, 0, 0.109375), axis X, rot -π/2
'east':           Q0→Q1, pivot(0, 0, 0.343125),         axis Z, rot -π/2
'south_from_q1':  Q1→Q2, pivot(0.234375, 0, 0.109375),  axis X, rot +π/2
'west_from_q2':   Q2→Q3, pivot(0, 0, 0.343125),         axis Z, rot +π/2

// CCW-Zyklus (Gegenuhrzeigersinn): Q3 → Q2 → Q1 → Q0 → Q3
'south':          Q0→Q3, pivot(-0.234375, 0, 0.109375), axis X, rot +π/2
'west':           Q1→Q0, pivot(0, 0, 0.343125),         axis Z, rot +π/2
'north_from_q2':  Q2→Q1, pivot(0.234375, 0, 0.109375),  axis X, rot -π/2
'east_from_q3':   Q3→Q2, pivot(0, 0, 0.343125),         axis Z, rot -π/2
```

**Kipp-Animation (performKipp):**
```javascript
async function performKipp(direction, duration = 800) {
  // 1. PivotGroup erstellen an Pivot-Punkt
  // 2. Cube aus kipGroup lösen, in PivotGroup einhängen
  // 3. Rotation animieren (easeInOut: quadratisch)
  // 4. Cube zurück in kipGroup, PivotGroup entfernen
  // 5. currentQuadrant aktualisieren
}
```

**Kipp-Richtungserkennung (Swipe):**
- `kippDirection`: 'cw' (Uhrzeigersinn) oder 'ccw' (Gegen-Uhrzeigersinn)
- `lockedKippDir`: Gesperrt nach erstem Kipp in einer Runde
- Swipe-Erkennung: Touch-Delta X/Y, Mindest-Schwelle für Richtung

**Auto-Kipp-Sequenz (nach Tap-Count):**
```javascript
async function runAutoKippSequence(steps) {
  // Führt 'steps' Kipps automatisch aus
  // 700ms pro Kipp-Animation, 300ms Pause dazwischen
  // Richtung basiert auf kippDirection + currentQuadrant
  // Am Ende: checkKippzielTap() für Treffer-Wertung
}
```

---

## Liegezeit-Tracking

**Zweck:** Erkennung ob Spieler schnell oder zögerlich kippt.

**Empirische Daten:**
- Schnelles Kippen: 300-450ms Liegezeit
- Zögerliches Kippen: 1200ms+
- Erste Liegezeit = Denkzeit (wird nicht für Auto-Eval gewertet)

```javascript
let liegeZeiten = [];
let liegeStartTime = performance.now();
```

---

## Externe WebP-Assets

| Dateiname | Verwendung |
|-----------|------------|
| `Boxboden.webp` | Spielfeld-Boden-Textur |
| `metallwand.webp` | Wand-Textur (Silber-Metall) |
| `spieltisch.webp` | Tisch-Oberflächen-Textur |
| `leerewfl.webp` | Leere Würfelflächen-Textur |
| `leernohit.webp` | Leeres Kärtchen (kein Treffer) |
| `blauzielhit.webp` | Kärtchen-Textur Farbe Blau |
| `orangezielhit.webp` | Kärtchen-Textur Farbe Orange |
| `gruenzielhit.webp` | Kärtchen-Textur Farbe Grün |
| `violettzielhit.webp` | Kärtchen-Textur Farbe Violett |

**Würfel-Textur:** WebP Base64-embedded direkt im HTML (cubeTexImg.src)
**Auge-Icon:** PNG Base64-embedded direkt im HTML (eyeIcon src)

---

## UI-Elemente & CSS-Positionierung

**Haupt-Overlay:** `#uiOverlay` - `pointer-events: none` (Eltern-Container)
→ Alle klickbaren Kinder brauchen `pointer-events: auto`

**Wichtige UI-Elemente:**

| Element | CSS-Position | Funktion |
|---------|-------------|----------|
| `#spielZielText` | top:421px, left:66px, w:284px | Spielziel-Erklärung |
| `#instructionText` | top:421px, left:66px, w:284px | WÜRFELSCHAUEN-Anleitung |
| `#tutorialText` | top:421px, left:66px, w:284px | ÜBERLEGE!-Text in Spielphase |
| `#entscheidungText` | top:421px, left:66px, w:284px | Entscheidungs-Text |
| `#eyeIcon` | left:50%, translateX(-50%) | Pulsierendes Auge-Icon |
| `#countdown` | left:180px (zentriert) | SCHAU-Sekunden Timer |
| `#brainScore` | - | Endergebnis-Anzeige |
| `#kippTarget` | 3D-projiziert | Dunkelbraune Zielscheibe |
| `#actionButton` | dynamisch positioniert | Weiter/Zurücksetzen-Button |

**Zielscheibe (#kippTarget):**
```css
#kippTarget {
  width: 14px; height: 14px;
  border: 2px solid #4a2a10;
  border-radius: 50%;
  animation: targetPulse 1.5s ease-in-out infinite;
}
#kippTarget::after {
  /* 4px Punkt innen */
  width: 4px; height: 4px;
  background: #4a2a10;
}
```
Position: 3D-projiziert zwischen Zeile 7/8, Spalte C/D → `(-0.78125, 0.002, 0.265625)`

---

## Kamera-Einstellungen

**Augenhöhe (Spieler):**
```javascript
const baseY = 2.143; // 320mm = 13.71 Zellen
```

**Perspektive:**
```javascript
const camera = new THREE.PerspectiveCamera(
  50,                                   // FOV
  containerWidth / containerHeight,     // Aspect (360/720 = 0.5)
  0.01,                                 // Near
  100                                   // Far
);
```

**Startposition:**
```javascript
const verticalAngle = -1.0;
const horizontal = 0;
const radius = 1.6;

camera.position.set(
  0 + Math.sin(horizontal) * 0.5,
  baseY + radius * Math.sin(-verticalAngle),
  radius * Math.cos(verticalAngle) + Math.cos(horizontal) * 0.3
);
```

**LookAt-Punkt (variiert mit Kamerawinkel):**
```javascript
const lookAtBlend = Math.max(0, Math.min(1, (verticalAngle + 0.7) / 0.4));
const lookAtY = 0.234375 + (0.46875 - 0.234375) * (1 - lookAtBlend);
const lookAtZ = 0.7 + (0.421875 - 0.7) * lookAtBlend;

camera.lookAt(0, lookAtY, lookAtZ);
```

**3D→Screen Projektion (für UI-Positionierung):**
```javascript
const pos3D = new THREE.Vector3(x, y, z);
pos3D.project(camera);
const screenX = (pos3D.x * 0.5 + 0.5) * 360;  // px
const screenY = (-pos3D.y * 0.5 + 0.5) * 720;  // px
```

---

## Grid & Achsen

**Grid-Positionen:**
```javascript
// Grid auf π1
gridHelper.position.y = 0.001;
gridHelper.position.z = 0.421875; // Verschoben mit Spielbereich

// Grid auf Wandhöhe (falls vorhanden)
gridHelper2.position.y = 0.46875;
gridHelper2.position.z = 0.421875; // Verschoben mit Spielbereich
```

**Achsen:**
- X-Achse: Verläuft entlang X bei verschiedenen Y-Höhen
- Z-Achse: Verläuft entlang Z bei verschiedenen Y-Höhen

---

## Schnellreferenz: Häufige Umrechnungen

**Pixel → Three.js Einheiten:**
```javascript
Einheiten = Pixel × (2.5 / 720)
```

**Zellen → Three.js Einheiten:**
```javascript
Einheiten = Zellen × 0.15625
```

**Millimeter → Three.js Einheiten:**
```javascript
// 1 Zelle = 23.33mm
Einheiten = (mm / 23.33) × 0.15625
```

**Beispiele:**
- 1 Zelle = 45px = 23.33mm = 0.15625 Einheiten
- 3 Zellen = 135px = 70mm = 0.46875 Einheiten
- 6 Zellen = 270px = 140mm = 0.9375 Einheiten
- 8 Zellen = 360px = 187mm = 1.25 Einheiten

**Spielbereich-Verschiebung:**
- Z-Verschiebung = 0.1105 Einheiten = ~0.7 Zellen nach Süden
- Spielbereich-Zentrum Z = 0.1105 (nicht Z = 0!)

---

## Bekannte offene Punkte

1. **Card-Marking Preservation Bug:** Nach Runde (Treffer/Miss) gehen Kärtchen-Markierungen (goldene/rote Rahmen) bei neuer Runde verloren. Benötigt `afterKippwertung`-Logik die vorherige Kärtchen-Zustände beibehält.
2. **Brain Score FREE_TIME:** Im Code `FREE_TIME = 30`, im Display wird "bis 20 SCHAU-Sek" angezeigt → Diskrepanz klären.
3. **Timer-Modul:** Go/Stopp-Erkennung basierend auf Würfelbewegung (isCubeSettled) ist als Konzept vorhanden, aber noch nicht in game.html integriert.

---

## Verwendung dieser Referenz

**In anderen Chats:**
1. Diese Datei hochladen
2. Sagen: "Nutze dieses Rastersystem mit der Z-Verschiebung von 0.1105"
3. Der neue Chat versteht sofort die Koordinaten

**Für Entwicklung:**
- Spielbereich-Zentrum ist bei Z = 0.1105 (nicht bei 0!)
- Alle Maße in Rasterzellen denken
- Mit 0.15625-Multiplikator in Three.js umrechnen
- π1 als Basis-Ebene für alle Positionierungen

**Koordinaten-Kommunikation:**
- "3 Zellen über π1" = Y = 0.46875
- "Bei -3X/+3Z relativ" = Position relativ zum Spielbereich-Zentrum (0, Y, 0.1105)
- "Auf x12" = Z = 0 (NICHT Spielbereich-Mitte!)
- "Spielbereich-Zentrum" = (0, Y, 0.1105)
- "Q0" = Nordwest-Quadrant (X<0, Z<0.1105)

---

*Erstellt: 2026-01-31*  
*Aktualisiert: 2026-02-08*  
*Projekt: DigiKipp 3D / KIPP COLOR*  
*Entwickler: Peter Lenhart*  
*Version: Erweitert mit Spielphasen, Treffererkennung, Kippziel-Randomisierung, Brain Score, Tap-Mechanik, UI-System, Asset-Liste und bekannten Bugs*
