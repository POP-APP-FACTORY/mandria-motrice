# Mandria Motrice

> Convertitore di potenza zootecnica.
> *Quanti animali ha la tua automobile?*

Una piccola web-app single-file che converte la potenza di un veicolo
(in CV, HP o kW) nel numero equivalente di animali da fattoria, usando
la stessa logica con cui storicamente si è definito il cavallo vapore.

🌐 **Live**: <https://mandria-motrice.pop-appfactory.com>
🌐 **Mirror Firebase**: <https://mandria-motrice-popapp.web.app>

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

## Come si usa

1. Inserisci la potenza del veicolo (numero, niente default — ti
   stuzzica a scriverlo tu).
2. Scegli l'unità di misura (CV, HP o kW).
3. Scegli un animale dall'archivio.
4. La conversione parte (con un piccolo loader animato) e ti
   restituisce il risultato come **numero intero**, perché in mandria
   non si conta a virgola.

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

### Archivio animali

Definito in `index.html` come array `ANIMALS`, facilmente estendibile.

| Animale  | Watt nominali | Equivalente in CV | Note          |
|----------|--------------:|-------------------:|---------------|
| Gallina  |          7.355|             0.01   |               |
| Pecora   |         73.55 |             0.10   |               |
| Capra    |        110.32 |             0.15   |               |
| Maiale   |        220.6  |             0.30   |               |
| Asino    |        367.7  |             0.50   |               |
| Lama     |        515.0  |             0.70   |               |
| Mucca    |        588.4  |             0.80   |               |
| Mulo     |        735.5  |             1.00   | = 1 CV esatto |
| Bue      |        882.6  |             1.20   |               |
| Cammello |       1029.7  |             1.40   |               |
| Bisonte  |       1471.0  |             2.00   |               |
| Elefante |       2206.5  |             3.00   |               |
| Mammut   |       3677.5  |             5.00   | apocrifo      |
| T-Rex    |       5882.0  |             8.00   | apocrifo      |

I valori sono **proporzionati tra loro** in modo coerente, non sono
omologazioni ministeriali. Sono pensati per il confronto e per il
divertimento.

### Aggiungere un animale

Apri `index.html`, trova l'array `ANIMALS` e aggiungi una voce:

```js
{ id:"renna", watt: 441.3, emoji:"🦌",
  i18n:{
    it:["renna","renne"],   en:["reindeer","reindeer"],
    es:["reno","renos"],    fr:["renne","rennes"],
    de:["Rentier","Rentiere"]
  }
}
```

Tutto il resto (picker, render, plurali, traduzioni) si adatta da
solo.

---

## Lingue

L'app è disponibile in **🇮🇹 italiano · 🇬🇧 inglese · 🇪🇸 spagnolo · 🇫🇷 francese · 🇩🇪 tedesco**.

- All'avvio rileva la lingua del browser (`navigator.language`).
- Override manuale dal selettore in alto, persistente in `localStorage`.
- Override via URL: `?lang=en`.

Per aggiungere una lingua: estendi `LANGS`, aggiungi un blocco a
`I18N` e aggiungi le traduzioni `i18n[<codice>]` a ogni animale in
`ANIMALS`.

---

## URL parametrizzato

L'app accetta parametri nella query string per condividere link
precompilati:

- `?cv=200` — 200 cavalli vapore
- `?hp=150` — 150 horsepower
- `?kw=110` — 110 kilowatt
- `?animal=pecora` — preseleziona l'animale (per `id`)
- `?lang=en` — forza la lingua

Esempio combinato: `?cv=147&animal=elefante&lang=en` apre la pagina
in inglese, già impostata su 147 CV e con l'elefante selezionato.

---

## Architettura

Volutamente minimale. Il progetto è **un singolo file HTML** che
contiene HTML, CSS e JS inline. Nessuna build, nessuna dipendenza
runtime, nessun framework.

```
mandria-motrice/
├── index.html      # tutta l'app (HTML + CSS + JS + i18n + animali)
├── firebase.json   # config hosting + headers
├── .firebaserc     # progetto Firebase
└── README.md       # questo file
```

### Scelte tecniche

- **Single-file HTML.** Il dominio del problema è banale (input numerico,
  qualche moltiplicazione, render di una griglia): qualsiasi framework
  sarebbe un'overkill ridicola. Niente React, niente bundler, niente
  `node_modules`.
- **i18n inline.** Lingue e traduzioni vivono nello stesso file.
  L'archivio animali porta con sé i propri plurali per ogni lingua —
  così aggiungere un animale o una lingua resta locale a un punto.
- **Niente backend, niente cookie.** Tutto lato client. L'unica cosa
  persistita lato browser è la preferenza di lingua, in `localStorage`.
- **Analytics aggregati anonimi.** Google Analytics 4 via `gtag.js` in
  modalità *Consent Mode v2 default-denied*: nessun cookie viene
  scritto, nessun identificatore utente viene generato, nessun banner
  GDPR è richiesto. Vengono inviati solo *cookieless pings* aggregati
  (paese, device, page view) utili a sapere se qualcuno usa l'app —
  niente tracking individuale. Eventi custom: `select_language`,
  `select_animal`, `change_unit`, `share`.
- **Estetica deliberata.** Tema "catalogo zootecnico vintage / manuale
  tecnico anni '50": carta avorio, inchiostro caldo, bordi netti,
  accenti rosso-amaranto, una texture-noise SVG inline come
  background. Nessun gradiente viola, nessun "AI slop look".
- **Font da Google Fonts.** *DM Serif Display* (titoli),
  *Fraunces* (corpo, italics e numeri old-style),
  *JetBrains Mono* (etichette, metadati).
- **Versione visibile in footer.** La costante `APP_VERSION` nel JS è
  l'unica fonte di verità; viene scritta nel DOM al boot.

---

## Sviluppo locale

Aprire `index.html` direttamente nel browser. Non c'è nessuno step
di build, nessun server di sviluppo necessario.

```bash
open index.html         # macOS
xdg-open index.html     # Linux
```

In alternativa, per testare con un server statico (utile per evitare
quirk del protocollo `file://` su qualche browser):

```bash
python3 -m http.server 8000
# http://localhost:8000
```

---

## Deploy

Hosting su Firebase, progetto `mandria-motrice-popapp`, custom domain
`mandria-motrice.pop-appfactory.com`.

```bash
firebase deploy --only hosting
```

Il `firebase.json` definisce headers di sicurezza
(`Strict-Transport-Security`, `X-Content-Type-Options`,
`Referrer-Policy`, `X-Frame-Options`, `Permissions-Policy`) e cache
aggressiva su asset statici.

---

## Possibili evoluzioni

- **Modalità "potenza reale"**: switch che usa la potenza
  *fisicamente sostenibile* invece di quella nominale (un cavallo
  reale = 0.1 CV, e così via). I numeri diventano molto più grandi
  e molto più assurdi.
- **Confronto inverso**: "questa mandria di 47 pecore equivale a una
  Panda 1.2".
- **Visualizzazione**: piccoli pittogrammi che si moltiplicano sullo
  schermo in proporzione al risultato.
- **Catalogo animali esteso**: drago, balena, formica
  (potenze proporzionate al ridicolo).

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
