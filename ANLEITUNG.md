# Lebendiger Adventskalender – Anleitung

Diese Website besteht nur aus Dateien: kein Server, keine Installation, keine Datenbank auf dem Webspace.
Sie kann sofort ausprobiert werden und später mit wenigen Handgriffen scharf geschaltet werden.

---

## 1. Sofort ansehen (Vorschau-Modus)

Ordner entpacken und `index.html` doppelklicken. Fertig.

In diesem Zustand liegen alle Daten **nur im eigenen Browser**. Es werden keine Server kontaktiert,
nichts wird veröffentlicht, nichts kann kaputtgehen. Beispieldaten für die Saison 2026 sind schon
hinterlegt, damit man sieht, wie die Seite gefüllt aussieht.

Der Verwaltungsbereich lässt sich ebenfalls testen: unten im Fußbereich auf „Verwaltung“ klicken
(oder `admin.html` öffnen). Zum Anmelden genügt in der Vorschau **eine beliebige E-Mail-Adresse und
ein beliebiges Passwort**. Unter „Einstellungen“ ganz unten kann die Vorschau jederzeit wieder auf
den Anfangszustand zurückgesetzt werden.

Am unteren Bildschirmrand steht in diesem Modus dauerhaft „Vorschau“.

Kleiner Hinweis: Wird die Seite direkt von der Festplatte geöffnet, sperrt manch ein Browser das
Speichern im Browserspeicher. Die Seite zeigt dann bei jedem Neuladen wieder die Beispieldaten.
Am Ausprobieren ändert das nichts – und sobald die Seite im Internet liegt, bleiben Änderungen erhalten.

---

## 2. Was ist neu gegenüber der alten Seite?

* **Eigener Verwaltungsbereich** (`admin.html`) statt eines Fensters innerhalb der Seite – mit
  Übersicht, Anmeldungen, Terminen, Nachrückern, Einstellungen und Saisons.
* **Saisonverwaltung**: Eine neue Adventszeit ist in einem Formular angelegt. Der erste Advent wird
  automatisch berechnet, alle Abende bis zum 23. Dezember werden erzeugt, die Heiligabend-Gottesdienste
  können aus dem Vorjahr übernommen werden. Alte Jahre bleiben vollständig erhalten und lassen sich
  über die Saison-Auswahl oben jederzeit ansehen.
* **Fast alle Texte sind im Verwaltungsbereich änderbar** – Untertitel, Info-Texte, Formulartexte,
  Kontakt, Impressum und Datenschutz. Es muss keine Datei mehr angefasst werden.
* **Ankündigungsband** für kurzfristige Hinweise („Heute fällt der Abend leider aus“).
* **Echtes Wochenraster**: Der Kalender ist wie ein Wandkalender aufgebaut – Montag bis Sonntag,
  jede Woche eine Zeile. Der Zeitraum kann bei Bedarf über den ersten Advent hinaus nach vorn
  verlängert werden.
* **Datenschutz deutlich verbessert**: kein Google Analytics mehr, keine externen Schriftarten,
  keine externen Skript-Bibliotheken. Die Karte lädt erst nach ausdrücklicher Einwilligung.
  E-Mail-Adressen und Telefonnummern der Gastgeber sind für Besucher **nicht** abrufbar.
* **Mobil zuerst gedacht**: große Schaltflächen, Menü zum Aufklappen, Detailfenster fahren von unten
  ins Bild wie in einer App.
* **Sicherheitsregeln** für die Datenbank liegen als Datei bei (`firestore.rules`) und lassen nur
  eingetragene E-Mail-Adressen an die Verwaltung.

---

## 3. Firebase vorbereiten

Das bestehende Projekt `adventskalender-90bb1` kann weiterverwendet werden. Alternativ ein neues
Projekt anlegen – dann in Schritt 4 die neuen Zugangsdaten eintragen.

1. **console.firebase.google.com** öffnen und das Projekt auswählen.
2. **Firestore-Datenbank**: falls noch nicht vorhanden, anlegen. Beim Speicherort möglichst eine
   Region in der EU wählen, zum Beispiel `europe-west3` (Frankfurt). Der Speicherort lässt sich
   später **nicht** mehr ändern.
3. **Authentifizierung → Anmeldemethode**: „E-Mail/Passwort“ aktivieren.
   Die Option „E-Mail-Link“ ist nicht nötig.
4. **Authentifizierung → Nutzer**: für jede Person des Organisationsteams einen Nutzer anlegen
   (E-Mail und Passwort). Selbstregistrierung sollte deaktiviert bleiben, damit sich niemand
   von außen ein Konto erstellen kann (Einstellungen → Nutzeraktionen).
5. **Sicherheitsregeln einsetzen**: Firestore → Regeln. Den Inhalt der Datei `firestore.rules`
   vollständig hineinkopieren. **Wichtig:** Im oberen Bereich bei `istAdmin()` die E-Mail-Adressen
   aller Personen eintragen, die verwalten dürfen – genau so geschrieben wie beim Nutzerkonto,
   in Kleinbuchstaben und in einfachen Anführungszeichen.

   ```
   request.auth.token.email in [
     'dominik.teminski@ekkw.de',
     'zweite.person@ekkw.de'
   ];
   ```

   Dann „Veröffentlichen“.

> **Achtung:** Die neuen Regeln ersetzen die alten. Die bisherige Website greift auf andere
> Sammlungen zu (`anmeldungen`, `events`, `settings` direkt im Wurzelverzeichnis) und funktioniert
> danach nicht mehr. Am besten also erst umstellen, wenn die neue Seite online geht. Die alten
> Daten bleiben in der Datenbank erhalten und können bei Bedarf über die Firebase-Konsole
> eingesehen werden.

---

## 4. Zugangsdaten eintragen

Die Datei `firebase-config.js` mit einem Texteditor öffnen. Dort steht:

```js
window.FIREBASE_CONFIG = null;
```

Diese Zeile löschen und beim Block darunter die Zeichen `/*` und `*/` entfernen. Die Zugangsdaten
des bestehenden Projekts sind schon eingetragen. Danach arbeitet die Seite mit der echten Datenbank.

Diese Zugangsdaten sind kein Passwort und dürfen öffentlich im Quelltext stehen – der Schutz kommt
allein durch die Sicherheitsregeln aus Schritt 3.

---

## 5. Veröffentlichen

Der gesamte Ordner-Inhalt wird hochgeladen – so wie bisher, zum Beispiel über GitHub Pages mit dem
Konto `kirche-heli`:

1. Im Repository alle alten Dateien löschen (oder in einen Ordner `alt/` verschieben).
2. Den kompletten Inhalt dieses Ordners hochladen (also `index.html`, `admin.html`, `assets/`,
   `css/`, `js/`, `fonts/` und die übrigen Dateien).
3. Fertig – die Seite ist unter der gewohnten Adresse erreichbar, der Verwaltungsbereich unter
   `…/admin.html`.

Die Dateien `ANLEITUNG.md` und `firestore.rules` werden nicht gebraucht, stören aber nicht.

---

## 6. Ersteinrichtung im Verwaltungsbereich

`admin.html` aufrufen und mit der E-Mail-Adresse aus Schritt 3 anmelden.

Da noch keine Saison besteht, erscheint die **Ersteinrichtung**: Jahr eintragen (der erste Advent
wird automatisch vorgeschlagen), gegebenenfalls den Auftakt-Eintrag bestätigen, auf „Saison anlegen“
klicken. Alle Abende werden erzeugt, die Startseite ist ab diesem Moment gefüllt.

Danach der Reihe nach:

* **Einstellungen** → Texte prüfen, WhatsApp-Link kontrollieren, Impressum und Datenschutz abstimmen.
* **Termine** → Heiligabend-Gottesdienste eintragen.
* **Übersicht** → „Anmeldung öffnen“, sobald geworben wird.

---

## 7. Der Ablauf während der Adventszeit

**Anmeldungen zuweisen.** Im Bereich „Anmeldungen“ steht jede Anfrage mit ihren Wunschterminen als
goldene Kärtchen. Ein Klick auf ein Kärtchen weist diesen Abend zu: Name und Adresse landen
automatisch beim Termin, die Anmeldung wird als „zugeteilt“ markiert. Bereits vergebene Termine sind
durchgestrichen.

**Termine nachbearbeiten.** Im Bereich „Termine“ lässt sich jeder Abend einzeln ändern – Uhrzeit
(leer bedeutet 18:00 Uhr), Gastgeber, Adresse, öffentlicher Hinweis. Drei Schalter je Abend:
barrierefrei, „geändert“ (erscheint als Warnhinweis auf der Startseite) und die goldene Darstellung
auf der Karte. „Fenster freigeben“ macht einen Abend wieder frei und setzt die zugehörige Anmeldung
zurück.

**Koordinaten für die Karte.** Mit „📍 ermitteln“ wird die Adresse eines Abends einmalig in
Koordinaten umgewandelt. Für alle offenen Adressen zugleich gibt es oben „📍 Koordinaten für alle
ermitteln“ – das dauert etwa eine Sekunde pro Adresse, weil der Dienst nicht schneller angefragt
werden darf. Besucherinnen und Besucher der Website fragen diesen Dienst nie selbst an.

**E-Mails an die Gastgeber.** Unter „Anmeldungen“ steht ein Feld mit allen Adressen zum Kopieren.
Im Mailprogramm bitte in das Feld **BCC** einsetzen, damit die Adressen nicht untereinander
sichtbar werden.

**Zeitraum erweitern.** Normalerweise beginnt der Kalender am ersten Advent. Soll ein Abend schon
vorher stattfinden, lässt sich der Zeitraum unter „Termine“ ganz oben nach vorn verlängern –
entweder mit den Schnellknöpfen („1 Tag früher“, „3 Tage früher“, „1 Woche früher“) oder mit einem
frei gewählten Datum. Die zusätzlichen Abende erscheinen sofort im Kalender und im Anmeldeformular.
Umgekehrt lässt sich der Zeitraum auch wieder verkürzen; bereits eingetragene Angaben bleiben dabei
gespeichert und tauchen wieder auf, sobald man den Zeitraum erneut verlängert. „Zurück zum
1. Advent“ stellt den Ausgangszustand her.

**Kurzfristige Hinweise.** Unter „Übersicht“ die Ankündigung schreiben und anzeigen lassen.

**Nachrückerliste.** Unter „Nachrücker“ aktivieren – dann erscheint auf der Startseite ein zweites,
kleineres Formular für Kurzentschlossene.

**Datenschutz am Ende der Saison.** Die Anmeldungen enthalten Telefonnummern und E-Mail-Adressen.
Wenn sie nicht mehr gebraucht werden, sollten sie gelöscht werden: entweder einzeln über „Löschen“
oder vorher über „Als Excel-Datei (CSV) exportieren“ sichern. Die Termine selbst bleiben davon
unberührt.

---

## 8. Das nächste Jahr

Verwaltungsbereich → **Saisons** → „Neue Saison anlegen“:

* Jahr eintragen – der erste Advent wird berechnet und angezeigt, kann aber überschrieben werden.
* „Zusätzliche Tage vor dem 1. Advent“ verlängert den Kalender gleich beim Anlegen nach vorn.
  0 bedeutet: Start am ersten Advent. Später jederzeit unter „Termine“ änderbar.
* „Gottesdienste übernehmen aus …“ kopiert die Heiligabend-Einträge des Vorjahres.
* „Auftakt-Eintrag am ersten Advent anlegen“ setzt die Eröffnung an der Stadtkirche.
* „Sofort öffentlich schalten“ lässt Besucher ab sofort das neue Jahr sehen; die Anmeldung der
  bisherigen Saison wird dabei geschlossen.

Das alte Jahr kann anschließend über „Archivieren“ weggelegt werden. Alle Daten bleiben erhalten.
Mehr ist nicht zu tun – Texte, Bilder und Einstellungen gelten weiter.

---

## 9. Datenschutz – Stand der Dinge

**Bereits umgesetzt:**

* Kein Analytics, keine Werbe-Cookies, keine Zählpixel.
* Schriftarten liegen im Ordner `fonts/` und werden nicht von Google geladen.
* Alle Programmbibliotheken liegen im Ordner `js/`; es wird kein externes Skript-Netzwerk (CDN)
  angesprochen.
* Die Karte lädt erst nach ausdrücklicher Einwilligung, mit klarem Hinweis auf OpenStreetMap.
* Der Wetterdienst ist abschaltbar und erhält keine Standortdaten des Geräts.
* Telefonnummern und E-Mail-Adressen der Gastgeber sind für Besucher der Seite technisch nicht
  abrufbar – nur Name und Adresse des Veranstaltungsorts, und nur mit Einwilligung.
* Das Anmeldeformular hat einen unsichtbaren Spam-Schutz, ganz ohne externes Prüfsystem.
* Jede Seite enthält eine Sicherheitsvorgabe (Content-Security-Policy), die Verbindungen zu
  fremden Servern unterbindet.

**Impressum und Datenschutzerklärung sind vollständig ausformuliert** – mit den konkreten Angaben
der Gemeinde, der örtlich Beauftragten für den Datenschutz der EKKW (Kerstin Koch, Landeskirchenamt
Kassel) und der zuständigen Aufsichtsbehörde (BfD EKD, Außenstelle Dortmund, Datenschutzregion
Mitte-West). Als verantwortliche Person nach § 18 Abs. 2 MStV ist Pfarrer Dominik Teminski
eingetragen. Beide Texte lassen sich im Verwaltungsbereich unter „Einstellungen“ jederzeit
überschreiben.

**Es bleiben drei Punkte, die nur vor Ort geklärt werden können:**

1. **Speicherort der Datenbank prüfen.** In der Datenschutzerklärung steht, dass die Datenbank in
   der EU liegt (Region `europe-west3`, Frankfurt). Bei einem neuen Firebase-Projekt wird diese
   Region in Schritt 3 einfach ausgewählt. Beim bestehenden Projekt `adventskalender-90bb1` steht
   die tatsächliche Region in der Firebase-Konsole unter Firestore ganz oben. Sollte dort eine
   andere Region stehen, ist entweder der eine Satz in `datenschutz.html` anzupassen – oder, was
   sauberer wäre, ein neues Projekt mit EU-Region anzulegen. Die Region eines bestehenden Projekts
   lässt sich nachträglich nicht ändern.
2. **Hosting-Anbieter prüfen.** Der Text nennt GitHub Pages als Anbieter. Falls die Seite woanders
   liegt, ist Abschnitt 4 der Datenschutzerklärung entsprechend anzupassen.
3. **Auftragsverarbeitung mit Google bestätigen.** In der Google-Cloud-Konsole des Projekts unter
   „Data Processing and Security Terms“ die Vereinbarung akzeptieren, sofern das nicht schon
   geschehen ist.

Eine kurze Rücksprache mit der örtlich Beauftragten für den Datenschutz (datenschutz@ekkw.de) ist
trotzdem empfehlenswert, bevor die Seite online geht.

---

## 10. Wenn etwas nicht funktioniert

**„Keine Berechtigung“ im Verwaltungsbereich.** Die angemeldete E-Mail-Adresse steht nicht in den
Sicherheitsregeln. Schritt 3.5 prüfen – Schreibweise muss genau übereinstimmen.

**Die Startseite sagt „Die Seite ist fast fertig“.** Es ist noch keine Saison öffentlich geschaltet.
Verwaltungsbereich → Saisons → „Öffentlich schalten“.

**Die Karte bleibt leer.** Die Abende haben noch keine Koordinaten. Verwaltungsbereich → Termine →
„📍 Koordinaten für alle ermitteln“.

**Nach dem Umstellen ist alles leer.** Die neue Seite verwendet eine neue Datenstruktur; die alten
Einträge werden nicht automatisch übernommen. Die Termine der alten Saison müssen einmalig
eingetragen werden – oder man beginnt einfach mit der neuen Saison.

**Etwas mit der Sicherheitsvorgabe blockiert.** Falls eine Funktion unerwartet nicht arbeitet, kann
zum Testen die Zeile mit `Content-Security-Policy` im Kopfbereich der betreffenden HTML-Datei
entfernt werden. Sie ist ein zusätzlicher Schutz, keine Voraussetzung.

---

## 11. Was liegt wo?

```
index.html              Startseite
admin.html              Verwaltungsbereich
impressum.html          Impressum
datenschutz.html        Datenschutzerklärung
firebase-config.js      Zugangsdaten (Schritt 4)
firestore.rules         Sicherheitsregeln für Firebase (Schritt 3)
manifest.webmanifest    Angaben zum Speichern auf dem Handy-Startbildschirm
ANLEITUNG.md            diese Datei
assets/                 Logo, Titelbild, QR-Bild, Symbole
css/                    Gestaltung
fonts/                  Schriftarten (lokal)
js/                     Programmteile
```
