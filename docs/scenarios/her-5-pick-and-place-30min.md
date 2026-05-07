# Scenario: Robot binnen 30 minuten installeren voor pick-and-place in metaal-MKB

**Linear:** HER-5  
**Status:** Specificatie  
**Doelgroep:** Productontwikkeling, engineering, demo-team

---

## Samenvatting

Dit scenario beschrijft hoe een medewerker van een metaal-MKB bedrijf een lichte industriële
cobot in maximaal 30 minuten kan instellen voor een standaard pick-and-place taak. De flow maakt
gebruik van een 3D-scan van de werkcel, een virtuele robotrepresentatie, geautomatiseerde
padplanning, visuele feedbackmechanismen en teleoperatie (teleop) als fallback en
correctiemiddel. Teleop is beschikbaar via lokale interface én via internet.

---

## Aannames (metaal-MKB context)

| Aanname | Waarde/bereik |
|---|---|
| Objectmateriaal | Metaal (aluminium, staal, koper) |
| Objectgewicht | 0,1 – 15 kg |
| Objectafmetingen | 5 cm – 60 cm grootste maat |
| Nauwkeurigheid | ±1 mm voor plaatsing |
| Omgevingstemperatuur | 10 – 40 °C |
| Robotbereik | 600 – 1500 mm reikwijdte (lichte cobot, 6-DOF) |
| Montagetype | Tafel- of vloergemonteerd; verrijdbare maar vergrendelbare tafel toegestaan |
| Belichting | Fabriekslicht (fluorescent/LED), mogelijke reflectie op metaal |
| Veiligheidsklasse | ISO 10218-1/2, UR e-series of vergelijkbaar cobot |
| Netwerk | LAN voor robotbesturing; internetverbinding ondersteund voor remote teleop |

---

## Benodigde hardware en software

### Hardware

- Lichte 6-DOF cobot (bijv. Universal Robots UR5e/UR10e, of gelijkwaardig)
- 3D-camera voor omgevingsscan (dieptecamera, stereocamera of bewegende monocamera;
  type afhankelijk van vereiste nauwkeurigheid en omgevingsbelichting)
- Eindeffector/grijper passend bij objecttype (magnetisch, vacuüm of vingersgrijper)
- Werkstation of industriële pc (minimaal 16 GB RAM, discrete GPU voor visualisatie)
- Noodstopknop conform ISO 13849

### Software

- **Scan-module**: 3D-omgevingsscanner en puntenwolkverwerker
- **Digitale tweeling / visualisatielaag**: rendering van werkcel en robotmodel in real-time 3D
- **Padplanner**: kinematisch/collision-aware padplanningsalgoritme (eigen implementatie of
  leverancier-SDK; geen ROS vereist)
- **Haalbaarheidsanalyser**: bereikbaarheids-, botsings- en lastanalyse
- **Teleop-interface**: joystick, webinterface of leader-robot-arm voor handmatige correctie
  (lokaal of via internet)
- **Uitvoeringsruntime**: robotbesturing via leverancier-SDK of eigen stack (geen ROS)
- **UI-front-end**: tablet of webapplicatie voor operator-interactie

---

## Gebruikersscenario: stap voor stap

### Voorbereiding (buiten de 30 minuten)

De medewerker bereidt de volgende input voor voordat de klok start:

- 3D-scan van werkcel opgeslagen (puntenwolk, bijv. `.ply` of `.pcd`)
- Korte tekstuele taakomschrijving (bijv. "Plaat van positie A naar B verplaatsen")
- Objectparameters beschikbaar:
  - Gewicht: bijv. 4,2 kg
  - Afmetingen: 30 × 20 × 5 cm
  - Grijppunten: bovenzijde, magnetisch oppervlak
  - Start- en eindlocatie: coördinaten of visuele aanduiding in scan
  - Vereiste nauwkeurigheid: ±0,5 mm
  - Gewenste cyclustijd: 8 seconden per cyclus
  - Veiligheids- en omgevingsbeperkingen: geen personen in werkcel tijdens uitvoering

---

### Fase 1: Omgevingsimport en robotplaatsing (0 – 5 min)

| Tijd | Stap | Actor | Resultaat |
|---|---|---|---|
| 0:00 | Medewerker opent de installatiewizard en laadt de opgeslagen 3D-scan | Medewerker | Gescande werkcel verschijnt in 3D-viewer |
| 0:30 | Systeem verwerkt de puntenwolk en herkent vloer, obstakels en werkoppervlakken | Systeem | Gesegmenteerde werkcel met gemarkeerde zones |
| 1:30 | Medewerker positioneert het robotmodel in de viewer op de gewenste montagepositie (drag-and-drop of coördinateninvoer); het systeem toont beschikbare montageopties (tafel, verrijdbaar, vloer) | Medewerker | Virtuele robot verschijnt in gescande omgeving op de juiste locatie |
| 3:00 | Systeem berekent het werkbereik-overlay: groene zone = bereikbaar, rode zone = buiten bereik | Systeem | Kleurgecodeerde bereikbaarheidsoverzicht zichtbaar in viewer |
| 4:00 | Medewerker bevestigt robotpositie of past aan op basis van bereikbaarheidsfeedback | Medewerker | Robotpositie vastgelegd |
| 5:00 | ✅ Fase 1 compleet | – | Virtuele robot staat in gescande werkcel |

**Visuele feedback in deze fase:**
- Werkcel gerenderd in real-time 3D
- Bereikbaarheids-overlay (groen/rood) direct zichtbaar bij plaatsing
- Statusbalk toont: "Robotpositie: Bevestigd"

---

### Fase 2: Taakinvoer en objectdefinitie (5 – 10 min)

| Tijd | Stap | Actor | Resultaat |
|---|---|---|---|
| 5:00 | Medewerker voert taakomschrijving in (vrije tekst of gestructureerd formulier) | Medewerker | Taakparameters geregistreerd |
| 6:00 | Medewerker definieert start- en eindlocatie door klikken op puntenwolk of handmatige coördinateninvoer | Medewerker | Pick- en place-punten gemarkeerd in viewer |
| 7:00 | Medewerker voert objectparameters in (gewicht, afmetingen, grijptype) | Medewerker | Objectprofiel aangemaakt |
| 8:00 | Systeem koppelt grijptype aan eindeffectorbibliotheek en toont grijpermodel in viewer | Systeem | Geselecteerde grijper zichtbaar op robotarm in 3D |
| 9:00 | Systeem voert initiële last- en gripinschatting uit | Systeem | Grip-haalbaarheidsmelding (groen = haalbaar / geel = marginaal / rood = niet haalbaar) |
| 10:00 | ✅ Fase 2 compleet | – | Taakinvoer volledig, pick/place-punten gedefinieerd |

**Visuele feedback in deze fase:**
- Pick-punt: blauwe markering in werkcel
- Place-punt: groene markering in werkcel
- Object: 3D-model op pick-locatie
- Grip-indicator: groen/geel/rood pictogram

---

### Fase 3: Padplanning en haalbaarheidsanalyse (10 – 18 min)

| Tijd | Stap | Actor | Resultaat |
|---|---|---|---|
| 10:00 | Medewerker start padplanning via knop "Bereken pad" | Medewerker | Systeem start planningsalgoritme |
| 11:30 | Systeem berekent pick-to-place pad inclusief approach- en retreat-bewegingen | Systeem | Virtueel pad zichtbaar als animatie in 3D-viewer |
| 13:00 | Systeem voert botsingscheck uit langs het gehele pad (arm vs. omgeving) | Systeem | Botsingvrij pad = groen; conflicten = rood gemarkeerd |
| 14:00 | Systeem toont haalbaarheidsrapport: bereikbaarheid, singulariteiten, laadgrens, cyclustijdschatting | Systeem | Haalbaarheidsrapport zichtbaar in zijpaneel |
| 15:00 | Indien botsingswaarschuwing: medewerker past robotpositie of padparameters aan | Medewerker | Herberekening gestart |
| 17:00 | Medewerker beoordeelt pad-animatie en bevestigt of vraagt aanpassing | Medewerker | Pad goedgekeurd of bijgesteld |
| 18:00 | ✅ Fase 3 compleet | – | Botsingvrij pad berekend en goedgekeurd |

**Visuele feedback in deze fase:**
- Pad: vloeiende 3D-curve van pick naar place (met approach/retreat)
- Animatie: robot doorloopt het pad in versnelde simulatie
- Botsingsmarkeringen: rode highlight op conflicterende segmenten
- Haalbaarheidsrapport bevat:
  - ✅/❌ Bereikbaarheid pick-punt
  - ✅/❌ Bereikbaarheid place-punt
  - ✅/❌ Geen singulariteiten
  - ✅/❌ Gewicht binnen laadgrens
  - ⏱ Geschatte cyclustijd: X,X seconden

---

### Fase 4: Pre-executie checks en veiligheidsbevestiging (18 – 22 min)

De volgende checks zijn verplicht voor uitvoering mag starten:

| # | Check | Wie | Methode |
|---|---|---|---|
| 1 | Werkcel vrij van personen | Medewerker / Systeem | Visuele inspectie + bevestiging in UI; optioneel: automatische personendetectie via camera |
| 2 | Noodstop bereikbaar en getest | Medewerker | Noodstoptest uitvoeren via UI-wizard |
| 3 | Grijper gemonteerd en bevestigd | Medewerker | Visuele inspectie + koppelingsbevestiging |
| 4 | Object op startpositie geplaatst | Medewerker | Visuele inspectie |
| 5 | Robotverbinding actief (groen statuslampje) | Systeem | Automatische verbindingscheck |
| 6 | Padvalidatie geslaagd (geen botsingen) | Systeem | Automatisch uit fase 3 |
| 7 | Laadlimiet niet overschreden | Systeem | Automatisch uit haalbaarheidsrapport |

De UI toont een checklist. Pas na aftekening van alle 7 punten is de "Start"-knop actief.

---

### Fase 5: Eerste uitvoering (teleop-begeleide testrun) (22 – 27 min)

| Tijd | Stap | Actor | Resultaat |
|---|---|---|---|
| 22:00 | Medewerker kiest "Begeleide testrun" (teleop-modus actief als vangnet) | Medewerker | Robot start approach naar pick-punt; teleop beschikbaar |
| 23:00 | Robot voert pick-beweging uit; systeem toont gripstatus | Systeem | Grip bevestigd of foutmelding |
| 24:00 | Robot transporteert object langs het geplande pad | Robot | Virtueel pad-overlay toont actuele positie vs. gepland pad |
| 25:00 | Robot plaatst object op eindlocatie; systeem bevestigt plaatsnauwkeurigheid | Systeem | Plaatsfout < ±1 mm: ✅ geslaagd |
| 25:30 | Medewerker beoordeelt resultaat; kan direct corrigeren via teleop of pad aanpassen | Medewerker | Correctie uitgevoerd of testrun goedgekeurd |
| 27:00 | ✅ Fase 5 compleet | – | Testrun geslaagd of gecorrigeerd |

**Rol van teleop in deze fase:**
- Teleop is beschikbaar als real-time correctiemiddel: joystick, webinterface, of leader-robot-arm
- Teleop werkt lokaal (LAN) én via internet (remote)
- Bij afwijking > configureerbare drempel: systeem pauzeert en vraagt om teleop-interventie
- Teleop-correctie wordt gelogd en kan worden gebruikt voor padverbetering

---

### Fase 6: Goedkeuring en productiestart (27 – 30 min)

| Tijd | Stap | Actor | Resultaat |
|---|---|---|---|
| 27:00 | Medewerker beoordeelt testrun-resultaat in samenvatting (nauwkeurigheid, cyclustijd, incidenten) | Medewerker | Goedkeuring of heriterate |
| 28:00 | Medewerker activeert autonome lus-modus | Medewerker | Robot herhaalt pick-and-place cyclus autonoom |
| 29:00 | Systeem monitort continu: plaatsnauwkeurigheid en cyclustijd | Systeem | Live dashboard zichtbaar |
| 30:00 | ✅ Robot operationeel binnen 30 minuten | – | Productie gestart |

---

## Teleop: wanneer toegestaan en vereist

| Situatie | Teleop-gebruik |
|---|---|
| Fase 5 testrun | Toegestaan als begeleiding en correctiemiddel |
| Grip mislukt (geen bevestiging) | Vereist teleop-interventie |
| Padafwijking > drempelwaarde | Systeem pauzeert, teleop vereist |
| Object buiten tolerantie geplaatst | Teleop voor handmatige correctie |
| Autonome productielus | Niet beschikbaar (uitgeschakeld voor veiligheid) |
| Noodsituatie | Noodstop, daarna teleop voor herstelbeweging |
| Remote operatie | Teleop via internet toegestaan voor begeleiding en correctie |

**Teleop-methoden (alle gelijkwaardig ondersteund):**
- Joystick (lokaal, USB of Bluetooth)
- Webinterface (lokaal LAN of via internet)
- Leader-robot-arm (leader-follower setup voor intuïtieve sturing)

---

## Minimale demo / MVP

Om dit scenario overtuigend te demonstreren is het volgende minimale systeem vereist:

### MVP-componenten

1. **3D-scan import**: laad een bestaand `.ply`-bestand, toon in 3D-viewer
2. **Robotmodel plaatsing**: drag-and-drop van cobotmodel in de viewer; montageopties tonen (tafel/vloer/verrijdbaar)
3. **Bereikbaarheids-overlay**: eenvoudige groen/rood workspace-visualisatie
4. **Pick/place-punten**: klik in de viewer om punten te markeren
5. **Padplanning**: eigen padplanningsalgoritme of leverancier-SDK met botsingscheck (geen ROS)
6. **Simulatie-animatie**: robot doorloopt pad in viewer (zonder echte robot)
7. **Haalbaarheidsrapport**: statische checklist op basis van padplanneroutput
8. **Teleop-interface**: joystick of webUI voor handmatige sturing (lokaal en via internet)
9. **Pre-executie checklist**: UI-wizard met 7 checkboxes (inclusief camera-optie voor persoonsdetectie)
10. **Live dashboard**: cyclustijd en statusmonitor tijdens uitvoering

### MVP buiten scope (vervolgfases)

- Automatische objectherkenning via camera
- Multi-robot coördinatie
- Cloud-synchronisatie voor data/logging
- Automatisch grijpertypeadvies
- Leerloop vanuit teleop-correcties
- Krachtsensor voor grijpbevestiging (vervolgfase)

---

## Tijdoverzicht

| Fase | Activiteit | Duur |
|---|---|---|
| Voorbereiding | Scan + parameters verzamelen | Buiten 30 min |
| Fase 1 | Omgevingsimport en robotplaatsing | 5 min |
| Fase 2 | Taakinvoer en objectdefinitie | 5 min |
| Fase 3 | Padplanning en haalbaarheidsanalyse | 8 min |
| Fase 4 | Pre-executie checks | 4 min |
| Fase 5 | Testrun (teleop-begeleiding) | 5 min |
| Fase 6 | Goedkeuring en productiestart | 3 min |
| **Totaal** | | **30 min** |

---

## Randvoorwaarden (samenvatting)

| Categorie | Randvoorwaarde |
|---|---|
| Reikwijdte | Robot bereikt zowel pick- als place-locatie (haalbaarheidscheck vereist) |
| Gewicht | Object ≤ maximale payload grijper én robotarm |
| Veiligheid | ISO 10218-1/2 compliant; noodstop getest vóór start |
| Nauwkeurigheid | Pick ±1 mm, place ±0,5 mm (configureerbaar per applicatie) |
| Cyclustijd | Padplanner geeft schatting; acceptatie door operator vóór productiestart |
| Omgeving | Geen personen in werkcel tijdens autonome modus |
| Netwerk | LAN voor robotbesturing; internet ondersteund voor remote teleop |
| Hardware gereed | Grijper gemonteerd, robot gekalibreerd, scan beschikbaar |
| Montage | Tafel-, vloer- of verrijdbare vergrendelbare opstelling toegestaan |
