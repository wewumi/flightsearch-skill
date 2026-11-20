---
name: "flugsuche"
description: "Findet die günstigsten bzw. besten Flüge über Google Flights, Momondo, Kiwi und Trip.com ab den gewünschten Flughäfen im Umkreis (Standard Region Stuttgart) und maximiert die Zeit vor Ort. Nutzen bei jeder Flugsuche, Flugpreis-Frage oder Trip-Idee."
---

# Flugsuche

Dieser Skill macht das, was ein preisbewusster Reisender sonst von Hand tut: Er sucht ab allen sinnvollen Flughäfen in der Umgebung, die der Nutzer bestätigt hat, vergleicht mehrere Portale und stellt die Funde übersichtlich nebeneinander, mit dem Anreiseaufwand als Zusatzinfo. Ziel ist der günstigste *passende* Flug, nicht nur der günstigste Preis auf einem Portal – und bei gleichem Budget der, der die meiste Zeit am Ziel lässt.

Recherche ja, Buchung nein: Der Skill bucht nie, gibt keine persönlichen Daten ein und löst keine Captchas.

## Ablauf im Überblick

1. Eckdaten klären (interaktiv, nur was fehlt) – inkl. Vorschlag für die Abflughäfen – und **alles kurz zusammengefasst bestätigen lassen, erst dann suchen**
2. Flughafen-Suchgruppen bilden → Anhang A
3. Bei offenem Ziel: Ziele vorsortieren (Ziellisten, Google „Erkunden“, Kiwi „anywhere“)
4. Google Flights mit den gewählten Flughäfen (hin und zurück + zwei Nur-Hinflug-Suchen)
5. Momondo und Kiwi mit den gewählten Flughäfen
6. Trip.com für die 2–3 besten Flughafen-Kombinationen
7. Zeit vor Ort maximieren + Spar-Tricks: Nachbar-Zielflughäfen, Gabelflüge/Einzeltickets, Airline-Gegencheck
8. Ergebnisse vereinheitlichen → Buchungslinks für die ersten 5 Flüge → Vergleichsseite veröffentlichen

Details zu Links, Seitenaufbau und Eigenheiten jedes Portals: Anhang B. Lies Anhang B vor der ersten Suche.

## Vorbereitung: Hilfsskripte anlegen

Dieser Skill nutzt drei kleine Python-Skripte (Anhang C, D und E). Lege sie beim ersten Gebrauch in einer Sitzung mit dem Write-Tool unverändert als `scripts/gflights_url.py`, `scripts/build_report.py` und `scripts/book_link.py` in deinem Arbeitsverzeichnis an; alle Befehle unten gehen von diesem Pfad aus. Nur `gflights_url.py` kann Google-Flights-Links mit mehreren Flughäfen bauen – nicht von Hand nachbauen.

---

## 1. Eckdaten klären

Frag mit dem Rückfrage-Tool (Multiple Choice) nur nach dem, was aus der Anfrage nicht hervorgeht. Pro Runde gehen max. 4 Fragen; bei vielen offenen Punkten zwei Runden. Gute Standardwerte stehen als erste Option mit „(Empfohlen)“.

**Runde 1 – die Reise:**
- **Ziel:** fest, Region („Südspanien“) oder offen („irgendwo warm“, „egal, Hauptsache billig“). Viele Nutzer sind beim Ziel flexibel – frag, statt anzunehmen.
- **Reisedaten & Flexibilität:** feste Daten, ±1–3 Tage, ganzer Zeitraum (z. B. „ein Wochenende im März“). Flexibilität immer *vorher* abfragen, weil sie die Suche stark verändert.
- **Personen** – eigene Frage: allein (Standard), zu zweit, 3–4 Erwachsene, mit Kindern (Alter erfragen).
- **Gepäck** – eigene Frage, nicht mit den Personen zusammenlegen: nur kleine Tasche unter dem Sitz (Standard), Handgepäck-Trolley, Aufgabegepäck. Gepäck ändert die Preise bei Kiwi und Low-Cost-Airlines erheblich.

**Runde 2 – Start und Anreise:**
- **Startort** (Region Stuttgart / Schwaikheim). Der Skill wird von verschiedenen Leuten genutzt; wenn der Startort nicht genannt ist und nichts im Kontext darauf hindeutet, frag nach – dann in einer eigenen Runde *vor* der Flughafen-Frage, weil der Vorschlag davon abhängt.
- **Abflughäfen** – mach einen Vorschlag und lass ihn bestätigen oder anpassen (Mehrfachauswahl, Details unten).
- **Anreise zum Flughafen:** Deutschlandticket (Standard), Auto oder beides. Wird als Zusatzinfo angezeigt und fließt in die Machbarkeit ein.
- Optional, nur wenn relevant: Nur-Hinflug, Direktflug-Wunsch, Uhrzeiten (z. B. „Freitag erst ab 15 Uhr“).

**Abflughäfen vorschlagen.** Schlag eine Auswahl vor, die zu Reisedauer und Ziel passt, und frag mit `multiSelect: true`. Biete Gruppen an (nicht 11 Einzelflughäfen), markiere deinen Vorschlag mit „(Empfohlen)“ und nenne in der Beschreibung die Städte, Codes und die Anreise. Für die Region Stuttgart (Details in Anhang A):

| Gruppe | Flughäfen | Anreise mit D-Ticket |
|---|---|---|
| Stuttgart | STR | S3, ~50 min |
| Regionale Low-Cost-Flughäfen | Karlsruhe/Baden-Baden FKB, Memmingen FMM, Friedrichshafen FDH, Nürnberg NUE | ~2 h 15 – 3 h 30 |
| Große Drehkreuze | Frankfurt FRA, München MUC | ICE ~2–3 h 30 (Aufpreis) |
| Grenznah | Zürich ZRH, Basel BSL, Straßburg SXB, Frankfurt-Hahn HHN | ~3–4 h, Hahn nur mit Auto |

Faustregeln für den Vorschlag – erkläre ihn in einem Halbsatz in der Frage:
- **Kurztrip (≤ 3 Nächte):** Stuttgart + regionale Low-Cost-Flughäfen. Lange Anfahrten fressen Zeit am Ziel.
- **Längere Reise, Fernziel oder Langstrecke:** alle Gruppen; Frankfurt/München/Zürich haben die meisten Verbindungen.
- **Ziel offen, „Hauptsache billig“:** Stuttgart + regional + grenznah (Low-Cost-Basen Basel, Memmingen, Karlsruhe).
- Über „Sonstiges“ kann der Nutzer einzelne Flughäfen ergänzen oder streichen; das gilt dann für die ganze Suche.

Gesucht wird nur ab den bestätigten Flughäfen. Merk dir die Wahl für Folgesuchen im selben Gespräch.

**Daten gegen Wochentage prüfen** (z. B. mit `python3 -c "import datetime; print(datetime.date(2027,1,15).strftime('%a'))"`). Passt „15.–18. Januar (Do–So)“ nicht zum Kalender, frag nach, welche Angabe gilt – mit den naheliegenden Varianten als Optionen. Ist niemand da, beide Varianten suchen und den Widerspruch im Ergebnis nennen.

**Vor der Suche bestätigen lassen.** Die Suche dauert mehrere Minuten und läuft durch viele Portale – ein falsches Datum oder ein vergessener Koffer macht alles umsonst. Deshalb fass, nachdem alle Fragen beantwortet sind, die Eckdaten **sehr kurz in Stichpunkten** zusammen und frag mit dem Rückfrage-Tool „Passt das so?“. Die Stichpunkte gehören in den Fragetext selbst, weil der Nutzer während der Frage oft nur den Dialog sieht – Optionen „Ja, los geht's (Empfohlen)“ und „Etwas ändern“. Erst nach „Ja“ mit Schritt 2 beginnen; bei „Etwas ändern“ die Änderung aufnehmen und erneut kurz bestätigen lassen.

Beispiel für die Zusammenfassung (Städtenamen statt nur Codes):
- **Wann:** 26.–31.12. oder 1.–10.1., 2–5 Nächte
- **Wer:** 2 Erwachsene, nur kleine Tasche
- **Wohin:** egal, Hauptsache günstig
- **Ab:** Stuttgart, Karlsruhe/Baden-Baden, Memmingen, Friedrichshafen, Nürnberg
- **Anreise:** Deutschlandticket

Wenn der Nutzer keine Zeit für Fragen hat oder nicht antwortet (z. B. geplante Ausführung): Standardwerte und den Flughafen-Vorschlag nehmen, ohne Bestätigung suchen und die Annahmen oben im Ergebnis nennen.

## 2. Flughäfen festlegen

Nimm die in Schritt 1 bestätigten Flughäfen und teile sie in Suchgruppen zu max. 7 Codes (Google Flights und Momondo vertragen nicht mehr). Bei allen 11 Flughäfen der Region Stuttgart sind das:
- Gruppe A: `STR,FKB,FDH,FMM,NUE,FRA,MUC`
- Gruppe B: `ZRH,BSL,SXB,HHN`

Bei ≤ 7 gewählten Flughäfen reicht eine Gruppe. Die Anreise steht im Ergebnis bei jeder Option, damit der Nutzer abwägen kann, ob sich ein weiterer Weg lohnt. Bei anderen Startorten: Flughäfen im Umkreis von ~3 Stunden selbst ermitteln (Anhang A, Abschnitt 2) und daraus den Vorschlag in Schritt 1 bauen.

Ergänze bei festem Ziel passende **Nachbar-Zielflughäfen** (z. B. Barcelona → BCN, GRO, REU) aus Anhang A, Abschnitt 3.

## 3. Ziel offen? Erst vorsortieren

Wenn das Ziel offen oder eine Region ist:
1. **Hat der Wunsch einen Charakter** („warm“, „Strand“, „Städtetrip“, „Skandinavien“)? Dann nimm die passende Zielliste aus Anhang A, Abschnitt 4 und suche sie direkt als Ziel-Flughäfen in Google Flights (max. 7 pro Seite, also ggf. mehrere Suchen). Das ist zuverlässiger als die Erkunden-Karte, die nur Ziele im sichtbaren Kartenausschnitt zeigt (im Test fehlten Kanaren, Ägypten, Marokko komplett).
2. **Ergänzend / bei „egal wohin, Hauptsache billig“:** Kiwi „anywhere“ als Kachel-Übersicht (respektiert Datumsbereiche und Nächte, siehe Anhang B) und Google Flights „Erkunden“ ab den gewählten Flughäfen: `python3 scripts/gflights_url.py --from <Suchgruppe> --explore` (eine Suche pro Gruppe). Die Daten aus dem Link übernimmt Google nicht zuverlässig – im Datumsfeld der Seite die Reisedaten immer setzen und bestätigen (zeigt die Seite danach „Es ist ein Fehler aufgetreten“, einmal neu laden).
3. Die 3–7 günstigsten, zum Wunsch passenden Ziele auswählen und ehrlich einordnen (z. B. „Málaga im Januar ~17 °C – mild, nicht warm; richtig warm sind die Kanaren ab ~195 €“). Shortlist kurz nennen, dann die Schritte 4–7 für die Top-Ziele durchführen – mehrere Ziele passen gemeinsam in eine `--to`-Liste.

## 4. Google Flights (Ausgangspunkt)

Links mit `scripts/gflights_url.py` bauen – nur dieser Weg erlaubt mehrere Flughäfen in einem Link (eine Suche pro Gruppe aus Schritt 2):
```bash
python3 scripts/gflights_url.py --from STR,FKB,FDH,FMM,NUE,FRA,MUC --to BCN,GRO,REU --depart 2026-12-05 --return 2026-12-12
python3 scripts/gflights_url.py --from ZRH,BSL,SXB,HHN --to BCN,GRO,REU --depart 2026-12-05 --return 2026-12-12
```
**Zusätzlich immer die zwei Nur-Hinflug-Suchen** (hin am Abreisetag: Heimatflughäfen → Ziele; zurück am Rückreisetag: Ziele → Heimatflughäfen). Die Hin-und-Rück-Liste zeigt nur die Hinflugzeiten; die Einzelsuchen liefern alle Rückflugzeiten auf einen Blick, sind die Grundlage für Gabelflüge und für die Zeit-vor-Ort-Optimierung in Schritt 7. Das ist schneller und zuverlässiger, als Einträge anzuklicken.

**Viele Daten (flexible Zeiträume):** Pro Reisetag eine Nur-Hinflug-Suche hin und eine zurück. Das geht schnell, wenn du 4 Tabs anlegst (`tabs_create`), in einem `browser_batch` alle 4 per `navigate` mit eigener `tabId` lädst, einmal ~18 s wartest und dann in einem zweiten `browser_batch` alle 4 mit diesem `javascript_tool`-Snippet kompakt ausliest (ein Batch darf max. ~50 s dauern, daher Laden und Auslesen trennen):
```js
'03.01.\n'+[...new Set([...document.querySelectorAll('li')].map(l=>l.innerText.replace(/\n+/g,' ')).filter(t=>/€/.test(t)&&/\d:\d\d/.test(t)&&!/, am /.test(t)))].slice(0,16).map(t=>t.replace(/Durchschn.*$|CO2e.*$|Durchgeführt von /,'').slice(0,72)).join('\n')
```
Ergebnis pro Zeile: „17:25 FMM 19:25 TIA 15 € Nonstop 2 Std. Ryanair“. Der Hinweis „Preise … für 2 Erwachsene“ zeigt: Google-Preise gelten für alle Reisenden zusammen. Danach aus allen Tagen die besten Hin-/Rück-Kombinationen bilden (Preis, Zeit vor Ort, Machbarkeit).

Seite im Browser öffnen, ~20 s warten, mit `get_page_text` auslesen. Erst werten, wenn kein „Ergebnisse werden abgerufen“ / Prozent-Fortschritt mehr da ist – während des Ladens zeigt Google teils „ab“-Preise, die danach verschwinden. Notiere pro Gruppe die ~8 günstigsten Flüge (Preis, Flughäfen, Zeiten, Stopps, Airline) und jede Datums-Empfehlung („Im Zeitraum … für … € fliegen“). Bei flexiblen Daten zusätzlich Preisgrafik/Kalender öffnen.

## 5. Momondo und Kiwi

Beide nehmen mehrere Flughäfen pro Link (Formate in Anhang B). Momondo deckt viele Buchungsportale ab; Kiwi findet Kombinationen aus getrennten Tickets, die sonst niemand zeigt.

Momondo verträgt nur ~7 Abflughäfen pro Link – dieselben Suchgruppen wie bei Google verwenden. Momondo zeigt Preise **pro Person**. Kiwi schafft alle in einem Link.

Kiwi-Funde mit Self-Transfer sind erlaubt, werden aber **immer als Risiko markiert** (kein Anschlussschutz bei Verspätung). Wenn Kiwi nur wenige Euro günstiger ist als derselbe Flug bei der Airline, ist die Airline die bessere Empfehlung.

## 6. Trip.com gezielt nachprüfen

Trip.com kann nur einen Abflughafen pro Suche. Prüfe deshalb die 2–3 besten Flughafen-Kombinationen aus den Schritten 4–5. Trip.com ist für exakt denselben Flug manchmal günstiger – genau das soll dieser Schritt finden. Preise dort gelten pro Person.

## 7. Zeit vor Ort und Spar-Tricks

**Zeit vor Ort maximieren.** Bei gleichem oder ähnlichem Preis gewinnt der Flug, der mehr Zeit am Ziel lässt. Gerade bei Kurztrips entscheidet das über den Wert der Reise: Ein Rückflug am Sonntag um 06:40 macht aus einem Wochenende einen einzigen Tag.
- Rechne für jede Option die **Zeit vor Ort** aus (Landung am Ziel bis Abflug zurück, z. B. „Fr 19:25 – So 15:50 ≈ 44 h“) und trag sie ins Ergebnis ein (`stay`, z. B. „3 Nächte · ≈ 68 h“).
- Kombiniere aus den Nur-Hinflug-Suchen gezielt: frühester sinnvoller Hinflug + spätester Rückflug, mit dem man noch nach Hause kommt (Machbarkeit, Schritt 8). Ein anderer Rückflughafen (Gabelflug) bringt oft Stunden – z. B. Rückflug nach Stuttgart am Nachmittag statt nach Memmingen am Morgen.
- Nenne im Fazit den Tausch ausdrücklich, wenn er sich lohnt: „+30 € für 2 Nächte mehr“.
- Den Nutzer nicht in frühe Abflüge zwingen, die er nur mit Hotelnacht erreicht; Anreise und Heimfahrt gehören mit zur Rechnung.

**Spar-Tricks:**
- **Nachbar-Zielflughäfen:** schon in Schritt 2 in die Zielliste aufgenommen. Transferzeit zur Stadt im Ergebnis nennen.
- **Gabelflüge / Einzeltickets:** Die zwei Nur-Hinflug-Suchen (hin: gewählte Abflughäfen → Ziel; zurück: Ziel → gewählte Heimatflughäfen) gegen das günstigste *praktikable* Hin- und Rückflug-Ticket rechnen. Zeigen, wenn es ≥ ~10 € spart, die Anreise deutlich verkürzt oder mehr Zeit vor Ort bringt (z. B. hin ab Memmingen, zurück nach Stuttgart). Kiwi und Momondo mischen Flughäfen teils schon selbst.
- **Andere Daten:** Wenn der Nutzer flexibel ist, die günstigsten Datumsalternativen aus Google/Kiwi/Trip.com sammeln (Preisleisten).
- **Airline-Gegencheck:** Für den Gewinner kurz den Preis direkt bei der Airline prüfen. Bei gleichem Preis die Airline empfehlen (einfachere Umbuchung, Erstattung).
- **Skyscanner** blockiert automatisierte Browser – nur den fertigen Link zum Selbstprüfen beilegen.

Browser-Hinweise: Ergebnislisten mit `get_page_text` oder dem JS-Snippet lesen, nicht per Screenshot. Wenn doch ein Klick auf einen Eintrag nötig ist, funktioniert ein Klick per `ref` oft nicht; dann einen Screenshot machen und per Koordinate klicken, oder im Hintergrund-Tab per `javascript_tool` das Listenelement anklicken. Arbeiten mehrere Suchen parallel, jede in einem eigenen Tab (`tabs_create`) mit expliziter `tabId`. Am Ende die eigenen Tabs schließen.

Zeitbudget: Wenn ein Portal hängt, umleitet oder blockiert, nach zwei Versuchen weitermachen und es im Ergebnis vermerken. Ein vollständiger Vergleich über vier Portale ist wertvoller als Perfektion auf einem.

## 8. Ergebnis

**Machbarkeit prüfen.** Die Anreise ist Zusatzinfo, aber ein Flug, den man realistisch nicht erreicht, ist kein Deal. Typische Fallen: Abflug vor ~8 Uhr an einem Flughafen > 1,5 h entfernt (mit Bahn nicht erreichbar, ggf. Hotelnacht), Rückkunft nach ~22 Uhr ohne Bahnanschluss, Frankfurt-Hahn ohne Auto, Self-Transfer mit < 2 h Umstieg. Solche Optionen bleiben in der Liste, bekommen das Flag `nachtflug` bzw. eine Notiz mit dem Grund – aber nicht die Empfehlung.

**Vereinheitlichen,** bevor du vergleichst – sonst gewinnt ein Scheinangebot:
- Gleiche Personenzahl und gleiches Gepäck. Wenn ein Preis ohne das gewünschte Gepäck ist, den Aufpreis schätzen oder klar vermerken.
- Hin- und Rückflug immer als Gesamtpreis für alle Reisenden; pro Person in `price_note` (Momondo, Trip.com und Ryanair zeigen Preise pro Person – umrechnen).
- Denselben Flug von mehreren Portalen nur einmal listen, mit dem günstigsten Preis und der Quelle; die anderen Preise in die Notiz.

**Günstigster ab dem nächsten Flughafen.** Viele wollen wissen, was es „ohne Anfahrt“ kostet. Nimm deshalb immer die günstigste Option ab dem nächstgelegenen Flughafen (bei Region Stuttgart: STR, hin und zurück) in die Liste auf – auch wenn sie teurer ist; such sie notfalls gezielt. Setze `search.home_airport` (z. B. `"STR"`): Die Seite markiert diese Option automatisch als „Günstigster ab Stuttgart“ und zeigt sie als eigene Karte neben der Empfehlung.

**Städte statt Codes.** Schreib in `verdict`, `note`, `access` und im Chat immer Städtenamen („ab Memmingen“, „zurück nach Karlsruhe/Baden-Baden“), Codes höchstens in Klammern. Die Seite setzt bei Strecken automatisch den Stadtnamen vor den Code (Tabelle `AIRPORTS` in `build_report.py`); für Flughäfen, die dort fehlen, `from_name`/`to_name` mitgeben.

**Buchungslinks für die ersten 5 Flüge** (nach Preis sortiert) sowie für die Empfehlung und den Günstigsten ab dem nächsten Flughafen. Feld `book`: eine Liste mit Label und URL – bei Gabelflügen/Einzeltickets ein Link pro Strecke („Hinflug bei Ryanair“, „Rückflug (Wizz Air)“). Links mit `scripts/book_link.py` bauen:
```bash
python3 scripts/book_link.py --airline ryanair --from FMM --to ZAG --date 2026-12-28 --adults 2                 # Ryanair direkt
python3 scripts/book_link.py --airline ryanair --from FMM --to KRK --date 2027-01-07 --return-date 2027-01-09 --adults 2
python3 scripts/book_link.py --airline wizz --from TIA --to STR --date 2026-12-31 --adults 2                    # alle anderen: Google Flights genau diese Strecke
```
Ryanair-Links öffnen direkt die Flugauswahl des Tages (getestet). Für alle anderen Airlines liefert das Skript eine Google-Flights-Suche genau für diese Strecke und dieses Datum; ein Klick auf den Flug zeigt dort die Buchungsoptionen inkl. Airline-Website. Wurde der beste Preis bei einem Portal gefunden (Momondo, Trip.com, Kiwi), zusätzlich dessen Link als Buchungsoption angeben. Nie selbst buchen.

**Vergleichsseite bauen:** Ergebnisse als JSON speichern (Schema im Kopf von `scripts/build_report.py`), dann:
```bash
python3 scripts/build_report.py results.json flugvergleich.html
```
Die Datei mit dem Artifact-Tool veröffentlichen (Icon `plane`, eine Satz-Beschreibung mit Route und Zeitraum). Wenn das Artifact-Tool fehlt, die Datei als Anhang senden.

Inhalt, auf den es ankommt:
- `verdict`: 1–3 Sätze Empfehlung – welcher Flug, warum, ob sich der weitere Weg lohnt (z. B. „Nürnberg spart 25 € gegenüber Stuttgart, kostet aber 3 h 30 Anreise pro Richtung“) und was ein paar Euro mehr an Zeit vor Ort bringen.
- `options`: 5–12 Flüge, günstigste zuerst, mit Anreise-Info, Zeit vor Ort (`stay`), Buchungslinks (`book`) und Flags. Markiere genau eine Option mit `"recommended": true` – den besten Flug unter Berücksichtigung von Preis, Zeit vor Ort *und* Machbarkeit. Die Seite hebt sie hervor und zeigt daneben den absolut günstigsten Preis und den Günstigsten ab dem nächsten Flughafen. Flags: `self-transfer`, `gabelflug`, `nachbarflughafen`, `anderes-datum`, `trip-guenstiger`, `einzeltickets`, `nachtflug`.
- `portals`: günstigster Preis je Portal inkl. Skyscanner-Link.
- `date_tips`: günstigere Daten, wenn gefunden.
- `tips`: Hinweise wie Gepäckaufpreise, Online-Check-in, Trip.com-Kundenservice, früher Abflug ohne Bahnverbindung.

Die Seite ist bewusst ruhig und klar gestaltet (helle Flächen, Systemschrift, große Preise, Karten statt Tabellen, Hell- und Dunkelmodus) und funktioniert auch auf dem Handy – nichts am Layout von Hand nachbauen, nur das JSON füllen.

Im Chat danach nur kurz: bester Preis, Flug (mit Städtenamen), Zeit vor Ort, Quelle – Details stehen auf der Seite.

---

# Anhang A: Flughäfen & Anreise

### Inhalt
1. Region Stuttgart / Rems-Murr (Standard-Startort Schwaikheim)
2. Andere Startorte
3. Alternative Zielflughäfen (Nachbar-Flughäfen)
4. Ziellisten nach Reisewunsch

Alle Zeiten und Preise sind grobe Richtwerte (Stand 2026). Sie dienen als Zusatzinfo neben dem Flugpreis, nicht als Ranking-Kriterium. Wenn eine Anreise für die Entscheidung kippt (z. B. Abflug 6:00 ab München mit Bahn-Anreise unmöglich), erwähne das ausdrücklich.

---

### 1. Region Stuttgart – ab Schwaikheim (Rems-Murr-Kreis, S3)

Schwaikheim liegt an der S-Bahn-Linie S3, die ohne Umstieg bis zum Flughafen Stuttgart fährt.

| Code | Flughafen | Auto (Fahrzeit · Parken) | Bahn mit Deutschlandticket | Bahn schneller (Aufpreis) | Typische Airlines |
|---|---|---|---|---|---|
| STR | Stuttgart | ~35 min · P ab ~8–15 €/Tag (online vorbuchen) | ~50 min, S3 direkt, 0 € | – | Eurowings, Vueling, Wizz Air, Ryanair (wenig), Condor, SunExpress, Pegasus, LH-Zubringer |
| FKB | Karlsruhe/Baden-Baden | ~1 h 15 · P ~6–10 €/Tag | ~2 h 15 (Zug bis Baden-Baden + Bus 285), 0 € | – | Ryanair, Wizz Air, Eurowings, Sundair |
| FDH | Friedrichshafen | ~2 h · P ~8 €/Tag | ~3 h (über Ulm, Bahnhof direkt am Flughafen), 0 € | – | Ryanair, Pegasus, Corendon, Charter |
| FMM | Memmingen | ~1 h 45 · P ~5–8 €/Tag | ~2 h 45 (bis Memmingen + Bus ~10 min), 0 € | – | Ryanair, Wizz Air, SunExpress |
| NUE | Nürnberg | ~2 h 15 · P ~10 €/Tag | ~3 h 30 Regio, 0 € | IC ~2 h 10 + U2, ~25–50 € | Eurowings, Ryanair, Vueling, Condor, Corendon |
| FRA | Frankfurt | ~2 h · P ~20–30 €/Tag | ~4 h Regio, 0 € | ICE ~1 h 50 ab Waiblingen (Sparpreis ~20–50 €) | Lufthansa, Condor, fast alle Langstrecken |
| MUC | München | ~2 h 45 · P ~20–30 €/Tag | ~5 h Regio, 0 € | ICE bis Hbf + S8 ~3 h 20 (~25–60 €) | Lufthansa, Eurowings, Vueling, Langstrecke |
| ZRH | Zürich | ~2 h 30 · P ~25–35 CHF/Tag, Vignette nötig | D-Ticket bis Singen, dann CH-Ticket (~25–35 CHF) ~3 h 30 | IC Stuttgart–Zürich ~3 h + 10 min (Sparpreis ~20–40 €) | SWISS, Edelweiss, easyJet (wenig) |
| BSL | Basel-Mulhouse (EuroAirport) | ~2 h 45 · P ~15 €/Tag | ~4 h bis Basel Bad Bf, dann Bus 50 (~5 CHF) | ICE bis Basel ~2 h 45 + Bus | easyJet (große Basis), Wizz Air |
| SXB | Straßburg | ~1 h 45 · P ~10 €/Tag | ~3 h 30 (über Kehl/Offenburg) | – | Volotea, wenige Linien |
| HHN | Frankfurt-Hahn | ~3 h · P ~5 €/Tag | schlecht erreichbar (Fernbus ab Frankfurt) | – | Ryanair |

#### Suchgruppen für Google Flights (max. 7 Flughäfen pro Seite)
- **Gruppe A (Standard):** `STR,FKB,FDH,FMM,NUE,FRA,MUC`
- **Gruppe B (Grenznah/Low-Cost):** `ZRH,BSL,SXB,HHN`

Welche Flughäfen durchsucht werden, entscheidet der Nutzer in Schritt 1 (Vorschlag + Bestätigung); daraus die Suchgruppen bilden. Bei Kurztrips (≤ 3 Nächte) fällt ein langer Anfahrtsweg stärker ins Gewicht – sag das im Fazit.

---

### 2. Andere Startorte

Der Skill wird von verschiedenen Leuten genutzt. Wenn der Startort nicht in der Region Stuttgart liegt:
1. Ermittle alle Flughäfen mit Linienflügen im Umkreis von ca. 3 Stunden (Auto oder Bahn). Denke auch an grenznahe Flughäfen im Ausland (z. B. ab Freiburg: BSL, ZRH, SXB; ab Saarbrücken: LUX, SCN, FRA, HHN; ab Köln: CGN, DUS, DTM, EIN, MST, NRN, BRU-CRL).
2. Biete sie in Schritt 1 als 3–4 sinnvolle Gruppen zur Auswahl an (nah, regional/Low-Cost, Drehkreuze, grenznah) und teile die bestätigten in Suchgruppen zu max. 7 Codes auf.
3. Schätze die Anreise analog zur Tabelle oben (Fahrzeit Auto, Bahn mit/ohne Deutschlandticket). Kennzeichne Schätzungen mit „~“.
4. Setze `home_airport` auf den nächstgelegenen Flughafen.

---

### 3. Alternative Zielflughäfen

Low-Cost-Airlines fliegen oft einen Nachbarflughafen an. Nimm passende Alternativen direkt mit in die `--to`-Liste (Google Flights) bzw. mehrere Ziele in Kiwi/Momondo, und gib im Ergebnis den Transfer zur Zielstadt an.

| Zielstadt | Alternativen (Transfer in die Stadt) |
|---|---|
| Barcelona BCN | Girona GRO (~1 h 15 Bus), Reus REU (~1 h 30 Bus) |
| Mailand | MXP, LIN, Bergamo BGY (~1 h Bus) |
| London | LHR, LGW, STN, LTN, LCY, Southend SEN |
| Paris | CDG, ORY, Beauvais BVA (~1 h 15 Bus) |
| Rom | FCO, Ciampino CIA |
| Venedig | VCE, Treviso TSF (~1 h Bus) |
| Brüssel | BRU, Charleroi CRL (~1 h Bus) |
| Oslo | OSL, Torp TRF (~1 h 50 Bus) |
| Stockholm | ARN, Skavsta NYO (~1 h 20 Bus), BMA |
| Kopenhagen | CPH, Malmö MMX (~1 h) |
| Wien | VIE, Bratislava BTS (~1 h Bus) |
| Nizza | NCE, (Genua GOA ~2 h 30) |
| Lissabon | LIS (Porto OPO als Zweitziel, ~3 h Zug) |
| Malaga / Costa del Sol | AGP, Almería LEI, Granada GRX, Sevilla SVQ |
| Mallorca | PMI (keine Alternative, aber oft viele Abflughäfen vergleichen) |
| Teneriffa | TFS (Süd), TFN (Nord) |
| Neapel | NAP, (Rom FCO ~1 h 10 Zug) |
| Krakau | KRK, Kattowitz KTW (~1 h 15 Bus) |
| New York | JFK, EWR, LGA |

Wenn das Ziel nur als Region bekannt ist („irgendwo Südspanien“, „Balkan“), nimm die wichtigsten 3–7 Flughäfen der Region als Zielliste.

---

### 4. Ziellisten nach Reisewunsch

Für Wünsche wie „irgendwo warm“ oder „Städtetrip“ diese Listen als `--to` in Google Flights verwenden (max. 7 Codes pro Suche → mehrere Suchen) und als `to=` bei Kiwi. Temperaturen = typische Tageshöchstwerte; ehrlich einordnen („mild“ vs. „warm“).

| Wunsch | Saison | Zielflughäfen | Typisch |
|---|---|---|---|
| Wirklich warm (≥ 20 °C) | Nov–März | Kanaren: TFS, TFN, LPA, ACE, FUE · Madeira FNC · Ägypten: HRG, SSH, RMF · Marokko: AGA, RAK · Kapverden SID | 20–26 °C; ab Region Stuttgart meist ab ~150–250 € |
| Mild (15–19 °C), günstig | Nov–März | Südspanien: AGP, ALC, SVQ, LEI · Malta MLA · Zypern: LCA, PFO · Algarve FAO · Lissabon LIS · Sizilien: PMO, CTA · Kreta HER | 15–19 °C; oft Low-Cost ab FMM, FKB, NUE, BSL |
| Sommer-Strand | Mai–Okt | PMI, IBZ, AGP, ALC, FAO, HER, RHO, KGS, CFU, SPU, DBV, BJV, AYT, NAP, OLB, CAG | |
| Städtetrip Europa | ganzjährig | BCN, LIS, OPO, FCO/CIA, NAP, BUD, PRG, KRK, VIE, CPH, AMS, DUB, LON (STN/LTN/LGW), PAR (ORY/CDG/BVA), MAD, VLC, ATH | |
| Skandinavien/Norden | ganzjährig | CPH, ARN/NYO, OSL/TRF, BGO, HEL, KEF, RVN/TOS (Polarlicht im Winter) | |
| Balkan/Osteuropa günstig | ganzjährig | SPU, DBV, ZAG, TIA, SKP, SOF, OTP, BEG, KTW, GDN, WRO, TGD, PRN | Oft < 50 € ab FMM/FKB (Ryanair, Wizz Air) |

---

# Anhang B: Portale

Getestet im Oktober 2026 im Claude-Browser. Wenn sich ein Linkformat geändert hat, nutze die Suchmaske der Seite und notiere die Abweichung im Ergebnis.

### Allgemein
- **Lesen statt gucken:** Nutze `get_page_text` (bzw. `read_page` oder das JS-Snippet aus Schritt 4) zum Auslesen der Ergebnisse. Screenshots nur, wenn ein Klickziel gesucht wird.
- **Ladezeit:** Ergebnisse kommen nachträglich per JavaScript. Nach dem Laden ~20 s warten (Google, Momondo, Trip.com brauchten im Test 20–30 s); wenn noch „Ergebnisse werden abgerufen“ / „Preise werden abgerufen“ / „x % abgeschlossen“ steht, nochmal 10 s warten und erneut lesen. Zwischenstände („ab 93 €“ während des Ladens) nicht übernehmen.
- **Cookie-Banner:** Immer „Alle ablehnen“ wählen (Google: consent.google.com → „Alle ablehnen“; Kiwi: „Alle ablehnen“). Danach den Suchlink erneut laden, falls auf die Startseite umgeleitet wurde.
- **Zugriffsfreigaben:** Fordere Seitenzugriff mit Scope `site` an (google.com, consent.google.com, kiwi.com, trip.com, momondo.de, ryanair.com), damit der Nutzer nicht bei jedem Seitenaufruf neu bestätigen muss.
- **Captchas:** Niemals lösen. Seite überspringen, dem Nutzer den Link geben.
- **Niemals buchen** und keine persönlichen Daten eingeben. Der Skill recherchiert nur.
- **Datumsformat:** in allen Links `YYYY-MM-DD`, außer Skyscanner (`YYMMDD`).

---

### 1. Google Flights (immer zuerst)

**Link bauen** mit `scripts/gflights_url.py` (mehrere Flughäfen in einer Suche – der normale `?q=`-Link kann nur einen Abflughafen):

```bash
python3 scripts/gflights_url.py --from STR,FKB,FDH,FMM,NUE,FRA,MUC --to BCN,GRO,REU --depart 2026-12-05 --return 2026-12-12
python3 scripts/gflights_url.py --from ZRH,BSL,SXB,HHN --to BCN,GRO,REU --depart 2026-12-05 --return 2026-12-12
# Nur Hinflug: --return weglassen. Personen: --adults 2 --children 1. Nur Direktflüge: --max-stops 0
# Ziel offen: --explore (Erkunden-Ansicht, siehe unten)
```

**Ergebnisse lesen:** Die Seite zeigt „Günstigste Hinflüge“ bzw. „Beste Flüge“ und darunter „Andere Hinflüge“. Jeder Eintrag: Abflugzeit, Abflughafen, Ankunftszeit, Zielflughafen, Preis, „Hin und zurück“, Stopps, Dauer, Airline. Bei Hin- und Rückflug ist der angezeigte Preis der **Gesamtpreis** (für alle Reisenden); die Rückflug-Zeiten sieht man erst nach Klick auf den Hinflug. Statt zu klicken: die zwei Nur-Hinflug-Suchen machen (Schritt 4), die zeigen alle Rück-Zeiten auf einen Blick.

**Datums-Hinweise mitnehmen:** Google zeigt Zeilen wie „Im Zeitraum 6.–13. Dez. für 79 € fliegen“. Diese immer ins Ergebnis übernehmen. Wenn der Nutzer flexibel ist: auf „Preisgrafik“ bzw. „Kalender“ klicken und die günstigsten Kombinationen ablesen.

**Gepäck:** Google-Preise sind ohne Aufgabegepäck. Für Handgepäck-Trolley/Koffer den Filter „Gepäckstücke“ setzen oder im Ergebnis vermerken, dass bei Low-Cost-Airlines Aufpreis anfällt.

**Erkunden (Ziel flexibel):** `--explore` öffnet die Liste/Karte „beliebiges Ziel“ ab den angegebenen Flughäfen. Ohne Datum zeigt Google einwöchige Reisen in den nächsten 6 Monaten mit Preis und Zeitraum. Daten aus dem Link werden ignoriert: im Datumsfeld der Seite „Genaue Reisedaten“ wählen, Daten prüfen und mit „Fertig“ bestätigen; bei „Es ist ein Fehler aufgetreten“ die Seite einmal neu laden. Liste per `get_page_text` auslesen (Stadt, Preis, Stopps, Dauer).
Achtung: Die Liste enthält nur Ziele im sichtbaren Kartenausschnitt (meist Mitteleuropa). Für entfernte Regionen (Kanaren, Nordafrika, Nahost) stattdessen eine normale Suche mit einer Zielliste aus Anhang A, Abschnitt 4 machen.

---

### 2. Momondo (Metasuche, mehrere Flughäfen pro Link)

```
https://www.momondo.de/flight-search/STR,FKB,FDH,FMM,NUE,FRA,MUC-BCN,GRO,REU/2026-12-05/2026-12-12?sort=price_a
```
- **Max. ~7 Abflughäfen pro Link** – mit allen 11 leitet Momondo auf die Startseite um (`errorOccurred=true`). Dieselben Suchgruppen wie bei Google verwenden.
- Bei mehreren Zielflughäfen *verschiedener Städte* kombiniert Momondo hin und zurück frei (hin Málaga, zurück Malta) – solche Scheinangebote aussortieren; Nachbarflughäfen derselben Stadt (BCN/GRO) sind okay.
- Nur Hinflug: zweites Datum weglassen. Personen: `/2adults` nach den Daten anhängen (z. B. `/2026-12-05/2026-12-12/2adults?sort=price_a`). Preise stehen dann **pro Person** da.
- Ergebnisse laden progressiv („50 % abgeschlossen“) – warten bis fertig.
- Zeigt kombinierte Hin/Rück-Flughäfen und „Eigenständiger Transfer“ (= Self-Transfer, Risiko markieren).
- Anzeigen („Dies ist eine bezahlte Anzeige“, „Werbung“) ignorieren.
- Der Preis führt zu einem Buchungsportal; notiere welches (Airline direkt ist am verlässlichsten).

---

### 3. Kiwi.com (Self-Transfer-Kombis, Gabelflüge)

Deep-Link (mehrere Flughäfen kommagetrennt, leitet automatisch auf die Ergebnisseite weiter):
```
https://www.kiwi.com/deep?from=STR,FKB,FDH,FMM,NUE,FRA,MUC&to=BCN,GRO,REU&departure=2026-12-05&return=2026-12-12&lang=de&currency=EUR
```
- Nur Hinflug: `return` weglassen. Ziel offen: `to=anywhere` (Kachel-Übersicht „ab X €“ je Ziel mit Abflughafen). Alle 11 Abflughäfen in einem Link funktionieren.
- **Flexible Daten + Nächte** (Kachel-Übersicht, ideal für „geht in diesem Zeitraum, 2–5 Nächte“): `https://www.kiwi.com/de/search/tiles/<Abflug-Slugs>/anywhere/2026-12-26_2026-12-29/2-5?adults=2&currency=eur` – Slugs wie `flughafen-stuttgart-stuttgart-deutschland`, `flughafen-memmingen-memmingen-deutschland`, `flughafen-karlsruhe-baden-baden-karlsruhe-deutschland`, `flughafen-friedrichshafen-friedrichshafen-deutschland`, `flughafen-nurnberg-nurnberg-deutschland` (kommagetrennt). Preise für alle Reisenden zusammen. Achtung: Die Rückreise kann nach dem Zeitraumende liegen – nur zur Vorauswahl der Ziele nutzen, die genauen Flüge dann mit Google-Einzelsuchen pro Tag.
- Falls auf die Startseite umgeleitet wird: Cookie-Banner ablehnen und Link erneut laden.
- Kiwi-Kombis mit sehr frühen Abflügen (06:xx ab Memmingen/Nürnberg/Frankfurt) sind mit Bahn nicht erreichbar – als unpraktisch markieren.
- Kiwi mischt automatisch Flughäfen (hin ab München, zurück nach Stuttgart) – das ist erwünscht (Gabelflug), Anreise beider Flughäfen angeben.
- Die Leiste oben zeigt Preise für benachbarte Zeiträume (z. B. „6. Dez. – 13. Dez. 79 €“) – bei flexiblen Daten übernehmen.
- Standard ist 0 Gepäckstücke. Bei Kiwi ist im Preis oft nicht einmal ein Trolley drin; Gepäck in der Leiste („1 · 0“) einstellen, wenn der Nutzer mehr als ein kleines Handgepäck hat.
- **Self-Transfer** („Selbst-Transfer“, „Self-Transfer“, getrennte Tickets, Flughafenwechsel) im Ergebnis immer als Risiko markieren: kein Anschlussschutz bei Verspätung, ggf. Gepäck neu aufgeben. „Kiwi.com Guarantee“ kostet extra.
- Kiwi-Preise können im Checkout steigen (Servicegebühren). Wenn Kiwi nur wenige Euro günstiger ist als derselbe Flug direkt bei der Airline, empfiehl die Airline.

---

### 4. Trip.com (gezielte Nachprüfung)

Nur **ein** Abflug- und Zielort pro Link (weitere Codes werden ignoriert). Daher: erst Google/Momondo/Kiwi auswerten, dann die 2–3 besten Flughafen-Kombinationen hier gegenprüfen.
```
https://de.trip.com/flights/showfarefirst?dcity=str&acity=bcn&ddate=2026-12-05&rdate=2026-12-12&triptype=rt&class=y&quantity=1&locale=de-DE&curr=EUR
```
- Nur Hinflug: `triptype=ow` und `rdate` weglassen. Personen: `quantity=2` (Preise bleiben pro Person).
- Oben steht eine Preisleiste für benachbarte Zeiträume (gut für flexible Daten).
- Der Preis in der Liste gilt für „Hin- und Rückflug“ gesamt. Links gibt es Filter „Gepäck → Handgepäck / Aufgabegepäck“ mit Preisen.
- Trip.com ist manchmal für exakt denselben Flug günstiger als die Airline – genau das ist der Grund für diesen Check. Kundenservice bei Umbuchungen ist schwächer; das im Ergebnis kurz erwähnen, wenn Trip.com der Gewinner ist.

---

### 5. Skyscanner (nur als Link)

Skyscanner blockiert automatisierte Browser mit einem Captcha („Bist du ein Mensch oder ein Roboter?“). Nicht lösen. Gib dem Nutzer stattdessen den fertigen Link zum Selbstprüfen:
```
https://www.skyscanner.de/transport/fluge/str/bcn/261205/261212/?adultsv2=1&cabinclass=economy&rtn=1
```

---

### 6. Airline direkt (Gegencheck für den Favoriten)

Wizz Air hat keinen funktionierenden Deep-Link (getestet: Fehlerseite) – dafür den Google-Flights-Link aus `book_link.py` nehmen.

Für den Gewinner kurz prüfen, ob die Airline selbst gleich viel oder weniger kostet (sicherer bei Änderungen/Ausfällen). Typische Seiten: ryanair.com, wizzair.com, eurowings.com, vueling.com, easyjet.com, lufthansa.com, condor.com. Wenn das zu lange dauert oder die Seite blockiert: überspringen und im Ergebnis als „Airline-Preis nicht geprüft“ vermerken.

Ryanair-Deep-Link (funktioniert hin/zurück und als Nur-Hinflug mit `isReturn=false&dateIn=`; zeigt Preis **pro Person** und Restplätze „x Platz zu diesem Preis übrig“). Am einfachsten mit `scripts/book_link.py` bauen:
```
https://www.ryanair.com/de/de/trip/flights/select?adults=1&teens=0&children=0&infants=0&dateOut=2026-12-11&dateIn=2026-12-13&isConnectedFlight=false&discount=0&isReturn=true&promoCode=&originIata=FMM&destinationIata=TIA
```
Restplätze im Ergebnis erwähnen. Ryanair-Hinweise für `tips`: Basic-Tarif = nur kleine Tasche unter dem Sitz; Online-Check-in Pflicht (sonst 55 € Gebühr), nur digitale Bordkarten.

---

### 7. Gabelflüge / Einzeltickets

Lohnt sich vor allem, wenn Low-Cost-Airlines im Spiel sind oder Hin- und Rückflug günstig von verschiedenen Flughäfen gehen:
- Zwei Nur-Hinflug-Suchen in Google Flights (Hinweg: gewählte Abflughäfen → Ziel; Rückweg: Ziel → gewählte Heimatflughäfen) und die passenden addieren – nach Preis *und* nach Zeit vor Ort.
- Mit dem günstigsten *praktikablen* Hin- und Rückflug-Ticket vergleichen. Zeigen, wenn es mindestens ~10 € spart, die Anreise deutlich verkürzt oder spürbar mehr Zeit vor Ort bringt.
- Hinweis im Ergebnis: Zwei getrennte Buchungen; fällt der Hinflug aus, ist der Rückflug nicht automatisch geschützt (anders als bei Self-Transfer aber unkritisch, da kein Anschluss).

---

# Anhang C: scripts/gflights_url.py

```python
#!/usr/bin/env python3
"""Build Google Flights URLs with MULTIPLE departure and/or arrival airports.

Why: Google Flights' plain ?q= search accepts only one origin. The `tfs`
parameter (a small protobuf, base64url-encoded) accepts up to 7 airports per
side, so one URL covers e.g. STR + FKB + FDH + FMM + NUE + FRA + MUC.
Verified working in Oct 2026 for /travel/flights/search and /travel/explore.

Examples:
  # round trip, 7 origins -> 1 destination
  gflights_url.py --from STR,FKB,FDH,FMM,NUE,FRA,MUC --to BCN --depart 2026-12-05 --return 2026-12-12
  # one way, several destination airports (e.g. Barcelona + Girona + Reus)
  gflights_url.py --from STR,ZRH,BSL --to BCN,GRO,REU --depart 2026-12-05
  # Explore map/list: "anywhere" from all origins (Google shows ~1-week trips in the next 6 months;
  # set exact dates/duration in the page afterwards if needed)
  gflights_url.py --from STR,FKB,FDH,FMM,NUE,FRA,MUC --explore

Prints the URL. Currency EUR and German UI by default.
"""
import argparse
import base64


def _varint(n):
    out = bytearray()
    while True:
        b = n & 0x7F
        n >>= 7
        if n:
            out.append(b | 0x80)
        else:
            out.append(b)
            return bytes(out)


def _fv(num, val):  # varint field
    return _varint(num << 3) + _varint(val)


def _fb(num, data):  # length-delimited field
    if isinstance(data, str):
        data = data.encode()
    return _varint((num << 3) | 2) + _varint(len(data)) + data


def _airport(code):
    return _fv(1, 1) + _fb(2, code.upper())


def _leg(date, froms, tos, max_stops=None):
    body = _fb(2, date) if date else b""
    if max_stops is not None:
        body += _fv(5, max_stops)
    for c in froms:
        body += _fb(13, _airport(c))
    for c in tos:
        body += _fb(14, _airport(c))
    return _fb(3, body)


SEAT = {"economy": 1, "premium": 2, "business": 3, "first": 4}


def build_tfs(froms, tos, depart=None, ret=None, adults=1, children=0,
              seat="economy", max_stops=None, explore=False):
    msg = _fv(1, 28) + _fv(2, 3 if explore else 2)
    msg += _leg(depart, froms, tos, max_stops)
    if ret or explore:
        msg += _leg(ret, tos, froms, max_stops)
    msg += b"".join(_fv(8, 1) for _ in range(adults))
    msg += b"".join(_fv(8, 2) for _ in range(children))
    msg += _fv(9, SEAT[seat])
    msg += _fv(19, 1 if (ret or explore) else 2)
    return base64.urlsafe_b64encode(msg).decode().rstrip("=")


def main():
    p = argparse.ArgumentParser(description=__doc__, formatter_class=argparse.RawDescriptionHelpFormatter)
    p.add_argument("--from", dest="froms", required=True, help="Comma-separated IATA codes (max 7)")
    p.add_argument("--to", dest="tos", default="", help="Comma-separated IATA codes (max 7); omit with --explore")
    p.add_argument("--depart", help="YYYY-MM-DD")
    p.add_argument("--return", dest="ret", help="YYYY-MM-DD (omit for one way)")
    p.add_argument("--adults", type=int, default=1)
    p.add_argument("--children", type=int, default=0)
    p.add_argument("--seat", default="economy", choices=SEAT)
    p.add_argument("--max-stops", type=int, help="0 = nonstop only, 1 = max one stop")
    p.add_argument("--explore", action="store_true", help="Explore view (destination optional)")
    p.add_argument("--curr", default="EUR")
    p.add_argument("--hl", default="de")
    a = p.parse_args()

    froms = [c.strip() for c in a.froms.split(",") if c.strip()]
    tos = [c.strip() for c in a.tos.split(",") if c.strip()]
    if len(froms) > 7 or len(tos) > 7:
        p.error("Google Flights accepts at most 7 airports per side - split into several searches")
    if not a.explore and (not tos or not a.depart):
        p.error("--to and --depart are required unless --explore is set")

    tfs = build_tfs(froms, tos, a.depart, a.ret, a.adults, a.children, a.seat, a.max_stops, a.explore)
    path = "explore" if a.explore else "flights/search"
    print(f"https://www.google.com/travel/{path}?tfs={tfs}&curr={a.curr}&hl={a.hl}&gl=DE")


if __name__ == "__main__":
    main()
```

# Anhang D: scripts/build_report.py

```python
#!/usr/bin/env python3
"""Turn the collected flight results (JSON) into a comparison page for the Artifact tool.

Usage:
  python3 build_report.py results.json report.html

The output is page *content* (no <!doctype>/<html>/<body>) because the Artifact
tool wraps it in its own skeleton. Publish it with the Artifact tool afterwards.

JSON schema (all strings German, prices as numbers in EUR, total for all travellers):
{
  "title": "Barcelona im Dezember",              # short page name, 2-4 words
  "search": {
    "start": "Schwaikheim",
    "home_airport": "STR",                         # nearest airport; its cheapest option gets marked
    "destination": "Barcelona",
    "dates": "Sa 5.12. – Sa 12.12.2026",
    "travellers": "1 Erwachsener · nur kleine Tasche",
    "transport": "Deutschlandticket",
    "airports": ["STR", "FKB", "FMM"],
    "searched_at": "28.10.2026"
  },
  "verdict": "Ein bis drei Sätze Empfehlung.",
  "options": [                                     # 5-12 entries; sorted by price automatically
    {
      "price": 125, "price_note": "62,50 € p. P.",
      "from": "NUE", "to": "BCN",
      "return_from": "BCN", "return_to": "STR",         # optional, defaults to reverse
      "from_name": "Nürnberg",                          # optional; otherwise looked up in AIRPORTS
      "outbound": "Sa 5.12. 11:45 → 13:50 · nonstop",
      "inbound": "Sa 12.12. 06:30 → 08:30 · nonstop",   # optional for one way
      "stay": "7 Nächte · ≈ 160 h",                     # time at destination
      "airline": "Vueling",
      "source": "Google Flights",                       # where the price was found
      "link": "https://...",                            # where the price was found (fallback)
      "book": [                                         # booking links, required for the first 5 options
        {"label": "Hinflug buchen", "url": "https://..."},
        {"label": "Rückflug buchen", "url": "https://..."}
      ],
      "access": "Bahn ~3 h 30 (D-Ticket)",
      "flags": ["gabelflug"],
      "recommended": true,                              # exactly one option
      "note": "short remark"
    }
  ],
  "portals": [{"name": "Google Flights", "best": 125, "link": "https://...", "note": ""}],
  "date_tips": ["..."],
  "tips": ["..."]
}
"""
import html
import json
import sys

AIRPORTS = {
    # Region Stuttgart and nearby
    "STR": "Stuttgart", "FKB": "Karlsruhe/Baden-Baden", "FDH": "Friedrichshafen", "FMM": "Memmingen",
    "NUE": "Nürnberg", "FRA": "Frankfurt", "MUC": "München", "ZRH": "Zürich", "BSL": "Basel-Mulhouse",
    "SXB": "Straßburg", "HHN": "Frankfurt-Hahn", "SCN": "Saarbrücken", "LUX": "Luxemburg",
    "CGN": "Köln/Bonn", "DUS": "Düsseldorf", "BER": "Berlin", "HAM": "Hamburg", "HAJ": "Hannover",
    "DTM": "Dortmund", "EIN": "Eindhoven", "NRN": "Weeze",
    # Destinations
    "BCN": "Barcelona", "GRO": "Girona", "REU": "Reus", "MAD": "Madrid", "VLC": "Valencia",
    "AGP": "Málaga", "ALC": "Alicante", "SVQ": "Sevilla", "LEI": "Almería", "GRX": "Granada",
    "PMI": "Palma de Mallorca", "IBZ": "Ibiza", "TFS": "Teneriffa Süd", "TFN": "Teneriffa Nord",
    "LPA": "Gran Canaria", "ACE": "Lanzarote", "FUE": "Fuerteventura", "FNC": "Madeira",
    "LIS": "Lissabon", "OPO": "Porto", "FAO": "Faro",
    "STN": "London-Stansted", "LTN": "London-Luton", "LGW": "London-Gatwick", "LHR": "London-Heathrow",
    "LCY": "London City", "SEN": "London-Southend", "MAN": "Manchester", "EDI": "Edinburgh",
    "BRS": "Bristol", "DUB": "Dublin",
    "CDG": "Paris-CDG", "ORY": "Paris-Orly", "BVA": "Paris-Beauvais", "NCE": "Nizza", "MRS": "Marseille",
    "BOD": "Bordeaux", "LYS": "Lyon", "TLS": "Toulouse",
    "AMS": "Amsterdam", "BRU": "Brüssel", "CRL": "Brüssel-Charleroi",
    "FCO": "Rom-Fiumicino", "CIA": "Rom-Ciampino", "MXP": "Mailand-Malpensa", "LIN": "Mailand-Linate",
    "BGY": "Mailand-Bergamo", "VCE": "Venedig", "TSF": "Venedig-Treviso", "NAP": "Neapel",
    "CTA": "Catania", "PMO": "Palermo", "BLQ": "Bologna", "PSA": "Pisa", "FLR": "Florenz",
    "OLB": "Olbia", "CAG": "Cagliari", "BRI": "Bari", "MLA": "Malta",
    "VIE": "Wien", "BTS": "Bratislava", "PRG": "Prag", "BUD": "Budapest", "KRK": "Krakau",
    "KTW": "Kattowitz", "WAW": "Warschau", "WMI": "Warschau-Modlin", "GDN": "Danzig", "WRO": "Breslau",
    "ZAG": "Zagreb", "SPU": "Split", "DBV": "Dubrovnik", "LJU": "Ljubljana", "BEG": "Belgrad",
    "TIA": "Tirana", "SKP": "Skopje", "SOF": "Sofia", "OTP": "Bukarest", "TGD": "Podgorica",
    "PRN": "Pristina", "SJJ": "Sarajevo", "KIV": "Chișinău",
    "ATH": "Athen", "SKG": "Thessaloniki", "HER": "Heraklion", "CHQ": "Chania", "RHO": "Rhodos",
    "KGS": "Kos", "CFU": "Korfu", "JTR": "Santorini", "JMK": "Mykonos",
    "IST": "Istanbul", "SAW": "Istanbul-Sabiha", "AYT": "Antalya", "BJV": "Bodrum", "ADB": "Izmir",
    "LCA": "Larnaka", "PFO": "Paphos",
    "CPH": "Kopenhagen", "MMX": "Malmö", "OSL": "Oslo", "TRF": "Oslo-Torp", "BGO": "Bergen",
    "ARN": "Stockholm", "NYO": "Stockholm-Skavsta", "HEL": "Helsinki", "KEF": "Reykjavík",
    "RVN": "Rovaniemi", "TOS": "Tromsø",
    "RAK": "Marrakesch", "AGA": "Agadir", "TNG": "Tanger", "FEZ": "Fès", "TUN": "Tunis",
    "HRG": "Hurghada", "SSH": "Sharm el-Sheikh", "RMF": "Marsa Alam", "SID": "Kapverden (Sal)",
    "DXB": "Dubai", "TLV": "Tel Aviv", "JFK": "New York-JFK", "EWR": "New York-Newark",
}

FLAG_LABELS = {
    "self-transfer": ("Self-Transfer", "risk", "Getrennte Tickets: kein Anschlussschutz bei Verspätung"),
    "gabelflug": ("Gabelflug", "info", "Hin- und Rückflug über verschiedene Flughäfen"),
    "anderes-datum": ("Anderes Datum", "info", "Weicht vom gewünschten Datum ab"),
    "nachbarflughafen": ("Nachbarflughafen", "info", "Landet nicht am Hauptflughafen der Zielstadt"),
    "trip-guenstiger": ("Portal günstiger", "good", "Derselbe Flug ist bei einem Portal günstiger als bei der Airline"),
    "einzeltickets": ("Einzeltickets", "info", "Zwei getrennte Buchungen"),
    "nachtflug": ("Unpraktische Zeit", "warn", "Sehr früh, sehr spät oder mit Bahn kaum erreichbar"),
}

BOOK_LINKS_FOR_FIRST = 5

e = html.escape


def euro(v):
    if v is None:
        return "–"
    if float(v).is_integer():
        return f"{int(v)}&nbsp;€"
    return f"{v:.2f}".replace(".", ",") + "&nbsp;€"


def city(code, override=None):
    return override or AIRPORTS.get((code or "").upper(), code or "")


def place(code, override=None):
    """'Memmingen' + small code tag."""
    return f'{e(city(code, override))}<span class="code">{e(code or "")}</span>'


def legs_of(o):
    out_from, out_to = o.get("from", ""), o.get("to", "")
    ret_from = o.get("return_from") or out_to
    ret_to = o.get("return_to") or out_from
    return out_from, out_to, ret_from, ret_to


def route_title(o):
    f, t, rf, rt = legs_of(o)
    s = f'{place(f, o.get("from_name"))}<span class="arr" aria-hidden="true">→</span>{place(t, o.get("to_name"))}'
    return s


def route_back(o):
    if not o.get("inbound"):
        return ""
    f, t, rf, rt = legs_of(o)
    if rf == t and rt == f:
        return ""
    return f'<div class="back">zurück {place(rf)}<span class="arr" aria-hidden="true">→</span>{place(rt)}</div>'


def chips(o, extra=()):
    out = [f'<span class="chip chip-{k}">{e(lbl)}</span>' for lbl, k in extra]
    for f in o.get("flags") or []:
        label, kind, tip = FLAG_LABELS.get(f, (f, "info", ""))
        out.append(f'<span class="chip chip-{kind}" title="{e(tip)}">{e(label)}</span>')
    return f'<div class="chips">{"".join(out)}</div>' if out else ""


def times(o):
    rows = [f'<div class="leg"><span class="leg-l">Hin</span><span>{e(o.get("outbound",""))}</span></div>']
    if o.get("inbound"):
        rows.append(f'<div class="leg"><span class="leg-l">Zurück</span><span>{e(o["inbound"])}</span></div>')
    return f'<div class="legs">{"".join(rows)}</div>'


def buttons(o, with_booking, primary=False):
    out = []
    if with_booking:
        for i, b in enumerate(o.get("book") or []):
            cls = "btn btn-primary" if (primary and i == 0) else "btn btn-tint"
            out.append(f'<a class="{cls}" href="{e(b["url"])}" target="_blank" rel="noopener">{e(b.get("label","Buchen"))}</a>')
        if not o.get("book") and o.get("link"):
            cls = "btn btn-primary" if primary else "btn btn-tint"
            out.append(f'<a class="{cls}" href="{e(o["link"])}" target="_blank" rel="noopener">Zum Angebot</a>')
    elif o.get("link"):
        out.append(f'<a class="btn btn-plain" href="{e(o["link"])}" target="_blank" rel="noopener">Ansehen bei {e(o.get("source",""))}</a>')
    return f'<div class="actions">{"".join(out)}</div>' if out else ""


def meta(o):
    bits = [e(o.get("airline", ""))]
    if o.get("source"):
        bits.append("gefunden bei " + e(o["source"]))
    return " · ".join(b for b in bits if b)


def render(d):
    s = d.get("search", {})
    opts = d.get("options", [])
    title = d.get("title", "Flugvergleich")
    home = (s.get("home_airport") or "").upper()

    rec = next((o for o in opts if o.get("recommended")), None)
    cheapest = opts[0] if opts else None

    # cheapest option from the nearest airport (both legs from/to home preferred)
    def is_home(o, both):
        f, t, rf, rt = legs_of(o)
        return f == home and (rt == home or not both)
    home_best = None
    if home:
        cands = [o for o in opts if is_home(o, True)] or [o for o in opts if is_home(o, False)]
        home_best = min(cands, key=lambda o: o.get("price") or 0) if cands else None
    home_city = city(home)

    def tags_for(o):
        t = []
        if o is rec:
            t.append(("Empfehlung", "accent"))
        if o is cheapest and o is not rec:
            t.append(("Günstigster", "good"))
        if o is home_best:
            t.append((f"Günstigster ab {home_city}", "home"))
        return t

    # ---------- header
    pills = "".join(f'<span class="pill">{e(v)}</span>' for v in
                    [s.get("dates"), s.get("travellers"), s.get("transport")] if v)
    airports = " · ".join(e(city(a)) for a in s.get("airports", []))
    header = f'''
<header class="head">
  <p class="eyebrow">Flugvergleich{(" · Stand " + e(s["searched_at"])) if s.get("searched_at") else ""}</p>
  <h1>{e(title)}</h1>
  <p class="sub">{e(("Ab " + s["start"]) if s.get("start") else "")}{(" · Ziel: " + e(s["destination"])) if s.get("destination") else ""}</p>
  <div class="pills">{pills}</div>
  {f'<p class="airports">Gesucht ab {airports}</p>' if airports else ""}
</header>'''

    verdict = f'<p class="verdict">{e(d["verdict"])}</p>' if d.get("verdict") else ""

    # ---------- feature cards
    feature = ""
    if rec or cheapest:
        main = rec or cheapest
        side = []
        if cheapest is not main:
            side.append(("Absolut günstigster", cheapest, "good"))
        if home_best is not None and home_best is not main and home_best is not cheapest:
            side.append((f"Günstigster ab {home_city}", home_best, "home"))
        side_html = "".join(f'''
<article class="card mini mini-{k}">
  <p class="label label-{k}">{e(lbl)}</p>
  <p class="mini-price">{euro(o.get("price"))}</p>
  <p class="mini-route">{route_title(o)}</p>
  {route_back(o)}
  {times(o)}
  {f'<p class="stay">{e(o["stay"])} vor Ort</p>' if o.get("stay") else ""}
  {f'<p class="note">{e(o["note"])}</p>' if o.get("note") else ""}
  {buttons(o, True)}
</article>''' for lbl, o, k in side)
        extra_tags = [(f"Günstigster ab {home_city}", "home")] if home_best is main else []
        feature = f'''
<section class="feature {'has-side' if side else ''}">
  <article class="card hero-card">
    <p class="label label-accent">{"Empfehlung" if rec else "Günstigster Fund"}</p>
    <div class="hero-top">
      <div>
        <p class="hero-price">{euro(main.get("price"))}</p>
        {f'<p class="price-note">{e(main["price_note"])}</p>' if main.get("price_note") else ""}
      </div>
      {f'<p class="stay-big"><span>{e(main["stay"])}</span>vor Ort</p>' if main.get("stay") else ""}
    </div>
    <h2 class="hero-route">{route_title(main)}</h2>
    {route_back(main)}
    {times(main)}
    <p class="meta">{meta(main)}</p>
    {f'<p class="meta">Anreise: {e(main["access"])}</p>' if main.get("access") else ""}
    {chips(main, extra_tags)}
    {f'<p class="note">{e(main["note"])}</p>' if main.get("note") else ""}
    {buttons(main, True, primary=True)}
  </article>
  {f'<div class="side">{side_html}</div>' if side else ""}
</section>'''

    # ---------- all options
    rows = []
    for i, o in enumerate(opts, 1):
        cls = ["opt"]
        if o is rec:
            cls.append("opt-rec")
        if "self-transfer" in (o.get("flags") or []):
            cls.append("opt-risk")
        rows.append(f'''
<li class="{' '.join(cls)}">
  <div class="opt-price">
    <span class="rank">{i}</span>
    <span class="price">{euro(o.get("price"))}</span>
    {f'<span class="price-note">{e(o["price_note"])}</span>' if o.get("price_note") else ""}
  </div>
  <div class="opt-main">
    <p class="opt-route">{route_title(o)}</p>
    {route_back(o)}
    {times(o)}
    <p class="meta">{meta(o)}{(" · Anreise: " + e(o["access"])) if o.get("access") else ""}</p>
    {f'<p class="note">{e(o["note"])}</p>' if o.get("note") else ""}
  </div>
  <div class="opt-side">
    {f'<p class="stay">{e(o["stay"])}</p>' if o.get("stay") else ""}
    {chips(o, tags_for(o))}
    {buttons(o, i <= BOOK_LINKS_FOR_FIRST or o is rec or o is home_best)}
  </div>
</li>''')

    portals = d.get("portals", [])
    pbest = min([p["best"] for p in portals if p.get("best") is not None], default=None)
    portal_html = "".join(f'''
<li>
  <div><a href="{e(p.get("link") or "#")}" target="_blank" rel="noopener">{e(p.get("name",""))}</a>
  {f'<p class="note">{e(p["note"])}</p>' if p.get("note") else ""}</div>
  <span class="pprice{' win' if p.get('best') is not None and p.get('best') == pbest else ''}">{euro(p.get("best"))}</span>
</li>''' for p in portals)

    def bullets(items):
        return "".join(f"<li>{e(t)}</li>" for t in items)

    date_tips, tips = d.get("date_tips", []), d.get("tips", [])

    return f'''<title>{e(title)}</title>
<meta name="claude:viewport-fit-sides" content="cover">
<style>
/* Layout: quiet header → recommendation card (+ side cards) → option list → portals & notes. Light, airy, system type. */
:root {{
  --bg: #f5f5f7; --surface: #ffffff; --fg: #1d1d1f; --fg2: #6e6e73; --fg3: #86868b; --line: #d2d2d7;
  --accent: #0071e3; --accent-tint: #e8f2fd; --good: #1d8a3b; --good-tint: #e6f4ea;
  --home: #7a4cc9; --home-tint: #f1ebfb; --warn: #a85b00; --warn-tint: #fdf1e1;
  --risk: #c4161c; --risk-tint: #fdeaea; --info: #515154; --info-tint: #eeeef0;
  --shadow: 0 1px 2px rgba(0,0,0,.04), 0 8px 24px rgba(0,0,0,.06);
  --font: -apple-system, BlinkMacSystemFont, "SF Pro Text", "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  --display: -apple-system, BlinkMacSystemFont, "SF Pro Display", "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
}}
@media (prefers-color-scheme: dark) {{ :root:not([data-theme="light"]) {{
  --bg: #000000; --surface: #1c1c1e; --fg: #f5f5f7; --fg2: #a1a1a6; --fg3: #8e8e93; --line: #38383a;
  --accent: #2997ff; --accent-tint: #0f2a45; --good: #30d158; --good-tint: #10301a;
  --home: #bf9bff; --home-tint: #2a1f3d; --warn: #ff9f0a; --warn-tint: #3a2a0e;
  --risk: #ff453a; --risk-tint: #3d1514; --info: #c7c7cc; --info-tint: #2c2c2e;
  --shadow: none; color-scheme: dark; }} }}
:root[data-theme="dark"] {{
  --bg: #000000; --surface: #1c1c1e; --fg: #f5f5f7; --fg2: #a1a1a6; --fg3: #8e8e93; --line: #38383a;
  --accent: #2997ff; --accent-tint: #0f2a45; --good: #30d158; --good-tint: #10301a;
  --home: #bf9bff; --home-tint: #2a1f3d; --warn: #ff9f0a; --warn-tint: #3a2a0e;
  --risk: #ff453a; --risk-tint: #3d1514; --info: #c7c7cc; --info-tint: #2c2c2e;
  --shadow: none; color-scheme: dark; }}
body {{ background: var(--bg); color: var(--fg); font: 17px/1.47 var(--font); -webkit-font-smoothing: antialiased; letter-spacing: -0.01em; }}
.wrap {{ max-width: 1060px; margin: 0 auto; padding-block: 48px 80px;
  padding-left: max(16px, env(safe-area-inset-left, 0px)); padding-right: max(16px, env(safe-area-inset-right, 0px));
  display: grid; gap: 40px; }}
p {{ margin: 0; }}
a {{ color: var(--accent); text-decoration: none; }}
a:hover {{ text-decoration: underline; }}
a:focus-visible {{ outline: 3px solid var(--accent); outline-offset: 2px; border-radius: 8px; }}
.price, .hero-price, .mini-price, .pprice, .legs, .stay, .stay-big {{ font-variant-numeric: tabular-nums; }}
.code {{ font-size: .62em; font-weight: 600; color: var(--fg3); letter-spacing: .04em; margin-left: .35em; vertical-align: .18em; }}
.arr {{ color: var(--fg3); margin: 0 .4em; font-weight: 400; }}

.head {{ display: grid; gap: 12px; text-align: left; }}
.eyebrow {{ font-size: 14px; font-weight: 600; color: var(--fg2); }}
h1 {{ font: 700 clamp(34px, 6vw, 56px)/1.07 var(--display); letter-spacing: -0.028em; margin: 0; text-wrap: balance; }}
.sub {{ font-size: 21px; color: var(--fg2); }}
.pills {{ display: flex; flex-wrap: wrap; gap: 8px; margin-top: 4px; }}
.pill {{ background: var(--surface); border: 1px solid var(--line); border-radius: 980px; padding: 6px 14px; font-size: 14px; color: var(--fg); }}
.airports {{ font-size: 14px; color: var(--fg3); }}
.verdict {{ font-size: 21px; line-height: 1.4; max-width: 46em; color: var(--fg); }}

.card {{ background: var(--surface); border-radius: 22px; box-shadow: var(--shadow); }}
.feature {{ display: grid; gap: 16px; grid-template-columns: minmax(0, 1fr); }}
.feature > * {{ min-width: 0; }}
.feature.has-side {{ grid-template-columns: minmax(0, 1.6fr) minmax(0, 1fr); }}
.side {{ display: grid; gap: 16px; align-content: start; }}
.hero-card {{ padding: 32px; display: grid; gap: 14px; align-content: start; min-width: 0; }}
.label {{ font-size: 13px; font-weight: 600; text-transform: uppercase; letter-spacing: .06em; }}
.label-accent {{ color: var(--accent); }} .label-good {{ color: var(--good); }} .label-home {{ color: var(--home); }}
.hero-top {{ display: flex; flex-wrap: wrap; justify-content: space-between; align-items: flex-end; gap: 16px; }}
.hero-price {{ font: 700 clamp(48px, 8vw, 72px)/1 var(--display); letter-spacing: -0.03em; }}
.price-note {{ font-size: 14px; color: var(--fg2); margin-top: 6px; display: block; }}
.stay-big {{ text-align: right; color: var(--fg2); font-size: 14px; display: grid; }}
.stay-big span {{ font: 600 24px/1.2 var(--display); color: var(--fg); }}
.hero-route {{ font: 600 28px/1.2 var(--display); letter-spacing: -0.02em; margin: 6px 0 0; overflow-wrap: anywhere; }}
.back {{ font-size: 15px; color: var(--fg2); }}
.legs {{ display: grid; gap: 4px; font-size: 15px; }}
.leg {{ display: flex; gap: 12px; }}
.leg-l {{ min-width: 4.5em; color: var(--fg3); font-size: 13px; font-weight: 600; text-transform: uppercase; letter-spacing: .04em; padding-top: 2px; }}
.meta {{ font-size: 14px; color: var(--fg2); }}
.note {{ font-size: 14px; color: var(--fg2); }}
.stay {{ font-size: 14px; font-weight: 600; color: var(--fg); }}

.mini {{ padding: 22px 24px; display: grid; gap: 8px; }}
.mini-price {{ font: 700 34px/1.05 var(--display); letter-spacing: -0.02em; }}
.mini-route {{ font-weight: 600; font-size: 17px; }}
.mini .legs {{ font-size: 14px; }}

.chips {{ display: flex; flex-wrap: wrap; gap: 6px; }}
.chip {{ font-size: 12px; font-weight: 600; padding: 3px 10px; border-radius: 980px; white-space: nowrap; }}
.chip-accent {{ background: var(--accent-tint); color: var(--accent); }}
.chip-good {{ background: var(--good-tint); color: var(--good); }}
.chip-home {{ background: var(--home-tint); color: var(--home); }}
.chip-warn {{ background: var(--warn-tint); color: var(--warn); }}
.chip-risk {{ background: var(--risk-tint); color: var(--risk); }}
.chip-info {{ background: var(--info-tint); color: var(--info); }}

.actions {{ display: flex; flex-wrap: wrap; gap: 8px; margin-top: 4px; }}
.btn {{ display: inline-flex; align-items: center; border-radius: 980px; padding: 8px 18px; font-size: 15px; font-weight: 500; white-space: nowrap; }}
.btn:hover {{ text-decoration: none; filter: brightness(1.06); }}
.btn-primary {{ background: var(--accent); color: #fff; }}
.btn-tint {{ background: var(--accent-tint); color: var(--accent); }}
.btn-plain {{ padding: 6px 0; color: var(--accent); font-size: 14px; }}

h2.section {{ font: 700 28px/1.15 var(--display); letter-spacing: -0.02em; margin: 0 0 16px; }}
.list {{ list-style: none; margin: 0; padding: 0; display: grid; gap: 12px; }}
.opt {{ background: var(--surface); border-radius: 18px; box-shadow: var(--shadow); padding: 20px 24px;
  display: grid; grid-template-columns: 120px minmax(0, 1fr) minmax(0, 240px); gap: 20px; align-items: start; }}
.opt-rec {{ box-shadow: inset 0 0 0 2px var(--accent), var(--shadow); }}
.opt-risk .opt-price .price {{ color: var(--risk); }}
.opt-price {{ display: grid; gap: 2px; }}
.rank {{ font-size: 13px; color: var(--fg3); font-weight: 600; }}
.price {{ font: 700 26px/1.1 var(--display); letter-spacing: -0.02em; }}
.opt-main {{ display: grid; gap: 6px; min-width: 0; }}
.opt-route {{ font-weight: 600; font-size: 19px; letter-spacing: -0.015em; }}
.opt-side {{ display: grid; gap: 8px; justify-items: start; min-width: 0; }}

.lower {{ display: grid; grid-template-columns: 1fr 1fr; gap: 32px; }}
.lower > section {{ min-width: 0; }}
.group {{ list-style: none; margin: 0; padding: 0; background: var(--surface); border-radius: 18px; box-shadow: var(--shadow); overflow: hidden; }}
.group li {{ display: flex; justify-content: space-between; align-items: flex-start; gap: 16px; padding: 14px 20px; }}
.group li + li {{ border-top: 1px solid var(--line); }}
.pprice {{ font-weight: 600; white-space: nowrap; }}
.pprice.win {{ color: var(--good); }}
.tips {{ margin: 0; padding-left: 1.1em; display: grid; gap: 10px; color: var(--fg); font-size: 15px; }}
.tips + h2.section {{ margin-top: 32px; }}
footer {{ font-size: 12px; color: var(--fg3); border-top: 1px solid var(--line); padding-top: 16px; }}

@media (max-width: 860px) {{
  .feature.has-side {{ grid-template-columns: 1fr; }}
  .opt {{ grid-template-columns: 1fr; gap: 12px; }}
  .opt-price {{ display: flex; align-items: baseline; gap: 10px; flex-wrap: wrap; }}
  .lower {{ grid-template-columns: 1fr; }}
  .hero-card {{ padding: 24px; }}
  .stay-big {{ text-align: left; }}
  .hero-route {{ font-size: 22px; }}
  .btn {{ white-space: normal; }}
}}
@media (prefers-reduced-motion: no-preference) {{
  .btn {{ transition: filter .15s ease; }}
}}
</style>
<div class="wrap">
  {header}
  {verdict}
  {feature}
  <section>
    <h2 class="section">Alle Optionen</h2>
    <ol class="list">{"".join(rows) if rows else "<li>Keine Ergebnisse gefunden.</li>"}</ol>
  </section>
  <div class="lower">
    <section>
      <h2 class="section">Bester Preis je Portal</h2>
      <ul class="group">{portal_html}</ul>
    </section>
    <section>
      {f'<h2 class="section">Günstigere Daten</h2><ul class="tips">{bullets(date_tips)}</ul>' if date_tips else ""}
      {f'<h2 class="section">Gut zu wissen</h2><ul class="tips">{bullets(tips)}</ul>' if tips else ""}
    </section>
  </div>
  <footer>Preise sind Momentaufnahmen für alle Reisenden zusammen und können sich bis zur Buchung ändern. Anreisezeiten sind grobe Richtwerte. Nichts wurde gebucht.</footer>
</div>
'''


def main():
    if len(sys.argv) != 3:
        print(__doc__)
        sys.exit(1)
    with open(sys.argv[1], encoding="utf-8") as f:
        data = json.load(f)
    data["options"] = sorted(data.get("options", []), key=lambda o: (o.get("price") is None, o.get("price") or 0))
    with open(sys.argv[2], "w", encoding="utf-8") as f:
        f.write(render(data))
    print(f"wrote {sys.argv[2]} ({len(data['options'])} options)")


if __name__ == "__main__":
    main()
```

# Anhang E: scripts/book_link.py

```python
#!/usr/bin/env python3
"""Booking link for one concrete flight (or a round trip with the same airports).

Ryanair: direct deep link to the flight selection page (tested, shows the flights of that day).
Every other airline: Google Flights search for exactly that route and date - one click on the
flight shows "Buchungsoptionen" with the airline's own site and portals.

  book_link.py --airline ryanair --from FMM --to ZAG --date 2026-12-28 --adults 2
  book_link.py --airline ryanair --from FMM --to KRK --date 2027-01-07 --return-date 2027-01-09 --adults 2
  book_link.py --airline wizz --from TIA --to STR --date 2026-12-31 --adults 2
"""
import argparse
import os
import sys

sys.path.insert(0, os.path.dirname(os.path.abspath(__file__)))
from gflights_url import build_tfs  # noqa: E402


def main():
    p = argparse.ArgumentParser()
    p.add_argument("--airline", default="")
    p.add_argument("--from", dest="frm", required=True)
    p.add_argument("--to", required=True)
    p.add_argument("--date", required=True)
    p.add_argument("--return-date")
    p.add_argument("--adults", type=int, default=1)
    p.add_argument("--children", type=int, default=0)
    a = p.parse_args()
    if a.airline.lower().startswith("ryan"):
        ret = a.return_date or ""
        print("https://www.ryanair.com/de/de/trip/flights/select?"
              f"adults={a.adults}&teens=0&children={a.children}&infants=0&dateOut={a.date}&dateIn={ret}"
              f"&isConnectedFlight=false&discount=0&isReturn={'true' if ret else 'false'}&promoCode="
              f"&originIata={a.frm.upper()}&destinationIata={a.to.upper()}")
    else:
        tfs = build_tfs([a.frm], [a.to], a.date, a.return_date, a.adults, a.children)
        print(f"https://www.google.com/travel/flights/search?tfs={tfs}&curr=EUR&hl=de&gl=DE")


if __name__ == "__main__":
    main()
```