# Programmierrichtlinien (projektübergreifend)

Gilt für alle Projekte. Projektspezifische CLAUDE.md-Dateien ergänzen oder
überschreiben das hier bewusst — im Konflikt gewinnt die Projektdatei.

**Regelmäßig auf Aktualität prüfen.** Diese Datei wird von den Projekten per
Import eingebunden, nicht kopiert. Trotzdem lohnt sich zu Beginn einer
Arbeitssitzung ein kurzer Blick, ob das lokale `claude-guidelines`-Repo den
aktuellen Stand hat (`git fetch` + Vergleich mit dem Remote-Branch) — sonst
arbeitet man unbemerkt gegen einen veralteten Stand der Richtlinien. Liegt ein
neuerer Stand vor, wird er gezogen, bevor die eigentliche Arbeit beginnt.

Das gilt nicht nur beim Start: Eine Sitzung kann sich über Tage hinziehen.
Deshalb wird auch zwischendurch — etwa vor größeren Arbeitsschritten oder in
regelmäßigen Abständen — erneut geprüft, nicht nur einmal zu Beginn.

## Projektstruktur

| Verzeichnis | Inhalt |
|---|---|
| `src/` | Quellcode. Darunter **immer** die Artifact-Group als Pfad: `src/jtkdvlp/<artifact>/…`, bei mehreren Build-Zielen `src/<bereich>/jtkdvlp/<artifact>/…` |
| `resources/` | **Nur handgeschriebene** Assets. Hier liegt niemals ein Kompilat. |
| `target/` | **Alles Generierte** — Kompilate und kopierte Assets. Die Struktur darunter ist frei. Muss jederzeit löschbar sein, ohne dass etwas verloren geht. |
| `dev/` | Alles, was zur Entwicklung gehört, aber nicht zum Produkt. Gibt es immer. |
| `dev/tmp/` | Wegwerf-Dateien: Screenshots, Logs, Messskripte. Nicht versioniert. |
| `dev/sample/` | Beispiel- und Testdateien, etwa große Dateien für Performance-Messungen. |
| `scripts/` | Build-, Start- und Hilfsskripte. |

**Artifact-Group ist `jtkdvlp`.** Der Artifact-Name folgt darunter, also
`jtkdvlp.<artifact>` als Namespace-Wurzel und `jtkdvlp/<artifact>/` als Pfad.

**Die Bereichsebene entsteht erst mit dem zweiten Build-Ziel.** Solange ein
Projekt nur einen Build hat, liegt der Quellcode direkt unter
`src/jtkdvlp/<artifact>/…`. Ein `<bereich>` trennt Build-Pfade voneinander —
Haupt- und Renderer-Prozess bei Electron, Server und Client bei einer
Webanwendung. Wo es nur einen Pfad gibt, trennt er nichts und kostet bloß eine
Ebene.

**Bereiche werden fachlich benannt, nie `common` oder `shared`.** Solche Namen
beschreiben eine technische Eigenschaft — „wird von mehreren benutzt" — und
sagen nichts über den Inhalt. Genau daraus entsteht das Sammelbecken, das mit
jeder Funktion wächst, ohne je eine Grenze zu ziehen. Braucht ein Thema wirklich
mehrere Bereiche, bekommt es einen eigenen, nach dem Thema benannten.

**Vor jeder Struktur-Ebene die Frage: Was trennt sie *heute*?** Lässt sich das
nicht konkret beantworten, entsteht sie später. Ein `git mv` kostet nichts; eine
Ebene ohne Zweck kostet bei jedem Lesen. Das gilt auch für einen Nutzen, der
„bestimmt bald" eintritt — er tritt oft nicht ein, und dann bleibt die Ebene
stehen und wird für Absicht gehalten.

**Temporäres gehört nach `dev/tmp/`**, nicht nach `/tmp` und nicht ins
Projektwurzelverzeichnis. Wenn eine Datei ausnahmsweise woanders liegen muss
(etwa weil ein Werkzeug `dev/tmp` ausblendet), gehört der Grund als Kommentar in
die `.gitignore`.

**Die Trennung `resources/` ↔ `target/` ist strikt.** Ein Build kopiert aus
`resources/` nach `target/` — nie umgekehrt, und nie erzeugt er etwas in
`resources/`. Das hält `target/` jederzeit löschbar und `resources/` frei von
Dingen, die man versehentlich mitversioniert.

## Änderungen an fremdem Hoheitsgebiet

Ohne ausdrückliche Freigabe **nicht** anfassen:

- Skripte unter `scripts/`
- Berechtigungsdateien (`.claude/settings.json` und Ähnliches)
- Alles außerhalb des Projektverzeichnisses

Der Grund ist bei allen dreien derselbe: Es sind Dinge, die Vertrauen oder
Automatisierung tragen. Wenn eine Änderung dort nötig ist, vorher fragen und
dabei sagen, welche Zeilen es betrifft.

## Vorgehen

**Messen statt raten.** Bei Performance-, Speicher- oder Timing-Fragen gilt
keine Vermutung als Befund, bevor sie gemessen ist — auch nicht die eigene. Die
Ursache liegt regelmäßig woanders als erwartet. Erst messen, dann ändern, dann
erneut messen.

**Das Messwerkzeug muss zur Frage passen.** Ein paar Fallen, die schon Zeit
gekostet haben:
- `ps` mittelt `%CPU` über die Prozesslaufzeit — für Momentanwerte unbrauchbar,
  dafür `top` verwenden.
- Frisch gestartete Prozesse verfälschen jede Messung; erst einschwingen lassen.
- Ein Werkzeug, das nur eine Ebene sieht (etwa React DevTools bei
  handgeschriebenem DOM-Rendering), zeigt zum Problem darunter gar nichts an.

**Gemessene Zahlen gehören in den Code**, nicht nur in die Antwort im Chat. Ein
Schwellwert ohne die Messung dahinter ist beim nächsten Mal eine willkürliche
Zahl, an der niemand zu drehen wagt. Also: Wert, Einheit, und wogegen gemessen
wurde.

**Internetzugriffe vorab planen und zuerst erledigen.** Wenn eine Aufgabe
Recherche im Netz erfordert, wird vor der eigentlichen Arbeit überlegt, welche
Informationen gebraucht werden — und die werden am Stück zu Beginn geholt.
Danach läuft die Umsetzung ohne weitere Zugriffe.

Der Grund: Der Nutzer schaltet das Internet bewusst manuell frei. Ein Zugriff
mitten in der Arbeit bedeutet, dass er erneut gefragt wird und die Arbeit so
lange steht. Also lieber einmal am Anfang zu viel nachschlagen als dreimal
zwischendurch. (Vor jedem Zugriff gilt weiterhin: vorher fragen.)

**Ausdrücklich Bescheid geben, sobald das Netz nicht mehr gebraucht wird**,
damit der Nutzer es wieder abschalten kann. Dazu gehört auch, vorher zu prüfen,
ob wirklich alles da ist — eine heruntergeladene Abhängigkeit etwa erst dann als
erledigt melden, wenn sie im lokalen Cache liegt *und* sich einbinden lässt.

**Erst verstehen, dann reparieren.** Eine Symptombehandlung, die zufällig wirkt,
ist schlechter als keine — sie verdeckt die Ursache. Wenn ein Fix wirkt, aber
unklar ist warum, ist das ein offener Punkt und kein Ergebnis.

## Aufbau von Quellcodedateien

**Zeilenlänge höchstens 80 Zeichen.** Gilt für Quellcode — Code und Kommentare
gleichermaßen —, nicht für Dokumentation. Wer die Grenze reißt, hat meist eine
zu tief verschachtelte Form oder einen zu lang geratenen Namen — beides ist der
eigentliche Befund, die Grenze nur der Anlass.

Markdown wird trotzdem umbrochen, aus einem anderen Grund: Eine Änderung an
einem unumbrochenen Absatz ist im Diff eine einzige geänderte Zeile, und man
sieht nicht, was sich geändert hat. Eine harte Grenze gibt es dafür nicht — 80
Zeichen sind hier nur derselbe Wert wie oben, damit man sich nicht zwei merken
muss.

**Einrückung mit Leerzeichen, nie mit Tabulatoren.**

**Clean Code:** Der Quellcode ist möglichst selbstsprechend und kleinteilig
organisiert. Konkret heißt das: sprechende Namen statt Kommentare, die einen
kryptischen Namen erklären; kurze Funktionen mit einer klaren Aufgabe;
Verschachtelung durch benannte Zwischenschritte auflösen. Eine Funktion, die man
beim Lesen nicht am Stück im Kopf behalten kann, ist zu groß.

### Kommentare mit besonderer Bedeutung

Drei Präfixe, die im Quelltext gesucht werden können:

| Präfix | Bedeutung |
|---|---|
| `NOTE:` | Erläuterung, die der Code nicht direkt hergibt — Hintergrund, verworfene Alternative, Messung hinter einer Zahl. |
| `WATCHOUT:` | Wesentliche Restriktion oder Information zur Umsetzung oder für künftige Erweiterungen. Wer hier weiterarbeitet, muss das gelesen haben. |
| `FIXME:` | Bekannter Fehler oder bekanntes Problem. |

Die Präfixe sind für Kommentare mit *besonderer* Bedeutung gedacht, nicht für
jeden beiläufigen Hinweis — sonst tragen sie nichts mehr. Der Unterschied
zwischen `NOTE:` und `WATCHOUT:`: Ein `NOTE:` erklärt, ein `WATCHOUT:` warnt.
Wenn das Übersehen des Kommentars zu einem Fehler führen kann, ist es ein
`WATCHOUT:`.

## Code

**Kommentare erklären das Warum, nicht das Was.** Der Code sagt bereits, was
passiert. Wertvoll ist, was man ihm nicht ansieht: die verworfene Alternative,
die Messung hinter einer Zahl, die Fußangel der Plattform, der Grund für einen
scheinbaren Umweg.

**Kommentare kurz und knapp, für Entwickler.** Keine Rückschau auf abgelöste
Fassungen — was der Code früher tat, steht in der Versionsgeschichte. Messwerte,
`WATCHOUT` und `NOTE` bleiben: Sie beschreiben, was jetzt gilt, und ersparen das
erneute Messen.

**Nicht offensichtliche Entscheidungen gehören dokumentiert** — an Ort und
Stelle im Code, und wenn sie die Architektur betreffen zusätzlich in der
Projekt-CLAUDE.md. Faustregel: Wenn jemand die Stelle in einem halben Jahr für
einen Fehler halten und „aufräumen" könnte, fehlt der Kommentar.

**Konstanten mit Bedeutung bekommen einen Namen** und eine Erklärung, keine Zahl
im Ausdruck. Besonders bei Schwellwerten, Zeitspannen und Grenzen.

**Eine Zahl, ein Ort.** Wenn derselbe Wert an zwei Stellen gebraucht wird (etwa
in CSS und im Code), wird er an einer Stelle definiert und an der anderen
gelesen. Doppelt gepflegte Werte laufen beim ersten Nachjustieren auseinander.

**Dem umgebenden Code folgen** — Namensgebung, Kommentardichte, Idiome. Ein
Projekt soll wie aus einer Hand wirken, nicht wie eine Sammlung von
Handschriften.

**Fachbegriffe in Fließtext werden mit Markdown hervorgehoben** — Bezeichner,
Funktionsnamen und Schlüsselwörter in Backticks (`` `impulse` ``, ``
`retracement` ``), statt sie unmarkiert im Satz stehen zu lassen. Das gilt für
Kommentare und Docstrings ebenso wie für Texte, die der Code selbst erzeugt und
die später als Markdown gerendert werden — etwa Erklärungen, die eine Analyse zu
ihrem Ergebnis mitliefert.

## Bibliotheken und Frameworks

**re-frame:** Die offizielle Dokumentation unter
<https://day8.github.io/re-frame/re-frame/> ist die Grundlage der
Implementierung. Nicht nach Gefühl oder nach dem, was gerade funktioniert -- die
dort beschriebenen Muster gelten, insbesondere:

- Event-Handler sind **pure**. Jeder Seiteneffekt gehört in einen Effect
  (`reg-fx`), jeder Zugriff auf die Außenwelt in einen Coeffect (`reg-cofx` plus
  `inject-cofx`). Dateisystem, Zeit, Zufall, DOM: alles davon.
- In `app-db` liegen **Daten**, keine Funktionen und keine veränderlichen
  Objekte. Sonst sind Serialisierung, Zeitreise-Debugging und die
  Entwicklerwerkzeuge hinüber.
- Subscriptions werden geschichtet: Extraktoren lesen aus `app-db`, abgeleitete
  Sichten bauen über `:<-` darauf auf. Nicht in der View rechnen und nicht in
  einer Subscription erneut subscriben.
- Ein Handler schreibt **nur, was er wirklich ändern muss**, und niemals den
  gesamten app-state. Er hinterlässt einen in sich stimmigen Zustand -- alles
  andere bleibt, wie es war. (Die eine Ausnahme ist das Initialisierungs-Event,
  das den Ausgangszustand herstellt.)
- Fehlt für einen Seiteneffekt der passende Effect, wird einer geschrieben. „Nur
  diesmal direkt im Handler" ist der Anfang vom Ende der Testbarkeit. Dasselbe
  gilt für veränderliche Eingangsdaten: dafür gibt es Coeffects.

Bei Zweifeln in der Dokumentation nachsehen, statt zu raten -- sie ist
ausführlich und begründet ihre Muster.

**Events, die Events auslösen, vermeiden.** Ein Handler, der seine Arbeit an
`{:dispatch [:anderes-event]}` weiterreicht, zerlegt einen fachlichen Vorgang in
mehrere Zustandsübergänge. Das erschwert das Lesen (was passiert eigentlich?),
die Tests (Reihenfolge und Zwischenzustände) und macht aus einem Undo-Schritt
mehrere. Stattdessen ein **dediziertes Event** je Vorgang.

Die gemeinsame Logik wird dabei als gewöhnliche Funktion ausgelagert und von den
beteiligten Handlern aufgerufen -- das ist der Regelfall und kostet nichts.
Lässt sie sich nicht sinnvoll herausziehen, ist im Zweifel etwas Verdopplung im
Handler besser als eine Event-Kette.

Davon unberührt: Ein *Effect*, der nach getaner Arbeit ein Event auslöst
(asynchrones Ergebnis, IPC-Antwort), ist genau richtig -- das ist kein Handler,
der weiterreicht, sondern die Rückmeldung aus der Außenwelt.

**Der Client zeigt an und nimmt Eingaben entgegen — die Businesslogik liegt im
Server.** Der Client bildet sie nicht ein zweites Mal ab. Ein Server-Roundtrip
je Eingabe ist dafür der richtige Preis:

- Der Roundtrip trägt wenig Daten und dauert entsprechend kurz.
- Er sichert die Persistenz. Es gibt **eine** Datenwahrheit, nicht zwei Stände,
  die auseinanderlaufen können.
- Der Client lädt nur, was er anzeigt. Rechnete er selbst, müsste er die Daten
  dafür erst alle holen.
- Server und Client können nicht auseinanderlaufen, weil im Client keine zweite
  Abbildung der Fachlichkeit liegt, die man nachziehen müsste.

Daraus folgt auch, wo der Code liegt: Fachlogik, die nur der Server ausführt,
ist ein Thema **des Servers**. Sie wird nicht zum geteilten Baustein und nicht
zu `.cljc`, bloß weil sie theoretisch auch im Browser liefe. Erst ein
tatsächlicher zweiter Aufrufer ändert das.

**Electron: Aktionen im Main-Prozess laufen über eine IPC-Brücke.** Der Renderer
greift nie direkt zu, sondern löst ein Event aus; ein Effect schickt einen
benannten Befehl samt Nutzdaten über IPC, der Main-Prozess führt ihn aus und
antwortet. Das Muster ist bewusst das einer HTTP-Anfrage: Befehl, Nutzdaten,
Antwort als Event. So bleibt die Prozessgrenze eine einzige, überschaubare
Stelle, statt sich über die Views zu verteilen, und der Main-Prozess bietet eine
benannte Befehlsliste statt verstreuter `ipcMain`-Handler.

### Eigene Bibliotheken (jtkDvlp)

Vor einer selbstgebauten Lösung hier nachsehen -- diese Pakete decken die
wiederkehrenden Fälle ab und sind aufeinander abgestimmt.

| Bibliothek | Wofür | Woran man den Anlass erkennt |
|---|---|---|
| [core.async-helpers](https://github.com/jtkDvlp/core.async-helpers) (`jtk-dvlp/core.async-helpers`) | `go`/`<!` mit Fehlerfortpflanzung über den go-Block-Stapel; `<p!`, `p->c`, `c->p` für Promise-Interop, `cb->c` für Callbacks | Irgendwo steht `.then`/`.catch`, oder eine Callback-API soll in eine Ablauffolge passen |
| [re-frame-async-coeffects](https://github.com/jtkDvlp/re-frame-async-coeffects) (`net.clojars.jtkdvlp/re-frame-async-coeffects`) | Asynchrone Eingangsdaten als **Coeffect** statt als Effect-Kette; `reg-acofx`, `reg-acofx-by-fx`, `inject-acofxs` laden mehrere Quellen nebenläufig | Ein Event braucht Daten von außen und die Antwort löst wieder ein Event aus -- die klassische Kette aus Laden, Erfolg, Weiterverarbeiten |
| [re-frame-tasks](https://github.com/jtkDvlp/re-frame-tasks) (`jtk-dvlp/re-frame-tasks`) | Langläufer als benannte Tasks: `as-task`, `wait-for`, Subscription `::running?` | Es gibt handgebaute `:loading?`-Flags in app-db, oder ein Event muss warten, bis etwas anderes fertig ist |
| [cljs-workers](https://github.com/jtkDvlp/cljs-workers) (`jtk-dvlp/cljs-workers`) | Web-Worker-Pool mit core.async-Kanälen; `create-pool`, `do-with-pool!`, im Worker `register`/`bootstrap` | Rechenarbeit blockiert den Hauptthread **und** die Daten überleben die Worker-Grenze (structured clone) |
| [re-frame-worker-fx](https://github.com/jtkDvlp/re-frame-worker-fx) (`jtk-dvlp/re-frame-worker-fx`) | Der `:worker`-Effect als re-frame-Anbindung von cljs-workers | Wie oben, aber der Anstoß kommt aus einem Event |
| [transit](https://github.com/jtkDvlp/transit) (`net.clojars.jtkdvlp/transit`) | `clj->transit` / `transit->clj` ohne Reader/Writer-Aufbau, mit clj-time-Handlern | Daten müssen über eine Grenze (Datei, IPC, Netz) und danach wieder Clojure-Daten sein |
| [re-frame-components](https://github.com/jtkDvlp/re-frame-components) (`jtk-dvlp/re-frame-components`) | Bausteine für typische Web-Apps: `clock` (Uhr-Coeffect, `ago`/`datetime`, zieht `cljs-time`), `notifications` (Toast-Stack, hängt an `clock`), `viewport` (verfolgt `window.scroll`/`resize`), `forms` (Label-Input, Dispatch-Helfer) | Vor einer eigenen Toast-/Uhr-/Formular-Lösung nachsehen -- aber gezielt je Namespace, nicht als Ganzes: `clock`+`notifications` ziehen `cljs-time` für ein einzelnes Datumsformat mit, `viewport` passt nur zu einer scrollenden Seite (nicht zu einem Fenster mit eigener Größenverfolgung je Komponente) |

**WATCHOUT:** `core.async-helpers` und `clojure.core.async` nicht mischen. Die
Fehlerfortpflanzung lebt davon, dass Ausnahmen als Wert durch den Kanal gehen;
ein `core.async/<!` dazwischen nimmt den Wert stumm entgegen, und der Fehler
verschwindet.

**reagent:** Die Oberfläche ist idiomatisch nach
<https://github.com/reagent-project/reagent> umgesetzt. Ergänzend:

- **Dynamische Inhalte (Listen) mit `for` erzeugen und in `doall` wickeln.**
  `for` ist lazy; wird die Sequenz erst außerhalb des reaktiven Kontexts
  realisiert, bemerkt reagent die Abhängigkeit zum Atom nicht und die Komponente
  aktualisiert sich nicht mehr zuverlässig. `doall` erzwingt die Auswertung
  dort, wo sie hingehört.

- **Form 2 und Form 3 müssen die Parameter der View wiederholen.** Die äußere
  Funktion sieht die Argumente nur beim ersten Aufruf; was die innere
  Render-Funktion nicht selbst in ihrer Parameterliste stehen hat, bleibt für
  immer auf dem Erstwert stehen. Der Fehler ist heimtückisch, weil die
  Komponente beim ersten Rendern richtig aussieht.

- **Form 3 nur, wo es sein muss.** Sie ist mächtig und entsprechend kompliziert;
  in etwa einem Prozent der Fälle braucht man sie wirklich. Der typische
  berechtigte Anlass ist die Anbindung einer Fremdbibliothek oder einer
  Ressource mit eigenem Lebenszyklus (Canvas, WebGL, Karte, Editor). Sonst
  reicht Form 2.

- **Views laden keine Daten.** Die Daten sind da, bevor die View gerendert wird
  -- geladen wird über Events/Effects davor. Für die Wartezeit gehört eine
  schematische Platzhalter-View eingeblendet, nicht ein Ladevorgang in den
  Render-Pfad.

- **Jedes Element einer Liste braucht einen `:key`, und der muss eine echte Id
  sein.** Kein laufender Index: React ordnet damit beim Einfügen oder Sortieren
  den falschen Zustand zu. Gibt es keine natürliche Id, wird eine aus den Daten
  gebildet -- oder die Daten selbst dienen als Schlüssel.

- **Kleinteilige Views bauen.** Nur so kann reagent gezielt neu rendern, statt
  einen großen Baum anzufassen; nebenbei liest sich das besser. Für
  Subscriptions gilt dasselbe. Was tatsächlich neu gerendert wird, zeigen die
  React DevTools -- unerwartete Bereiche fallen dort sofort auf.

- **Eine View bekommt nur die Daten, die sie und ihre Sub-Views wirklich
  brauchen.** Alles Weitere löst Re-Renderings aus, sobald sich irgendetwas
  daran ändert. Dazu passen dedizierte, schmale Subscriptions.

- **Event-Handler in Props mit `reagent/partial` bilden, nicht mit `#(...)` oder
  `(fn [] ...)`.** Ein Closure ist bei jedem Rendern eine neue Instanz und damit
  ungleich zur vorherigen -- die Komponente rendert neu, obwohl sich nichts
  geändert hat. `reagent/partial` vergleicht sich über Funktion und Argumente
  und bleibt gleich.

## Clojure und ClojureScript

### Namespaces

**Jeder Namespace trägt eine kurze Beschreibung** seines Sinns und Zwecks als
Docstring — was gehört hier hinein, was nicht.

**Ein Namespace, ein Thema.** Eigene Themen kommen in eigene Namespaces, gerne
verschachtelt, wenn die Themen es zulassen oder erfordern. Ein Namespace, der
zwei Dinge tut, wird geteilt.

#### Nach Thema gliedern, nicht nach Schicht

**Die Verzeichnisstruktur folgt den Themen der Anwendung**, nicht ihren
technischen Schichten. Also `themes/`, `files/`, `buffer/` — und *nicht*
`views/`, `events/`, `subs/` als Sammelbecken über alle Themen hinweg.
Sammelbecken wachsen mit jeder Funktion, ohne je eine Grenze zu ziehen: Wer ein
Thema versteht oder ändert, sucht sonst in drei Dateien nach drei Zeilen. Was
zusammen geändert wird, gehört zusammen.

Der Aufbau je Thema:

| Ebene | Inhalt |
|---|---|
| `<thema>` | Der Hauptnamespace des Themas — das, was von außen benutzt wird. |
| `<thema>.<teil>` | Teilbereiche, die für sich stehen (`buffer.highlight`, `files.search`). |

**Ein Thema beginnt als *ein* Namespace** — mit allem darin, auch wenn das
mehrere Schichten sind (Ansicht, Events, Subscriptions). Erst wenn er zu groß
wird, um ihn am Stück zu lesen, zerfällt er in Teile, und dann entlang der
Schichten (`workspace` als Ansicht, `workspace.events`, `workspace.subs`).
Vorher zu teilen kostet nur Sprünge zwischen Dateien.

**Was mehrere Themen brauchen, steht eine Ebene höher** als eigener Namespace —
nicht in dem Thema, das es zufällig zuerst brauchte. Ein Thema, das aus einem
anderen liest, ist ein Hinweis auf so einen gemeinsamen Nenner.

**Bibliotheken** haben einen Hauptnamespace, der so heißt wie die Bibliothek;
alles Weitere liegt darunter. Also `fileutils` als Hauptnamespace und
`fileutils.names` als Teilbereich.

**Anwendungen** haben immer `main` und `core`:

| Namespace | Aufgabe |
|---|---|
| `main` | Einstiegspunkt — die `main`-Funktion bzw. das Mounten. Startet das System. |
| `core` | Beschreibt das System: fasst die Hauptkomponenten zusammen. |
| `components` | Darunter je Komponente ein eigener Namespace. |

Die Hauptkomponenten werden als
[stuartsierra/component](https://github.com/stuartsierra/component) abgebildet.
`core` beschreibt das System, `main` startet es.

**Die Komponenten liegen unter `components`** — eine je Namespace, benannt wie
die Komponente: `<anwendung>.components.window`, `<anwendung>.components.ui`.
Darin steht **das ganze Thema**: die Arbeit, der `defrecord` mit `start`/`stop`
und eine Konstruktorfunktion `component`. Die Komponente *ist* das Thema, nicht
bloß dessen Lebenszyklus.

Den Lebenszyklus vom Thema zu trennen und daneben einen zweiten Namespace zu
legen, ist erst dann richtig, wenn es dafür einen echten Aufrufer gibt —
jemanden, der das Thema ohne laufendes System braucht. Ohne den entsteht nur
eine Hülle aus zwei Zeilen Delegation, die beim Lesen zwei Dateien statt einer
kostet.

Was *mehrere* Komponenten brauchen, wird ohnehin zu einem eigenen Namespace
daneben — nicht wegen des Lebenszyklus, sondern weil es geteilt wird.

`core` liest sich damit als reine Aufzählung:

```clojure
(component/system-map
 :menu
 (menu/component)

 :window
 (component/using (window/component) [:menu]))
```

Hat ein Prozess **mehrere Systeme** (etwa Electron mit Haupt- und
Renderer-Prozess), bekommt jedes seinen eigenen Zweig mit `main`, `core` und
`components` darunter.

### Requires und Imports

`:require` und `:import` werden umgebrochen (ein Eintrag je Zeile) und **nach
Stabilität gruppiert**: erst Clojure selbst, dann Fremdbibliotheken, dann die
eigenen Quellen. Innerhalb einer Gruppe alphabetisch. Zwischen den Gruppen eine
Leerzeile.

```clojure
(ns jtkdvlp.beispiel.core
  "Kurz, wofür dieser Namespace da ist."
  (:require
   [clojure.string :as string]

   [re-frame.core :as re-frame]

   [jtkdvlp.beispiel.buffer :as buffer]
   [jtkdvlp.beispiel.files :as files]))
```

**Aliase sind sprechend.** Abkürzungen werden vermieden; wo eine steht, muss sie
eindeutig und unmissverständlich sein. `:refer` gezielt einsetzen, nicht
pauschal.

### Formatierung

**Funktionsaufrufe:** Parameter durch Leerzeichen trennen — oder durch Umbrüche,
wenn das der Übersichtlichkeit dient.

**`let`-Blöcke:** Ein einzelnes Binding steht einzeilig. Bei mehreren Bindings
werden Symbol und Wert umgebrochen, zwischen den Bindings steht eine Leerzeile,
und zwischen Binding-Vector und Body ebenfalls.

```clojure
(let [x (berechne-etwas a b)]
  (verwende x))

(let [breite
      (miss-breite element)

      hoehe
      (miss-hoehe element)]

  (zeichne breite hoehe))
```

**Threading-Makros bevorzugen**, besonders bei Datenpipelines — statt ineinander
geschachtelter Aufrufe. Funktionen werden dabei immer geklammert notiert, auch
ohne weitere Parameter:

```clojure
(-> daten
    (filtere-gueltige)
    (sortiere-nach :name)
    (nimm 10))
```

### Separation of Concerns

Funktionen, Namespaces und Schichten haben je einen Fokus und behandeln auch nur
diesen. Eine Funktion sollte nicht gleichzeitig komplex rechnen, Seiteneffekte
ausüben und einen Wert zurückliefern — das sind drei Aufgaben und damit drei
Funktionen. Eine Trennung ist nicht in jedem Fall möglich; Ausnahmen bestätigen
die Regel.

### Namenskonventionen

**Namen möglichst nach der Fachlichkeit wählen, nicht nach der Technik.** Ein
Name soll sagen, was etwas in der Domäne bedeutet, nicht wie es implementiert
ist oder aus welcher Standard-Datenstruktur es stammt (`leaf`/`node` verrät nur
"Baum", nicht "Fenster mit Größenanteil"). Technische Namen sind zweitrangig —
passend nur dort, wo es keine bessere fachliche Entsprechung gibt oder der Code
selbst generische Technik ist (z.B. eine Zipper-Hilfsfunktion).

Namen stehen in Kleinbuchstaben, Wörter werden durch Bindestriche getrennt.
Funktionsnamen enthalten meist ein Verb.

| Zeichen | Position | Bedeutung |
|---|---|---|
| `?` | am Ende | Prädikat — liefert **immer** einen booleschen Wert |
| `!` | am Ende | Seiteneffekt |
| `?` | am Anfang | core.async-Channel |
| `!` | am Anfang | veränderliches Symbol, etwa ein Atom |
| `**` | umschließend | dynamische Var |

## Tests

**Ein Fehler, der einmal auftrat, bekommt einen Test.** Der Test hält fest,
*warum* es ihn gibt: was schiefging und woran man es merkte. Das ist wichtiger
als der Testname.

**Testen, was kaputtgehen kann, nicht was leicht zu testen ist.** Bei
Performance-Regressionen heißt das: gegen eine großzügige Grenze prüfen, die
einen echten Rückfall fängt (Größenordnung), nicht gegen einen knappen Wert, der
bei jeder Schwankung ausschlägt.

**Tests dürfen keine festen Werte duplizieren**, die anderswo als Konstante
stehen — sonst prüfen sie nach der nächsten Änderung etwas anderes als gedacht.
Positionen und Grenzen aus der Konstante ableiten.

## Zusammenarbeit

**Umfang ist der Auftrag.** Nicht stillschweigend erweitern und nicht
stillschweigend kürzen. Fällt unterwegs etwas auf, das über den Auftrag
hinausgeht: benennen, nicht einfach miterledigen.

**Ergebnisse ehrlich berichten.** Was nicht funktioniert, wird gesagt — mit der
Ausgabe dazu. Was übersprungen wurde, wird gesagt. Eine frühere Falschaussage
wird richtiggestellt, sobald sie auffällt, ohne Umschweife und ohne
Selbstgeißelung.

**Vor schwer umkehrbaren Schritten fragen.** Löschen, Überschreiben, alles nach
außen Wirkende.

**Über Pull Requests arbeiten, nicht direkt committen.** Änderungen gehen auf
einem Branch und über einen PR in den Hauptzweig, nicht per Direkt-Commit
dorthin. Das hält den Hauptzweig jederzeit in einem Zustand, den andere
ungeprüft übernehmen können.

**Der Hauptzweig ist der Default-Branch des Repos.** Dorthin geht ein PR. Die
eine Ausnahme ist eine Änderung, die ausdrücklich zu einer Wartungslinie gehört
— ein Fix für eine ältere, noch gepflegte Major-Version. Dann ist für diesen
Vorgang deren Zweig der Hauptzweig, und alles, was hier über den Hauptzweig
steht, gilt sinngemäß für ihn.

**Releases sind Tags, keine Zweige.** Ein Tag ist unveränderlich und markiert
einen Punkt auf dem Stamm; an einem Release arbeitet niemand weiter. Ein Zweig
entsteht erst, wenn eine ältere Linie noch Fixes bekommt, während der Stamm
schon weiter ist — und erst dann, nicht vorsorglich.

**Der Default-Branch wird nicht umgestellt, um die Anzeige zu reparieren.** Die
Versuchung ist da: GitHub rendert die README des Default-Branch, und wenn der
dem letzten Release voraus ist, liest ein Besucher Doku zu Code, den er noch gar
nicht bekommen kann. Den stabilen Zweig zum Default zu machen tauscht das aber
nur gegen ein Arbeitsproblem — PRs zielen dann standardmäßig falsch, Beitragende
zweigen von der falschen Stelle ab, und die halbe Oberfläche geht davon aus,
dass im Default entwickelt wird.

Dagegen helfen zwei Mittel, die beide nichts kosten: ein CHANGELOG mit einem
`Unreleased`-Abschnitt, in dem der Unterschied sichtbar steht, statt zu täuschen
— und ein Verweis auf die versionierte Doku, bei Clojure also cljdoc, das je
Version rendert. Die Version im Installationsschnipsel gehört als Badge in die
README, damit dort automatisch die veröffentlichte steht und nicht die im Zweig.

Läuft die Release-Automatisierung aus dem nächsten Abschnitt, übernimmt der
offene Release-PR die Rolle des `Unreleased`-Abschnitts: Dort steht, was noch
nicht veröffentlicht ist, samt der Version, die es bekommen wird. Von Hand
gepflegt wird das Changelog dann nicht mehr.

**Erst ein offizieller Feature-Branch, dann die Arbeit.** Vor der ersten
Änderung an einem Feature wird dafür ein eigener Branch angelegt; alle Commits
dazu gehen auf diesen Branch. Der PR entsteht erst, wenn das Feature fertig ist
— nicht als leerer PR zu Beginn.

**Ein PR ist ein abgeschlossener Vorgang, kein einzelner Schritt.** Mehrere
Commits darin sind der Normalfall, nicht die Ausnahme. Lieber ein PR mit fünf
Commits als fünf PRs mit je einem: Wer die Änderung beurteilen soll, braucht das
Ganze vor sich, und jeder PR kostet für sich eine Runde Lesen, Warten und
Mergen. Dass es dadurch weniger PRs werden, ist kein Nachteil, solange jeder für
sich ein Thema abschließt.

Die Grenze zieht der Hauptzweig: Ein PR ist richtig geschnitten, wenn der
Hauptzweig nach dem Merge in einem sinnvollen Zustand ist — nichts halb
Eingebautes, nichts, das erst der nächste PR benutzbar macht. Eine Aufteilung,
die diesem Maßstab nicht standhält, ist keine.

**Die Commits im PR sind die Lesereihenfolge.** Jeder ein nachvollziehbarer
Schritt mit eigener Botschaft, und wo es geht für sich lauffähig — so kann man
den PR Commit für Commit lesen statt als einen Klumpen Diff. Kein `wip`, kein
`fix`, kein `fix2`: Was nur Zwischenstand war, wird vor dem PR zusammengefasst.

**PRs nicht aufeinander stapeln.** Jeder PR geht gegen den Hauptzweig. Ein PR
auf einen anderen PR sieht ordentlich aus, ist beim Mergen aber eine Falle: Wird
der untere zuerst gemergt, zeigt der obere weiterhin auf dessen Branch — der
Merge läuft dann anstandslos durch und landet trotzdem nicht im Hauptzweig.
Auffallen tut das erst, wenn dort etwas fehlt.

Geht es ausnahmsweise nicht anders, gehört die Merge-Reihenfolge in die
PR-Beschreibung, und nach jedem Merge wird geprüft, ob die Änderung wirklich im
Hauptzweig steht — nicht nur, ob der Merge geklappt hat.

**Den Branch nach dem Merge löschen.** Er hat seinen Zweck erfüllt, sein Inhalt
steht im Hauptzweig. Stehengelassene Branches sammeln sich an, und nach ein paar
Wochen weiß niemand mehr, welcher davon noch etwas enthält, das nirgends
angekommen ist.

## Versionierung und Release

Gilt für jede veröffentlichte Bibliothek. **Die Versionsnummer wird nicht von
Hand gepflegt** — sie ergibt sich aus den Commit-Nachrichten. Eine von Hand
gesetzte Zahl wird irgendwann vergessen, und dann trägt das Paket eine andere
als der Tag.

### Conventional Commits

Jede Commit-Nachricht folgt
[Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/):

```
<typ>[(<bereich>)][!]: <beschreibung>
```

| Typ | Wirkung auf die Version |
|---|---|
| `fix:` | Patch |
| `feat:` | Minor |
| beliebiger Typ mit `!`, oder ein `BREAKING CHANGE:`-Footer | Major |
| `build:`, `chore:`, `ci:`, `docs:`, `perf:`, `refactor:`, `revert:`, `style:`, `test:` | keine — steht aber im Changelog |

**Das `!` hängt am Typ, nicht an `feat`.** `fix!:` ist ein Bugfix, der bricht,
und ergibt genauso eine Major-Version.

**Die Regel gilt für jeden einzelnen Commit, nicht nur für den PR-Titel.** PRs
werden gemergt und nicht gesquasht — jeder Commit des Branches landet also auf
dem Hauptzweig und wird dort gelesen. Eine CI-Prüfung sieht das bei jedem PR
nach; Merge-Commits sind ausgenommen, deren Betreff stammt von git.

Das ersetzt nicht, was oben über Commits steht — ein Commit bleibt ein
nachvollziehbarer Schritt mit eigener Botschaft. Der Typ kommt davor, der Rest
bleibt, wie er war.

### Der Ablauf

Drei Schritte, und keiner veröffentlicht ohne einen PR, den jemand angesehen
hat:

| Schritt | Was passiert |
|---|---|
| Merge auf den Hauptzweig | [release-please](https://github.com/googleapis/release-please) öffnet bzw. aktualisiert einen Release-PR: die nächste Version in der Build-Datei, die Changelog-Einträge aus den Commits seit dem letzten Tag. |
| Release-PR mergen | Tag und GitHub-Release entstehen. |
| Derselbe Workflow-Lauf | testet den getaggten Stand und veröffentlicht ihn. |

Der Release-PR ist damit die Stelle, an der ein Release entschieden wird — und
nicht ein Commit, der versehentlich das Falsche auslöst.

**WATCHOUT: Der Veröffentlichungs-Job gehört in denselben Workflow-Lauf** wie
release-please, nicht in einen eigenen `on: release`-Workflow. Ein Release, das
der `GITHUB_TOKEN` erzeugt, löst keine weiteren Workflows aus — ein getrennter
Workflow liefe stillschweigend nie.

**Der Job checkt den Tag aus, nicht den Zweig.** Bis er läuft, kann auf dem
Hauptzweig schon der nächste Commit liegen; veröffentlicht wird genau der
Stand, der das Release ist.

**Vor dem Veröffentlichen wird ein zweites Mal getestet**, und zwar dasselbe,
was die CI prüft. Auf dem PR lief der Test gegen den Stand *vor* dem
Versions-Bump, hier gegen das Artefakt, das gleich hinausgeht. Veröffentlichen
ist nicht rücknehmbar — die üblichen Paket-Repositories nehmen dieselbe Version
kein zweites Mal an.

### Drei Stellen, an denen es leise schiefgeht

- **Die Version in der Build-Datei braucht eine Anmerkung**
  (`x-release-please-version`), damit release-please sie findet. Ohne sie wird
  nur das Changelog fortgeschrieben, und das Paket trägt weiter die alte Zahl.
- **Das Tag-Format muss zu den vorhandenen Tags passen.** Heißen sie `3.6.1`,
  gehört `include-v-in-tag: false` in die Konfiguration; mit `v` fände
  release-please die Historie nicht wieder und finge bei `1.0.0` an. Der
  **Anzeigename des Releases hat eine eigene Option**
  (`include-v-in-release-name`) — wer nur die Tag-Option setzt, bekommt einen
  Tag `4.0.0` und darüber ein Release namens `v4.0.0`.
- **Die Manifest-Datei gehört der Maschine.** Sie hält den zuletzt
  veröffentlichten Stand und wird nicht von Hand editiert.

### Zugangsdaten

Die Zugangsdaten für das Paket-Repository stehen als **Repository-Secrets** und
kommen über Umgebungsvariablen in den Build. Sie liegen nie in einer Datei im
Repo, auch nicht in einer ignorierten.

Was ein Secret leistet und was nicht: GitHub speichert es verschlüsselt, zeigt
es nach dem Anlegen niemandem wieder an und maskiert es in Logs; ein PR aus
einem Fork bekommt es nicht. Wer aber Schreibrechte auf das Repo hat, kann es
ausschleusen — ein Workflow, der den Wert irgendwohin schickt, ist ein
gewöhnlicher Commit. Deshalb ein **Deploy-Token, eingeschränkt auf das eine
Artefakt**, und nicht das Kontopasswort.

**Signieren bleibt aus, solange kein Schlüssel im Lauf liegt.** Ein
umgestelltes Flag reicht dafür nicht, und die üblichen Paket-Repositories
verlangen keine Signatur.

### Was im Projekt bleibt

Die Richtlinie beschreibt den Vorgang. Projektspezifisch ist nur, **woran** er
hängt: welche Datei die Version trägt, wie das Paket-Repository heißt, wie die
Secrets heißen und welcher Befehl veröffentlicht. Das steht in der
Projekt-CLAUDE.md — und zwar nur das.
