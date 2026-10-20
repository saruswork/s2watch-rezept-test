# S2Watch Rezept-Testseite (GitHub Pages)

Oeffentliche Mini-Seite fuer den Ende-zu-Ende-Lauf von „Vom Handy", Stufe 2 (Szenario 12 in
`docs/SELF_HEALING_SZENARIEN.md` des App-Repos). Sie ist absichtlich so gebaut, dass

1. die eingebaute Kette der App (`RemoteWebResolver`) den Stream **nicht** findet — die
   Playlist-Adresse steht nur **protokollrelativ** (`//test-streams.mux.dev/...`) im
   `data-url`-Attribut eines eigenen Player-Divs (`div#s2-stage`). Die App sucht absolute
   http(s)-Adressen im Seitentext, JW-/`<video>`-/og-/JSON-LD-Muster und iframes; dieses Div
   kennt sie nicht;
2. auch der WebView-Abfang der App nichts findet — die Seite fragt den Stream am Fernseher erst
   **nach einem Tipp** auf den Player an;
3. der Pruef-Browser im Workflow (Playwright, `navigator.webdriver = true`) den Stream dagegen
   sofort anfragt und so den Mitschnitt liefert, gegen den das Rezept geprueft wird.

Das erwartete Rezept (so steht es auch in der gemeinsamen Falltabelle
`watchdog/test/fixtures/recipe_cases.json`, Fall „GitHub-Pages-Testseite"):

```json
{ "steps": [ { "op": "select", "selector": "div#s2-stage[data-url]", "attr": "data-url" } ],
  "result": { "kind": "hls", "referer": "none" } }
```

Es bleibt nach dem ersten Lauf als dauerhafter Kanarienvogel in der Regeldatei stehen (Gutachten
F1/F8) — der Lauf ist damit jederzeit wiederholbar (Host-Bremse: hoechstens einmal je 24 h).

## Video und Lizenzen

- Video: **„Big Buck Bunny"** © 2008 Blender Foundation (peach.blender.org), lizenziert unter
  **Creative Commons Namensnennung 3.0 (CC BY 3.0)**,
  https://creativecommons.org/licenses/by/3.0/ — Namensnennung steht auf der Seite.
- HLS-Teststream: `https://test-streams.mux.dev/x36xhzz/x36xhzz.m3u8`, von Mux oeffentlich als
  Teststream bereitgestellt (dieselbe Adresse nutzt bereits `RemoteWebLinkPolicyTest` der App).
- Abspielen im Browser: **hls.js** 1.5.17 von jsDelivr (Apache-2.0). Vor dem Anlegen pruefen,
  dass die Version auf jsDelivr existiert; sonst die naechste vorhandene 1.5.x eintragen.

## Anlegen (Orchestrator, nach Freigabe)

1. Neues **oeffentliches** Repo, z. B. `saruswork/s2watch-rezept-test` (GitHub Pages braucht im
   Free-Plan ein oeffentliches Repo).
2. `index.html` aus diesem Ordner in die Wurzel legen (diese README darf mit, sie verraet nur das
   Muster, nicht mehr als die Seite selbst).
3. Settings -> Pages -> „Deploy from a branch", `main` / root. Adresse merken, z. B.
   `https://saruswork.github.io/s2watch-rezept-test/`.
4. Im Browser pruefen: Seite laedt, nach Tipp spielt das Video. `view-source:` zeigt die
   m3u8-Adresse nur protokollrelativ im `data-url`.
5. Der Rezept-Host ist dann `saruswork.github.io` — **Achtung:** ein Rezept gilt fuer den ganzen
   Host inkl. aller Unterseiten (Rezepte binden nur an den Host, `pathPattern` ist verschoben).
   Andere Pages-Seiten desselben Kontos wuerden das Rezept ebenfalls versuchen (harmlos: ohne
   `div#s2-stage` kein Treffer, dann WebView wie bisher). Wer das vermeiden will, nimmt ein
   eigenes Konto/eine eigene Domain fuer die Testseite.
