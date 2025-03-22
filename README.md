# Z80-Karte für Commodore C64

Ich arbeite derzeit an einer Z80-Karte für den Commodore C64, um CP/M 2.2 verwenden zu können. Das Ganze basiert auf einem CPLD und einem 8 MHz Z80-Prozessor.

![](https://codeberg.org/diwou/Z80-Card_for_Commodore_C64/raw/branch/main/Images/Z80-Card_design_pcb.jpg)

Derzeit befindet sich das Projekt in der Beta-Phase. Ich würde die Stabilität zumindest auf den von mir getesteten Computern als ziemlich hoch einstufen.

## Beschreibung

Basierend auf dem alten Commodore-CP/M-Modul habe ich den Z80-Prozessor direkt an den 8 MHz Dot-Clock des C64 angeschlossen.

Allerdings arbeitet der Rest des C64, solange man den VIC nicht abschaltet, weiterhin nur mit 1 MHz, sodass der Zugriff auf RAM, Floppy usw. weiterhin in der Geschwindigkeit begrenzt ist.

Ein großes Problem war die Kompatibilität mit dem ursprünglichen Modul. Ich habe die Z80-Karte in folgenden Boards getestet:

- ASSY-NO.250407 / ARTWORK-NO.251137 / REV.B
- ASSY-NO.250407 / ARTWORK-NO.251137 / REV.C
- ASSY-NO.250469 / PCB-NO.252311 / REV.4
- ASSY-NO.250469 / PCB-NO.252311 / REV.B
- SX-64 (Revision müsste noch geprüft werden)

(alle PAL-Modelle)

Ich habe an einigen Stellen die Signalabhängigkeiten geändert, sodass sich VIC und Z80-Karte nicht in die Quere kommen. Außerdem verwende ich keinen Zähler für die Waitstates, sondern bestimme, in welchem Zyklus sich der Z80 aktuell befindet, und setze dann abhängig vom VIC die entsprechenden Waitstates.

Das hat die Stabilität enorm verbessert. Auch scheint nun die Version des VIC keine Rolle mehr zu spielen. Ich konnte zumindest keine Unterschiede in der Stabilität bei verschiedenen Versionen feststellen.

## Zusammenbau

Beim Zusammenbau gibt es eigentlich nicht viel zu beachten. Zuerst sollte das Xilinx-CPLD verlötet werden. Danach der Spannungsregler, gefolgt von den SMD-Kondensatoren.

Die restlichen Bauteile werden von klein nach groß bestückt. Also erst die Kondensatoren, dann die Ferritperlen, danach der 40-polige IC-Sockel. Zum Schluss der Elektrolytkondensator. Diesen am besten liegend montieren, da er vergleichsweise hoch ist und leicht abbrechen könnte.

## Firmware flashen

Die Firmware liegt als JED-Datei vor und kann mit einem JTAG-Programmieradapter geflasht werden.

Am einfachsten funktioniert dies mit dem Tool "xc3sprog" und einem kleinen FT232H-Adapterboard. Solche Boards gibt es sehr günstig.

Eine sehr gut beschriebene Anleitung dazu findet sich hier:
https://github.com/1c3d1v3r/neatPLA/tree/master/programming

Die Pinbelegung des JTAG-Anschlusses befindet sich auf der Rückseite der Platine:

![](https://codeberg.org/diwou/Z80-Card_for_Commodore_C64/raw/branch/main/Images/Z80-Card_JTAG.jpg)

Ich würde hier keinen Header anlöten. Für das kurze Flashen reicht es aus, die kleinen Dupont-Stecker des FT232H-Boards leicht schräg in die Lötpads zu stecken und zu halten.

Nach dem Flashen ist die Karte einsatzbereit.

## CP/M-Diskette

Meine Z80-Karte verwendet die gleiche CP/M-Systemdiskette wie das originale Commodore-Modul. Ein D64-Image der Standard-Systemdiskette befindet sich im Repository.

Weitere Diskettenabbilder (mit Wordstar, Turbo Pascal, Zork etc.) gibt es unter:
http://biosrhythm.com/?p=1220

Dort ist auch eine Software-Emulation für 80 Zeichen integriert. Diese ersetzt zwar keine echte 80-Zeichen-Karte, ist aber angenehmer als die 40-Zeichen-Ausgabe des C64.

Im Internet lassen sich viele weitere CP/M-Programme und Tools finden.

## Stückliste (BOM)

Viele Teile werden für dieses Projekt nicht benötigt.

**Seit Firmware-Version v0.5 werden auch CMOS-CPUs unterstützt.**

| Nr. | Menge | Bezeichner              | Hersteller    | Hersteller-Teilenummer                                   | Beschreibung                                                  |
|-----|--------|--------------------------|----------------|-----------------------------------------------------------|---------------------------------------------------------------|
| 1   | 6      | FB1 - FB6                | Fair-Rite      | 2743005112                                                | Ferritperle axial, 91 Ω @ 100 MHz                           |
| 2   | 6      | C2, C3, C10, C13, C14, C15| KEMET          | C315C100J3G5TA                                            | Keramik-Kondensator, 10 pF, 25 V, C0G/NP0, radial             |
| 3   | 6      | C4, C5, C6, C7, C8, C9   | KEMET          | C0603C104J3RACTU                                          | MLCC, 0,1 μF, 25 V, X7R, 0603 (1608 Metrisch), ±5 %           |
| 4   | 2      | C16, C17                | KEMET          | C0805C106K8RACTU                                          | Keramik-Kondensator, 10 μF, 10 V, X7R, 0805                   |
| 5   | 1      | C1                      | KEMET          | C320C104K5R5TA7305                                        | Keramikscheiben-Kondensator, 100 nF, -20 %~+80 %, 50 V, THT   |
| 6   | 1      | U5                      | Exar           | SPX5205M5-L-3-3/TR                                        | LDO-Spannungsregler, 3,3 V, 0,15 A, 5-Pin SOT-23              |
| 7   | 1      | CP1                     | Nichicon       | UMF1C470MDD1TP                                            | Aluminium-Elektrolytkondensator, 47 μF, 105 °C, bedrahtet     |
| 8   | 1      | U1                      | Xilinx         | XC95144XL-10TQ100C                                        | CPLD, 144 Makrozellen, 10 ns, 100-TQFP                        |
| 9   | 1      | U2                      | Zilog          | Z084C0008PEG / Z0840008PEC / Z0840008PSC / Z8400HPS       | Z80-CPU, 8 MHz, 40-DIP, NMOS oder CMOS                        |

## Status

### Stand: 21. März 2021
- Firmware angepasst: Ab sofort werden auch CMOS-Varianten des Z80 unterstützt.
- Release V0.5 ist verfügbar.

### Stand: 17. März 2021
- Beta-Testphase gestartet.

### Stand: 16. März 2021
- Beginn der offenen Beta-Phase. Version 0.3 veröffentlicht. Vorschläge und Fragen bitte ins Diskussionstool bei GitHub, Fehler in die Issues.
- Neues Boarddesign mit CP/M-Schriftzug begonnen.
- Interesse an Feedback zu getesteter Hardware und Aufbau.

### Stand: 15. März 2021
- Firmware erstmals in relativ stabilem Zustand. Erste Tests vielversprechend.

![](https://codeberg.org/diwou/Z80-Card_for_Commodore_C64/raw/branch/main/Images/Z80-Card_first_alpha_release.jpg)

- Tests mit alter 407-Platine, C64G mit 469er-Platine, DOS/SpeedDOS/JiffyDOS durchgeführt. Mit Speeder sehr gute Ergebnisse.
- Z80 läuft mit Dot-Clock (8 MHz). Nur PAL getestet.
- Turbo Pascal Mandelbrot-Benchmark zeigt spürbare Verbesserungen.

![](https://codeberg.org/diwou/Z80-Card_for_Commodore_C64/raw/branch/main/Images/Z80-Card_Mandelbrot.jpg)

### Stand: 12. März 2021
- Experiment mit unkonventionellen Platinenformen.

![](https://codeberg.org/diwou/Z80-Card_for_Commodore_C64/raw/branch/main/Images/Z80-Card_design_pcb.jpg)

### Stand: 6. März 2021
- Erste reale Platine bestückt. Einige Komponenten nicht mehr erforderlich dank CPLD.
- Entscheidung für 8 MHz-Version gefallen. 4 MHz unnötig langsam. Kein Konfigurationsaufwand für Nutzer nötig.
- Grundlegende Stabilität erreicht. Waitstates werden noch optimiert.

![](https://codeberg.org/diwou/Z80-Card_for_Commodore_C64/raw/branch/main/Images/z80-card-first_pcb.jpg)

### Stand: 5. März 2021
- 4 MHz-Modus weitgehend stabil. 8 MHz-Modus zu ca. 90 % funktional.
- Mandelbrot-Vergleich: 4 MHz = 7:16 min, 8 MHz = 3:58 min.

### Stand: 3. März 2021
- Stabiler Lauf unter Last. Bootvorgang fehlerfrei.
- Spontane Abstürze bei Inaktivität. Fehleranalyse läuft.
- 8 MHz bringt kaum Ladezeit-Vorteil, aber bei CPU-lastigen Aufgaben klar schneller.

### Stand: 22. Februar 2021
- Erste Prototyp-Platine erstellt und bestellt. Ziel: Signalprobleme beheben, dann Takt erhöhen.
- VIC nicht Hauptursache für CP/M-Probleme. Timingunterschiede zwischen TTL-Typen haben mehr Einfluss.

![](https://codeberg.org/diwou/Z80-Card_for_Commodore_C64/raw/branch/main/Images/Z80-Card_PCB_Prototype.jpg)

### Erster Eintrag
- Erster Aufbau auf Breadboard mit CPLD und Devboard.
- Verdrahtung per Lackdraht. Erste Tests bei 2 MHz erfolgreich.
- Boot von CP/M 2.2 möglich, wenn auch instabil.
- Funktioniert auch in neueren C64-Modellen – anders als das originale CP/M-Modul.

![](https://codeberg.org/diwou/Z80-Card_for_Commodore_C64/raw/branch/main/Images/Z80-Card_first_prototype_1.jpg)

![](https://codeberg.org/diwou/Z80-Card_for_Commodore_C64/raw/branch/main/Images/Z80-Card_first_prototype_2.jpg)

![](https://codeberg.org/diwou/Z80-Card_for_Commodore_C64/raw/branch/main/Images/Z80-Card_first_prototype_3.jpg)

---

## Lizenz

Der Inhalt dieses Repositories, mit Ausnahme der Verzeichnisse „Docs“ und „Software“, steht unter der folgenden Lizenz:

**Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC BY-NC-SA 4.0)**  
Der vollständige Lizenztext befindet sich in der Datei [LICENSE.CC-NC-BY-SA-4.0](https://codeberg.org/diwou/Z80-Card_for_Commodore_C64/raw/branch/main/LICENSE.CC-NC-BY-SA)  
Und ist auch online verfügbar unter: https://creativecommons.org/licenses/by-nc-sa/4.0/
