# Parcel und Elm - Projekt Setup wie von Zauberhand

Mit der kürzlich erschienen [Version 1.10](https://medium.com/@devongovett/parcel-v1-10-0-babel-7-flow-elm-and-more-c20736553573) unterstützt Parcel jetzt auch Elm Assets ohne zusätzliches Plugin - damit wird das Projekt Setup einer Elm Webapp massiv vereinfacht. Wie ein solches Projekt Setup aussehen könnte wird in diesem Blog beschrieben.

## Parcel - Geschenke verpacken oder was?

Die Aufgabe eines Bundlers ist es, die Assets einer Webapplikation (Html, Css, Javascript, Images etc.) und ihre Dependencies aufzubereiten, so dass die Applikation am Schluss optimal an den Browser ausgeliefert wird. [Parcel](https://parceljs.org/) ist eine alternative zu Webpack, Browserify oder rollup. Im Gegensatz zu diesen kommt Parcel ohne jegliche Konfiguration aus.
Wollte man bisher eine Elm-Applikation mit Parcel builden, war man auf externe Plugins angewiesen - das ist zwar nicht schlecht, aber es ist natürlich umso schöner, wenn ein Tool die gewünschte Funktionalitäten gleich von Haus aus mitbringt.


## Projekt Setup

(Lieber Leser: mach mit!)

Das Setup einer Elm-Applikation mit Parcel als Bundler gestaltet sich tatsächlich extrem einfach. Voraussetzung ist lediglich eine relativ aktuelle node/npm Installation, z.B.

```bash
$ node --version
v9.2.0
$ npm --version
5.5.1
```

Anschliessend kann das Projekt erstellt werden:

```bash
$ mkdir elm-parcel-project && cd $_ && npm init -y
$ npm install --save-dev parcel-bundler
```

Als Einstiegspunkt erstellen wir die Datei ```index.html```:

```
$ touch index.html
```

Das Html unserer Applikation ist minimal: Ein div-Element wird unsere Applikation enthalten und laden unsere Applikation (main.js).


```html
<html>
  <body>
    <div id="app"></div>
    <script src="./src/main.js"></script>
  </body>
</html>
```

Die Applikation ist vorerst in reinem Javascript gehalten und ersetzt lediglich den Inhalt des Html Elements mit einem Text:

```bash
$ mkdir src/ && touch src/main.js
```

```javascript
document.getElementById('app').innerHTML = "Hello Parcel";
```

Die Applikation kann nun mit ```parcel index.html``` gestartet werden - am einfachsten geht dies mit einem spezifischen npm-Script in ```package.json```:

```json
"scripts": {
    "start": "parcel index.html --open"
  },
```

Jetzt kann die Applikation gestartet werden:

```bash
$ npm start

> elm-parcel-project@1.0.0 start /home/.../elm-parcel/elm-parcel-project
> parcel index.html

Server running at http://localhost:1234 
✨  Built in 682ms.
```

Betrachtet man den Quelltext im Browser erkennt man, dass Parcel unsere Datei main.js mit einem Hash versehen und in index.html die entsprechende Anpassung vorgenommen hat. Anpassungen in unserer Applikation werden zudem wie erwartet automatisch im Browser nachgeladen.


## Integrating Elm

Anstatt einfach mit Javascript in den Dom-Node ```<div id="app">``` zu schreiben wollen wir dort jetzt eine Elm-Applikation mounten.

Dazu schreiben wir die wohl bekannteste Applikation aller Zeiten, jetzt einfach in Elm - Hello World und zwar in src/Main.elm:

```bash
$ touch src/Main.elm
```


```elm
module Main exposing (main)

import Html

main = 
  Html.text "Hello World from Elm!"
```
(Main.elm)

In main.js können wir jetzt die Elm-Applikation importieren und auf dem div initialisieren:

```javascript
import { Elm } from './src/Main.elm'

var element = document.getElementById('app');
Elm.Main.init({node: element});
```

Sobald diese Datei gespeichert wir, merkt Parcel, dass wir jetzt ein Elm-Asset laden wollen und es werden alle notwendigen Dependecies installiert und die Konfigurationdatei elm.json initialisert!


Damit haben wir eine simples aber robustes Projektsetup für die Entwicklung von Elm-Applikationen erstellt.

## Production Build und Optimisierungen

Eines der Heraustellungsmerkmale des kürzlich erfolgten Releases von Elm 0.19 waren die möglichen [Optimierungen des Compilers](http://elm-lang.org/blog/small-assets-without-the-headache) welche auf die funktionale Natur der Sprache zurückzuführen sind. Parcel beherzigt die [entsprechenden Empfehlungen aus dem Elm Guide](https://guide.elm-lang.org/optimization/asset_size.html), wie aus dem entsprechenden [Source Code](https://github.com/parcel-bundler/parcel/blob/master/src/assets/ElmAsset.js#L85) unschwer zu erkennen ist.

Ein einfaches Skript in package.json zum builden unserer Applikation genügt damit um ein optimales Resultat zu erhalten:

```json
 "scripts": {
    "build": "parcel build index.html",
    ...
  },
```

```bash
> elm-parcel-project@1.0.0 build /home/mburri/Documents/blogs/elm-parcel/elm-parcel-project
> parcel build index.html

✨  Built in 2.31s.

dist/main.16091949.js     7.82 KB    1.76s
dist/main.0a44fb6b.map      405 B      2ms
dist/index.html              92 B    479ms
```


## Fazit

Die ersten Gehversuche mit Parcel sind vielversprechend: innerhalb weniger Minuten ist ein simples aber robustes Projekt erstellt, welches sowohl für die Entwicklungsphase als auch für die Produktion bereits ansprechende bis optimale Resultate liefert - und dies ohne jegliche Konfiguration (ein Weg, den z.B. Webpack mit den neuesten Releases ebenfalls eingeschlagen hat).

Die Integration mit Elm ist sehr gut gelöst - der Entwickler muss keine zusätzlichen Dependencies händisch installieren oder zusätzliche Schritte im Projekt Setup durchführen - sobald ein Elm Asset eingebunden wird übernimmt Parcel die gesamte Arbeit.

Das hier erstellte Template ist auf Github veröffentlicht und kann für eigene Projekte verwendet werden: https://github.com/mburri/elm-starter-parcel