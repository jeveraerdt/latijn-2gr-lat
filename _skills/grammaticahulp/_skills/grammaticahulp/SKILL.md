# Skill: Grammaticahulp aanpassen in tekst-viewer-ludus7b.html

## Wat & waarom

De grammaticahulp in `tekst-viewer-ludus7b.html` (GitHub: `jeveraerdt/latijn-2gr-lat`) toont per Latijns woord een kaartje met drie rijen:
- **Det.** => `data-d` (determinatie: naamval, getal, geslacht, werkwoordsvorm)
- **Funct.** => `data-s` (syntactische functie: OND, LV, BWB, PV)
- **NL** => `data-n` (Nederlandse vertaling)

Er is **geen parser**. De tekst in `data-d` en `data-s` wordt 1:1 getoond. Aanpassingen = attribuutwaarden wijzigen in de HTML.

---

## Bestandsstructuur

```
tekst-viewer-ludus7b.html
+-- .lat-word elementen (één per woord/woordgroep)
    +-- data-w   = weergegeven Latijnse tekst
    +-- data-l   = lemma(ta)
    +-- data-d   = determinatie
    +-- data-s   = syntactische functie
    +-- data-n   = vertaling NL
```

Elk .wg-element groepeert semantische chunks; .lat-word zit erin.

---

## Vaste conventies

### data-d (determinatie)

Nomina/voorzetselgroepen:
- Formaat: naamval getal geslacht => acc. enk. v. / abl. mv. m.
- Geslacht altijd vermelden: m. / v. / o.
- Voorzetselgroepen: voorzetselgroep (acc. enk. v.) -- geslacht van het nomen binnen de groep
- Combinaties: acc. enk. + voorzetselgroep (acc. enk. v.)

Werkwoorden:
- Formaat: [A./P./dep.] [ind./conj./inf./imp.] [tijdsvorm] [persoon] [getal]
- Voorbeelden: A. ind. praes. 3 enk. / P. dep. ind. perf. 2 enk.

Participia: PPP nom. enk. m. / PPA acc. mv. v.

### data-s (syntactische functie)

Leerlingen (2de jaar) kennen ALLEEN deze termen:

| Functie | Label |
|---|---|
| Onderwerp | OND + context (bv. OND van stetit) |
| Lijdend voorwerp | LV + context |
| Meewerkend voorwerp | meew. voorw. + context |
| Persoonsvorm | PV (+ eventueel bijzintype) |
| BWB van middel | BWB van middel |
| BWB van tijd | BWB van tijd |
| BWB van wijze | BWB van wijze |
| BWB van plaats | BWB van plaats |
| BWB van oorzaak | BWB van oorzaak |
| BWB van verwijdering/scheiding | BWB van verwijdering/scheiding |
| Roepvorm | roepvorm |
| ACI | ACI + korte aanduiding |

Regels:
- GEEN technische subtermen achter BWB (fout: BWB van middel bij advocari)
- GEEN werkwoordsverwijzingen achter BWB (fout: BWB van tijd bij iussit)
- Wel context bij OND/LV als het helpt (bv. OND van audivit)

---

## Werkmethode in de browser

### Stap 1 -- Alle data ophalen (live pagina)
```
https://jeveraerdt.github.io/latijn-2gr-lat/tekst-viewer-ludus7b.html
```
```javascript
const words = document.querySelectorAll('.lat-word');
window._allWords = Array.from(words).map(w => ({
  w: w.dataset.w, l: w.dataset.l,
  d: w.dataset.d, s: w.dataset.s, n: w.dataset.n
}));
window._allWords.length + ' words';
```

### Stap 2 -- Gefilterd bekijken
```javascript
window._allWords
  .filter(w => w.d.includes('voorzetsel') || w.d.includes('abl.'))
  .map(w => w.w + ' | d: ' + w.d + ' | s: ' + w.s);
```

### Stap 3 -- GitHub editor + helper laden
```
https://github.com/jeveraerdt/latijn-2gr-lat/edit/main/tekst-viewer-ludus7b.html
```
```javascript
function rep(view, oldStr, newStr) {
  const idx = view.state.doc.toString().indexOf(oldStr);
  if (idx === -1) return 'NOT FOUND: ' + oldStr.slice(0,40);
  view.dispatch({ changes: { from: idx, to: idx + oldStr.length, insert: newStr } });
  return 'OK';
}
const view = document.querySelector('.cm-content').cmTile.view;
window._view = view; window._rep = rep;
```

### Stap 4 -- Vervanging uitvoeren
```javascript
// Zoek altijd op data-l + data-d samen (nooit data-d alleen -- meerdere hits)
window._rep(window._view,
  'data-l="dies, -ei" data-d="voorzetselgroep (acc. enk.)"',
  'data-l="dies, -ei" data-d="voorzetselgroep (acc. enk. v.)"'
);
```

### Stap 5 -- Valideren
```javascript
const doc = window._view.state.doc.toString();
[
  'voorzetselgroep (acc. enk.)"',
  'voorzetselgroep (abl. enk.)"',
  'data-s="abl. van',
  'BWB van tijd bij',
  'BWB van middel bij',
].map(s => s + ': ' + (doc.includes(s) ? 'OPEN' : 'clean'));
```

### Stap 6 -- Committen
```javascript
// Open dialoog
Array.from(document.querySelectorAll('button'))
  .find(b => b.textContent.includes('Commit changes...')).click();
// Commit message instellen
const input = document.querySelector('input[placeholder*="tekst-viewer"]');
const setter = Object.getOwnPropertyDescriptor(HTMLInputElement.prototype,'value').set;
setter.call(input, 'Jouw commit message');
input.dispatchEvent(new Event('input', { bubbles: true }));
// Bevestigen (button met tekst "Commit changes" die zichtbaar is)
Array.from(document.querySelectorAll('button'))
  .filter(b => b.textContent.trim() === 'Commit changes' && b.offsetParent)[0].click();
```

---

## Valkuilen

| Probleem | Oplossing |
|---|---|
| NOT FOUND bij rep() | Controleer exacte attribuutvolgorde -- gebruik data-l + data-d samen |
| Filter blokkeert output | Gebruik .length of index-queries ipv volledige strings |
| Commit-dialoog verdwenen | Heropen met find(b => b.textContent.includes('Commit changes...')).click() |
