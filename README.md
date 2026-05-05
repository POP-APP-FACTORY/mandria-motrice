# Mandria Motrice

> Convertitore di potenza zootecnica.
> *Quanti animali ha la tua automobile?*

Una piccola web-app single-file che converte la potenza di un veicolo
(in CV, HP o kW) nel numero equivalente di animali da fattoria, usando
la stessa logica con cui storicamente si è definito il cavallo vapore.

🌐 **Live**: <https://mandriamotrice.pop-appfactory.com>

---

## L'idea

Le auto si misurano in **cavalli**, ma:

1. Quei "cavalli" non sono cavalli veri — sono **cavalli vapore (CV)**,
   un'unità definita a fine '800 a partire da una stima ottimistica
   (e un po' arcaica) della potenza che un cavallo da tiro era ritenuto
   in grado di sostenere.
2. Un cavallo vapore (~735 W) e un horsepower meccanico anglosassone
   (HP, ~745 W) **non sono nemmeno la stessa cosa** — differiscono
   dell'1.4% circa, perché sono stati standardizzati indipendentemente
   in due tradizioni metrologiche diverse.
3. Un cavallo **reale** in regime sostenuto eroga circa **0.1 CV**.
   Il "cavallo vapore" è quindi più un'unità simbolica che fisica.

Da qui la domanda: **se possiamo misurare un'auto in cavalli che non
sono cavalli, perché non misurarla in pecore?** O in galline. O in
elefanti.

Questa app fa esattamente questo.

---

## Il metodo di conversione

Il cuore del calcolo è coerente con la logica del CV originale: per
ogni animale si definisce una **potenza nominale di riferimento in
Watt**, stimata come "lavoro di trazione sostenuto, leggermente
generoso" — esattamente lo stesso principio con cui Watt e i suoi
contemporanei tararono il cavallo vapore.

### Costanti reali

| Unità                    | Valore in Watt |
|--------------------------|---------------:|
| 1 CV (cavallo vapore)    |     735.49875  |
| 1 HP (horsepower mecc.)  |     745.7      |
| 1 kW                     |    1000.0      |

### Animali

| Animale  | Watt nominali | Equivalente in CV |
|----------|--------------:|-------------------:|
| Gallina  |          7.355|             0.01   |
| Pecora   |         73.55 |             0.10   |
| Maiale   |        220.6  |             0.30   |
| Asino    |        367.7  |             0.50   |
| Mucca    |        588.4  |             0.80   |
| Bue      |        882.6  |             1.20   |
| Cammello |       1029.7  |             1.40   |
| Elefante |       2206.5  |             3.00   |

I valori sono **proporzionati tra loro** in modo coerente, non sono
omologazioni ministeriali. Sono pensati per il confronto e per il
divertimento.

### Esempio

Un'auto da **200 CV** equivale a:

- ~67 elefanti
- ~143 cammelli
- 167 buoi
- 250 mucche
- 400 asini
- ~666 maiali
- 2.000 pecore
- 20.000 galline

---

## URL parametrizzato

L'app accetta un singolo parametro nella query string, utile per
condividere link precompilati:

- `?cv=200` — 200 cavalli vapore
- `?hp=150` — 150 horsepower
- `?kw=110` — 110 kilowatt

L'unità della query diventa anche l'unità attiva del toggle.
Il primo parametro valido vince; gli altri vengono ignorati.

---

## Architettura

Volutamente minimale. Il progetto è **un singolo file HTML** che
contiene HTML, CSS e JS inline. Nessuna build, nessuna dipendenza
runtime, nessun framework.

```
mandria-motrice/
├── index.html      # tutta l'app
├── firebase.json   # config hosting + headers
├── .firebaserc     # progetto Firebase
└── README.md       # questo file
```

### Scelte tecniche

- **Single-file HTML.** Il dominio del problema è banale (input numerico,
  qualche moltiplicazione, render di una griglia): qualsiasi framework
  sarebbe un'overkill ridicola. Niente React, niente bundler, niente
  `node_modules`.
- **Niente storage, niente backend.** Tutto lato client. Il calcolo è
  istantaneo e offline-friendly.
- **Niente analytics, niente tracker.** L'app non ha bisogno di sapere
  niente di chi la usa.
- **Font da Google Fonts.** *DM Serif Display* (titoli),
  *Fraunces* (corpo, con vere italics e numeri old-style) e
  *JetBrains Mono* (etichette, metadati).
- **Estetica deliberata.** Tema "catalogo zootecnico vintage / manuale
  tecnico anni '50": carta avorio, inchiostro caldo, bordi netti,
  accenti rosso-amaranto, una texture-noise SVG inline come
  background. Nessun gradiente viola, nessun "AI slop look".
- **Responsive senza media query elaborate.** Griglia
  `auto-fill, minmax(220px, 1fr)`; il toggle delle unità ruota da
  verticale a orizzontale sotto i 520px.

---

## Sviluppo locale

Aprire `index.html` direttamente nel browser. Non c'è nessuno step
di build, nessun server di sviluppo necessario.

```bash
open index.html         # macOS
xdg-open index.html     # Linux
```

In alternativa, per testare con un server statico:

```bash
python3 -m http.server 8000
# http://localhost:8000
```

---

## Deploy

Hosting su Firebase, progetto `mandria-motrice-pop-app-factory`,
sottodominio `mandriamotrice.pop-appfactory.com`.

```bash
firebase deploy --only hosting
```

Il `firebase.json` definisce headers di sicurezza
(`Strict-Transport-Security`, `X-Content-Type-Options`,
`Referrer-Policy`) e cache aggressiva sugli asset statici.

---

## Possibili evoluzioni

- **Modalità "potenza reale"**: switch che usa la potenza
  *fisicamente sostenibile* invece di quella nominale (un cavallo
  reale = 0.1 CV, e così via). I numeri diventano molto più grandi
  e molto più assurdi.
- **Confronto inverso**: "questa mandria di 47 pecore equivale a una
  Panda 1.2".
- **Visualizzazione**: piccoli pittogrammi animati o un campo che si
  riempie di iconcine.
- **Animali apocrifi**: T-Rex, mammut, drago.

---

## Crediti

- Costanti CV/HP: definizioni standard (CIPM 1889 per il cavallo
  vapore, normative anglosassoni per l'HP meccanico).
- Valori zootecnici: stime indicative, calibrate per coerenza
  interna piuttosto che per precisione veterinaria.
- Tipografia: Google Fonts (DM Serif Display, Fraunces, JetBrains Mono).

---

## Licenza

MIT — fai pure quello che vuoi.

---

Progetto di [Pop-App Factory](https://pop-appfactory.com).
*Pubblicazione apocrifa. Misurato in unità non SI dal MMXXVI.*
