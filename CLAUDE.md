# Programmierrichtlinien (projektübergreifend)

Gilt für alle Projekte. Projektspezifische CLAUDE.md-Dateien ergänzen oder überschreiben das hier bewusst — im Konflikt gewinnt die Projektdatei.

**Regelmäßig auf Aktualität prüfen.** Diese Datei wird von den Projekten
per Import eingebunden, nicht kopiert. Trotzdem lohnt sich zu Beginn einer
Arbeitssitzung ein kurzer Blick, ob das lokale `claude-guidelines`-Repo den
aktuellen Stand hat (`git fetch` + Vergleich mit dem Remote-Branch) — sonst
arbeitet man unbemerkt gegen einen veralteten Stand der Richtlinien. Liegt
ein neuerer Stand vor, wird er gezogen, bevor die eigentliche Arbeit
beginnt.

Das gilt nicht nur beim Start: Eine Sitzung kann sich über Tage hinziehen.
Deshalb wird auch zwischendurch — etwa vor größeren Arbeitsschritten oder
in regelmäßigen Abständen — erneut geprüft, nicht nur einmal zu Beginn.

## Projektstruktur

| Verzeichnis | Inhalt |
|---|---|
| `src/` | Quellcode. Darunter **immer** die Artifact-Group als Pfad: `src/<bereich>/de/jtkdvlp/<artifact>/…` |
| `resources/` | **Nur handgeschriebene** Assets. Hier liegt niemals ein Kompilat. |
| `target/` | **Alles Generierte** — Kompilate und kopierte Assets. Die Struktur darunter ist frei. Muss jederzeit löschbar sein, ohne dass etwas verloren geht. |
| `dev/` | Alles, was zur Entwicklung gehört, aber nicht zum Produkt. Gibt es immer. |
| `dev/tmp/` | Wegwerf-Dateien: Screenshots, Logs, Messskripte. Nicht versioniert. |
| `dev/sample/` | Beispiel- und Testdateien, etwa große Dateien für Performance-Messungen. |
| `scripts/` | Build-, Start- und Hilfsskripte. |

**Artifact-Group ist `de.jtkdvlp`.** Der Artifact-Name folgt darunter, also `de.jtkdvlp.<artifact>` als Namespace-Wurzel und `de/jtkdvlp/<artifact>/` als Pfad.

**Temporäres gehört nach `dev/tmp/`**, nicht nach `/tmp` und nicht ins Projektwurzelverzeichnis. Wenn eine Datei ausnahmsweise woanders liegen muss (etwa weil ein Werkzeug `dev/tmp` ausblendet), gehört der Grund als Kommentar in die `.gitignore`.

**Die Trennung `resources/` ↔ `target/` ist strikt.** Ein Build kopiert aus `resources/` nach `target/` — nie umgekehrt, und nie erzeugt er etwas in `resources/`. Das hält `target/` jederzeit löschbar und `resources/` frei von Dingen, die man versehentlich mitversioniert.

## Änderungen an fremdem Hoheitsgebiet

Ohne ausdrückliche Freigabe **nicht** anfassen:

- Skripte unter `scripts/`
- Berechtigungsdateien (`.claude/settings.json` und Ähnliches)
- Alles außerhalb des Projektverzeichnisses

Der Grund ist bei allen dreien derselbe: Es sind Dinge, die Vertrauen oder Automatisierung tragen. Wenn eine Änderung dort nötig ist, vorher fragen und dabei sagen, welche Zeilen es betrifft.

## Vorgehen

**Messen statt raten.** Bei Performance-, Speicher- oder Timing-Fragen gilt keine Vermutung als Befund, bevor sie gemessen ist — auch nicht die eigene. Die Ursache liegt regelmäßig woanders als erwartet. Erst messen, dann ändern, dann erneut messen.

**Das Messwerkzeug muss zur Frage passen.** Ein paar Fallen, die schon Zeit gekostet haben:
- `ps` mittelt `%CPU` über die Prozesslaufzeit — für Momentanwerte unbrauchbar, dafür `top` verwenden.
- Frisch gestartete Prozesse verfälschen jede Messung; erst einschwingen lassen.
- Ein Werkzeug, das nur eine Ebene sieht (etwa React DevTools bei handgeschriebenem DOM-Rendering), zeigt zum Problem darunter gar nichts an.

**Gemessene Zahlen gehören in den Code**, nicht nur in die Antwort im Chat. Ein Schwellwert ohne die Messung dahinter ist beim nächsten Mal eine willkürliche Zahl, an der niemand zu drehen wagt. Also: Wert, Einheit, und wogegen gemessen wurde.

**Internetzugriffe vorab planen und zuerst erledigen.** Wenn eine Aufgabe Recherche im Netz erfordert, wird vor der eigentlichen Arbeit überlegt, welche Informationen gebraucht werden — und die werden am Stück zu Beginn geholt. Danach läuft die Umsetzung ohne weitere Zugriffe.

Der Grund: Der Nutzer schaltet das Internet bewusst manuell frei. Ein Zugriff mitten in der Arbeit bedeutet, dass er erneut gefragt wird und die Arbeit so lange steht. Also lieber einmal am Anfang zu viel nachschlagen als dreimal zwischendurch. (Vor jedem Zugriff gilt weiterhin: vorher fragen.)

**Ausdrücklich Bescheid geben, sobald das Netz nicht mehr gebraucht wird**, damit der Nutzer es wieder abschalten kann. Dazu gehört auch, vorher zu prüfen, ob wirklich alles da ist — eine heruntergeladene Abhängigkeit etwa erst dann als erledigt melden, wenn sie im lokalen Cache liegt *und* sich einbinden lässt.

**Erst verstehen, dann reparieren.** Eine Symptombehandlung, die zufällig wirkt, ist schlechter als keine — sie verdeckt die Ursache. Wenn ein Fix wirkt, aber unklar ist warum, ist das ein offener Punkt und kein Ergebnis.

## Aufbau von Quellcodedateien

**Zeilenlänge höchstens 80 Zeichen.** Gilt für Code und Kommentare gleichermaßen. Wer die Grenze reißt, hat meist eine zu tief verschachtelte Form oder einen zu lang geratenen Namen — beides ist der eigentliche Befund, die Grenze nur der Anlass.

**Einrückung mit Leerzeichen, nie mit Tabulatoren.**

**Clean Code:** Der Quellcode ist möglichst selbstsprechend und kleinteilig organisiert. Konkret heißt das: sprechende Namen statt Kommentare, die einen kryptischen Namen erklären; kurze Funktionen mit einer klaren Aufgabe; Verschachtelung durch benannte Zwischenschritte auflösen. Eine Funktion, die man beim Lesen nicht am Stück im Kopf behalten kann, ist zu groß.

### Kommentare mit besonderer Bedeutung

Drei Präfixe, die im Quelltext gesucht werden können:

| Präfix | Bedeutung |
|---|---|
| `NOTE:` | Erläuterung, die der Code nicht direkt hergibt — Hintergrund, verworfene Alternative, Messung hinter einer Zahl. |
| `WATCHOUT:` | Wesentliche Restriktion oder Information zur Umsetzung oder für künftige Erweiterungen. Wer hier weiterarbeitet, muss das gelesen haben. |
| `FIXME:` | Bekannter Fehler oder bekanntes Problem. |

Die Präfixe sind für Kommentare mit *besonderer* Bedeutung gedacht, nicht für jeden beiläufigen Hinweis — sonst tragen sie nichts mehr. Der Unterschied zwischen `NOTE:` und `WATCHOUT:`: Ein `NOTE:` erklärt, ein `WATCHOUT:` warnt. Wenn das Übersehen des Kommentars zu einem Fehler führen kann, ist es ein `WATCHOUT:`.

## Code

**Kommentare erklären das Warum, nicht das Was.** Der Code sagt bereits, was passiert. Wertvoll ist, was man ihm nicht ansieht: die verworfene Alternative, die Messung hinter einer Zahl, die Fußangel der Plattform, der Grund für einen scheinbaren Umweg.

**Nicht offensichtliche Entscheidungen gehören dokumentiert** — an Ort und Stelle im Code, und wenn sie die Architektur betreffen zusätzlich in der Projekt-CLAUDE.md. Faustregel: Wenn jemand die Stelle in einem halben Jahr für einen Fehler halten und „aufräumen" könnte, fehlt der Kommentar.

**Konstanten mit Bedeutung bekommen einen Namen** und eine Erklärung, keine Zahl im Ausdruck. Besonders bei Schwellwerten, Zeitspannen und Grenzen.

**Eine Zahl, ein Ort.** Wenn derselbe Wert an zwei Stellen gebraucht wird (etwa in CSS und im Code), wird er an einer Stelle definiert und an der anderen gelesen. Doppelt gepflegte Werte laufen beim ersten Nachjustieren auseinander.

**Dem umgebenden Code folgen** — Namensgebung, Kommentardichte, Idiome. Ein Projekt soll wie aus einer Hand wirken, nicht wie eine Sammlung von Handschriften.

## Bibliotheken und Frameworks

**re-frame:** Die offizielle Dokumentation unter
<https://day8.github.io/re-frame/re-frame/> ist die Grundlage der
Implementierung. Nicht nach Gefühl oder nach dem, was gerade funktioniert --
die dort beschriebenen Muster gelten, insbesondere:

- Event-Handler sind **pure**. Jeder Seiteneffekt gehört in einen Effect
  (`reg-fx`), jeder Zugriff auf die Außenwelt in einen Coeffect (`reg-cofx`
  plus `inject-cofx`). Dateisystem, Zeit, Zufall, DOM: alles davon.
- In `app-db` liegen **Daten**, keine Funktionen und keine veränderlichen
  Objekte. Sonst sind Serialisierung, Zeitreise-Debugging und die
  Entwicklerwerkzeuge hinüber.
- Subscriptions werden geschichtet: Extraktoren lesen aus `app-db`,
  abgeleitete Sichten bauen über `:<-` darauf auf. Nicht in der View rechnen
  und nicht in einer Subscription erneut subscriben.
- Ein Handler schreibt **nur, was er wirklich ändern muss**, und niemals den
  gesamten app-state. Er hinterlässt einen in sich stimmigen Zustand --
  alles andere bleibt, wie es war. (Die eine Ausnahme ist das
  Initialisierungs-Event, das den Ausgangszustand herstellt.)
- Fehlt für einen Seiteneffekt der passende Effect, wird einer geschrieben.
  „Nur diesmal direkt im Handler" ist der Anfang vom Ende der Testbarkeit.
  Dasselbe gilt für veränderliche Eingangsdaten: dafür gibt es Coeffects.

Bei Zweifeln in der Dokumentation nachsehen, statt zu raten -- sie ist
ausführlich und begründet ihre Muster.

**Events, die Events auslösen, vermeiden.** Ein Handler, der seine Arbeit an
`{:dispatch [:anderes-event]}` weiterreicht, zerlegt einen fachlichen Vorgang
in mehrere Zustandsübergänge. Das erschwert das Lesen (was passiert
eigentlich?), die Tests (Reihenfolge und Zwischenzustände) und macht aus
einem Undo-Schritt mehrere. Stattdessen ein **dediziertes Event** je Vorgang.

Die gemeinsame Logik wird dabei als gewöhnliche Funktion ausgelagert und von
den beteiligten Handlern aufgerufen -- das ist der Regelfall und kostet
nichts. Lässt sie sich nicht sinnvoll herausziehen, ist im Zweifel etwas
Verdopplung im Handler besser als eine Event-Kette.

Davon unberührt: Ein *Effect*, der nach getaner Arbeit ein Event auslöst
(asynchrones Ergebnis, IPC-Antwort), ist genau richtig -- das ist kein
Handler, der weiterreicht, sondern die Rückmeldung aus der Außenwelt.

**Electron: Aktionen im Main-Prozess laufen über eine IPC-Brücke.** Der
Renderer greift nie direkt zu, sondern löst ein Event aus; ein Effect
schickt einen benannten Befehl samt Nutzdaten über IPC, der Main-Prozess
führt ihn aus und antwortet. Das Muster ist bewusst das einer HTTP-Anfrage:
Befehl, Nutzdaten, Antwort als Event. So bleibt die Prozessgrenze eine
einzige, überschaubare Stelle, statt sich über die Views zu verteilen, und
der Main-Prozess bietet eine benannte Befehlsliste statt verstreuter
`ipcMain`-Handler.

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

**WATCHOUT:** `core.async-helpers` und `clojure.core.async` nicht mischen.
Die Fehlerfortpflanzung lebt davon, dass Ausnahmen als Wert durch den Kanal
gehen; ein `core.async/<!` dazwischen nimmt den Wert stumm entgegen, und
der Fehler verschwindet.

**reagent:** Die Oberfläche ist idiomatisch nach
<https://github.com/reagent-project/reagent> umgesetzt. Ergänzend:

- **Dynamische Inhalte (Listen) mit `for` erzeugen und in `doall` wickeln.**
  `for` ist lazy; wird die Sequenz erst außerhalb des reaktiven Kontexts
  realisiert, bemerkt reagent die Abhängigkeit zum Atom nicht und die
  Komponente aktualisiert sich nicht mehr zuverlässig. `doall` erzwingt die
  Auswertung dort, wo sie hingehört.

- **Form 2 und Form 3 müssen die Parameter der View wiederholen.** Die
  äußere Funktion sieht die Argumente nur beim ersten Aufruf; was die
  innere Render-Funktion nicht selbst in ihrer Parameterliste stehen hat,
  bleibt für immer auf dem Erstwert stehen. Der Fehler ist heimtückisch,
  weil die Komponente beim ersten Rendern richtig aussieht.

- **Form 3 nur, wo es sein muss.** Sie ist mächtig und entsprechend
  kompliziert; in etwa einem Prozent der Fälle braucht man sie wirklich.
  Der typische berechtigte Anlass ist die Anbindung einer Fremdbibliothek
  oder einer Ressource mit eigenem Lebenszyklus (Canvas, WebGL, Karte,
  Editor). Sonst reicht Form 2.

- **Views laden keine Daten.** Die Daten sind da, bevor die View gerendert
  wird -- geladen wird über Events/Effects davor. Für die Wartezeit gehört
  eine schematische Platzhalter-View eingeblendet, nicht ein Ladevorgang in
  den Render-Pfad.

- **Jedes Element einer Liste braucht einen `:key`, und der muss eine echte
  Id sein.** Kein laufender Index: React ordnet damit beim Einfügen oder
  Sortieren den falschen Zustand zu. Gibt es keine natürliche Id, wird eine
  aus den Daten gebildet -- oder die Daten selbst dienen als Schlüssel.

- **Kleinteilige Views bauen.** Nur so kann reagent gezielt neu rendern,
  statt einen großen Baum anzufassen; nebenbei liest sich das besser. Für
  Subscriptions gilt dasselbe. Was tatsächlich neu gerendert wird, zeigen
  die React DevTools -- unerwartete Bereiche fallen dort sofort auf.

- **Eine View bekommt nur die Daten, die sie und ihre Sub-Views wirklich
  brauchen.** Alles Weitere löst Re-Renderings aus, sobald sich irgendetwas
  daran ändert. Dazu passen dedizierte, schmale Subscriptions.

- **Event-Handler in Props mit `reagent/partial` bilden, nicht mit `#(...)`
  oder `(fn [] ...)`.** Ein Closure ist bei jedem Rendern eine neue Instanz
  und damit ungleich zur vorherigen -- die Komponente rendert neu, obwohl
  sich nichts geändert hat. `reagent/partial` vergleicht sich über Funktion
  und Argumente und bleibt gleich.

## Clojure und ClojureScript

### Namespaces

**Jeder Namespace trägt eine kurze Beschreibung** seines Sinns und Zwecks als Docstring — was gehört hier hinein, was nicht.

**Ein Namespace, ein Thema.** Eigene Themen kommen in eigene Namespaces, gerne verschachtelt, wenn die Themen es zulassen oder erfordern. Ein Namespace, der zwei Dinge tut, wird geteilt.

#### Nach Thema gliedern, nicht nach Schicht

**Die Verzeichnisstruktur folgt den Themen der Anwendung**, nicht ihren technischen Schichten. Also `themes/`, `files/`, `buffer/` — und *nicht* `views/`, `events/`, `subs/` als Sammelbecken über alle Themen hinweg. Sammelbecken wachsen mit jeder Funktion, ohne je eine Grenze zu ziehen: Wer ein Thema versteht oder ändert, sucht sonst in drei Dateien nach drei Zeilen. Was zusammen geändert wird, gehört zusammen.

Der Aufbau je Thema:

| Ebene | Inhalt |
|---|---|
| `<thema>` | Der Hauptnamespace des Themas — das, was von außen benutzt wird. |
| `<thema>.<teil>` | Teilbereiche, die für sich stehen (`buffer.highlight`, `files.search`). |

**Ein Thema beginnt als *ein* Namespace** — mit allem darin, auch wenn das mehrere Schichten sind (Ansicht, Events, Subscriptions). Erst wenn er zu groß wird, um ihn am Stück zu lesen, zerfällt er in Teile, und dann entlang der Schichten (`workspace` als Ansicht, `workspace.events`, `workspace.subs`). Vorher zu teilen kostet nur Sprünge zwischen Dateien.

**Was mehrere Themen brauchen, steht eine Ebene höher** als eigener Namespace — nicht in dem Thema, das es zufällig zuerst brauchte. Ein Thema, das aus einem anderen liest, ist ein Hinweis auf so einen gemeinsamen Nenner.

**Bibliotheken** haben einen Hauptnamespace, der so heißt wie die Bibliothek; alles Weitere liegt darunter. Also `fileutils` als Hauptnamespace und `fileutils.names` als Teilbereich.

**Anwendungen** haben immer `main` und `core`:

| Namespace | Aufgabe |
|---|---|
| `main` | Einstiegspunkt — die `main`-Funktion bzw. das Mounten. Startet das System. |
| `core` | Beschreibt das System: fasst die Hauptkomponenten zusammen. |
| `components` | Darunter je Komponente ein eigener Namespace. |

Die Hauptkomponenten werden als [stuartsierra/component](https://github.com/stuartsierra/component) abgebildet. `core` beschreibt das System, `main` startet es.

**Die Komponenten liegen unter `components`** — eine je Namespace, benannt wie die Komponente: `<anwendung>.components.window`, `<anwendung>.components.ui`. Darin steht **das ganze Thema**: die Arbeit, der `defrecord` mit `start`/`stop` und eine Konstruktorfunktion `component`. Die Komponente *ist* das Thema, nicht bloß dessen Lebenszyklus.

Den Lebenszyklus vom Thema zu trennen und daneben einen zweiten Namespace zu legen, ist erst dann richtig, wenn es dafür einen echten Aufrufer gibt — jemanden, der das Thema ohne laufendes System braucht. Ohne den entsteht nur eine Hülle aus zwei Zeilen Delegation, die beim Lesen zwei Dateien statt einer kostet.

Was *mehrere* Komponenten brauchen, wird ohnehin zu einem eigenen Namespace daneben — nicht wegen des Lebenszyklus, sondern weil es geteilt wird.

`core` liest sich damit als reine Aufzählung:

```clojure
(component/system-map
 :menu
 (menu/component)

 :window
 (component/using (window/component) [:menu]))
```

Hat ein Prozess **mehrere Systeme** (etwa Electron mit Haupt- und Renderer-Prozess), bekommt jedes seinen eigenen Zweig mit `main`, `core` und `components` darunter.

### Requires und Imports

`:require` und `:import` werden umgebrochen (ein Eintrag je Zeile) und **nach Stabilität gruppiert**: erst Clojure selbst, dann Fremdbibliotheken, dann die eigenen Quellen. Innerhalb einer Gruppe alphabetisch. Zwischen den Gruppen eine Leerzeile.

```clojure
(ns de.jtkdvlp.beispiel.core
  "Kurz, wofür dieser Namespace da ist."
  (:require
   [clojure.string :as string]

   [re-frame.core :as re-frame]

   [de.jtkdvlp.beispiel.buffer :as buffer]
   [de.jtkdvlp.beispiel.files :as files]))
```

**Aliase sind sprechend.** Abkürzungen werden vermieden; wo eine steht, muss sie eindeutig und unmissverständlich sein. `:refer` gezielt einsetzen, nicht pauschal.

### Formatierung

**Funktionsaufrufe:** Parameter durch Leerzeichen trennen — oder durch Umbrüche, wenn das der Übersichtlichkeit dient.

**`let`-Blöcke:** Ein einzelnes Binding steht einzeilig. Bei mehreren Bindings werden Symbol und Wert umgebrochen, zwischen den Bindings steht eine Leerzeile, und zwischen Binding-Vector und Body ebenfalls.

```clojure
(let [x (berechne-etwas a b)]
  (verwende x))

(let [breite
      (miss-breite element)

      hoehe
      (miss-hoehe element)]

  (zeichne breite hoehe))
```

**Threading-Makros bevorzugen**, besonders bei Datenpipelines — statt ineinander geschachtelter Aufrufe. Funktionen werden dabei immer geklammert notiert, auch ohne weitere Parameter:

```clojure
(-> daten
    (filtere-gueltige)
    (sortiere-nach :name)
    (nimm 10))
```

### Separation of Concerns

Funktionen, Namespaces und Schichten haben je einen Fokus und behandeln auch nur diesen. Eine Funktion sollte nicht gleichzeitig komplex rechnen, Seiteneffekte ausüben und einen Wert zurückliefern — das sind drei Aufgaben und damit drei Funktionen. Eine Trennung ist nicht in jedem Fall möglich; Ausnahmen bestätigen die Regel.

### Namenskonventionen

Namen stehen in Kleinbuchstaben, Wörter werden durch Bindestriche getrennt. Funktionsnamen enthalten meist ein Verb.

| Zeichen | Position | Bedeutung |
|---|---|---|
| `?` | am Ende | Prädikat — liefert **immer** einen booleschen Wert |
| `!` | am Ende | Seiteneffekt |
| `?` | am Anfang | core.async-Channel |
| `!` | am Anfang | veränderliches Symbol, etwa ein Atom |
| `**` | umschließend | dynamische Var |

## Tests

**Ein Fehler, der einmal auftrat, bekommt einen Test.** Der Test hält fest, *warum* es ihn gibt: was schiefging und woran man es merkte. Das ist wichtiger als der Testname.

**Testen, was kaputtgehen kann, nicht was leicht zu testen ist.** Bei Performance-Regressionen heißt das: gegen eine großzügige Grenze prüfen, die einen echten Rückfall fängt (Größenordnung), nicht gegen einen knappen Wert, der bei jeder Schwankung ausschlägt.

**Tests dürfen keine festen Werte duplizieren**, die anderswo als Konstante stehen — sonst prüfen sie nach der nächsten Änderung etwas anderes als gedacht. Positionen und Grenzen aus der Konstante ableiten.

## Zusammenarbeit

**Umfang ist der Auftrag.** Nicht stillschweigend erweitern und nicht stillschweigend kürzen. Fällt unterwegs etwas auf, das über den Auftrag hinausgeht: benennen, nicht einfach miterledigen.

**Ergebnisse ehrlich berichten.** Was nicht funktioniert, wird gesagt — mit der Ausgabe dazu. Was übersprungen wurde, wird gesagt. Eine frühere Falschaussage wird richtiggestellt, sobald sie auffällt, ohne Umschweife und ohne Selbstgeißelung.

**Vor schwer umkehrbaren Schritten fragen.** Löschen, Überschreiben, alles nach außen Wirkende.

**Über Pull Requests arbeiten, nicht direkt committen.** Änderungen gehen auf einem Branch und über einen PR in den Hauptzweig, nicht per Direkt-Commit dorthin. Das hält den Hauptzweig jederzeit in einem Zustand, den andere ungeprüft übernehmen können.

**Erst ein offizieller Feature-Branch, dann die Arbeit.** Vor der ersten Änderung an einem Feature wird dafür ein eigener Branch angelegt; alle Commits dazu gehen auf diesen Branch. Der PR entsteht erst, wenn das Feature fertig ist — nicht als leerer PR zu Beginn.
