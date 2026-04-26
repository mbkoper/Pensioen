# FIRE BV Pensioensimulator

Een interactieve, client-side pensioensimulator speciaal gericht op **DGA's (directeuren-grootaandeelhouders) met een BV**. De tool rekent jaar-voor-jaar uit hoeveel netto inkomen je kunt opnemen vanuit je BV-vermogen, rekening houdend met de Nederlandse belastingen (IB en VPB), inflatie, AOW en sequence-of-returns risico.

Eén enkel HTML-bestand, geen back-end, geen installatie — gewoon openen in een browser.

---

## Functionaliteit

### Simulatiemodel

De simulator verdeelt je looptijd in drie **fases**:

| Fase | Kleur | Omschrijving |
|---|---|---|
| **Opbouw** | Grijs | Van vandaag tot de stopdatum. Geen opnames, vermogen groeit door rendement. |
| **Pre-pensioen** | Blauw | Van stopdatum tot AOW-leeftijd. Je leeft van BV-opnames; IB-brutering en VPB worden toegepast. |
| **Pensioen** | Groen | Vanaf AOW-leeftijd. AOW-uitkering wordt in mindering gebracht op de benodigde bruto-opname. |

Elk jaar wordt het volgende berekend:

1. **Rendement** op het belegde vermogen (uit de ingestelde rendementen-cyclus, 10-jarig patroon)
2. **VPB** over het rendement (19% tot € 200.000, 25,8% daarboven) — tenzij kostprijs-methode actief is
3. **Gewenst netto inkomen** (gecorrigeerd voor inflatie; in slechte jaren: minimumsaldo)
4. **Bruto-opname** via de IB-bruteringsformule (24,5% in de eerste schijf ≈ €67.000, 33% erboven)
5. **Buffer**: opnames worden eerst uit de cash-buffer gehaald; in goede jaren wordt de buffer aangevuld vanuit beleggingen

### Belastingparameters (hard-coded)

```
IB laag tarief  : 24,5%  (tot ~€67.000 netto)
IB hoog tarief  : 33%    (boven de eerste schijf)
VPB laag tarief : 19%    (tot €200.000 winst)
VPB hoog tarief : 25,8%  (boven €200.000)
```

---

## Instellingen

### Op = Op (Die With Zero)
Wanneer ingeschakeld zoekt de simulator via een binaire zoekopdracht (50 iteraties) het **maximale maandelijkse netto inkomen** waarmee het vermogen precies op de doelleeftijd op €0 eindigt. Handig om te zien wat maximaal haalbaar is.

Wanneer uitgeschakeld voer je zelf een gewenst netto maandbedrag in en berekent de simulator of en hoe lang dit haalbaar is.

### Beurs Rendementen
Kies een vooraf ingesteld rendementsprofiel of stel de 10-jarige cyclus handmatig in:

| Preset | Beschrijving |
|---|---|
| MSCI World (10j proxy) | Historische rendementen MSCI World index |
| Dow Jones / S&P 500 | Historische US-markt rendementen |
| Europa (AEX/STOXX) | Conservatievere Europese markt |
| Stabiel (6%) | Vlak rendement van 6% per jaar |

### Geavanceerde opties

**Kostprijs of lagere marktwaarde** — Als actief, betaal je VPB alleen bij daadwerkelijke opname (realisatie), niet jaarlijks over ongerealiseerde beleggingswinst. Dit is voordelig in groeijaren.

**Sequence of Returns Risk** — Herrangschikt de rendementscyclus zodat de **slechtste jaren eerst** komen. Dit simuleert het gevaarlijkste scenario: direct na pensionering een slechte beurs.

### Overige invoervelden

| Veld | Uitleg |
|---|---|
| Geboortedatum | Voor berekening van de leeftijd per jaar |
| Stopdatum (pre-pensioen) | Startdatum van opnames uit de BV |
| AOW Leeftijd | bijv. `67.25` = 67 jaar en 3 maanden |
| Doel Leeftijd | Tot welke leeftijd het vermogen moet reiken |
| Investeringen BV (€) | Huidig belegd vermogen in de BV |
| Cash Buffer BV (€) | Gewenste cash-buffer (wordt aangevuld vanuit beleggingen na goede beursjaren) |
| Min. Netto p/m crisis (€) | Minimum maandinkomen in slechte beursjaren (floor) |
| AOW Netto p/m (€) | Netto AOW-bedrag dat op de bruto-opname in mindering wordt gebracht |
| Inflatie (%) | Jaarlijkse inflatiefactor voor al het uitgavenpatroon |

---

## Resultatenkaart (bovenaan)

| Metric | Uitleg |
|---|---|
| Max. Netto / Ingesteld | Het actieve maandelijkse netto inkomen |
| Eindvermogen | Totaal vermogen (belegd + buffer) op de doelleeftijd |
| Laagste Buffer | Laagste bufferstand gedurende de hele simulatie |
| Voortgangsbalk | Percentage van de simulatiejaren met positief vermogen |

Kleurcodering: **groen** = plan slaagt, **oranje** = buffer raakt tijdelijk leeg, **rood** = vermogen raakt op vóór de doelleeftijd.

---

## Grafiek

Twee weergavemodi:

- **Totaal** — Totaal BV-vermogen (belegde + buffer) en cash-buffer als stippellijn
- **Belegd / Buffer** — Belegde portefeuille en cash-buffer als afzonderlijke gebieden

---

## Simulatietabel

Jaar-voor-jaar overzicht met:

- **Fase-indicatie** (Opbouw / Pre / Pensioen)
- **Beurs-rendement** dat jaar (groen = positief, rood = negatief)
- **Netto p/m** — werkelijk opgenomen netto maandinkomen
- **Extra** — invoerveld voor eenmalige extra uitgaven per jaar (bijv. verbouwing, vakantie)
- **Bruto** — totale bruto-opname inclusief IB
- **VPB** — vennootschapsbelasting over het rendement
- **Buffer** — eindstand cash-buffer dat jaar
- **Totaal** — totaal eindvermogen (belegd + buffer)

---

## Import / Export

- **Export** — Sla alle instellingen op als JSON-bestand (`pensioen_scenario.json`)
- **Import** — Laad een eerder opgeslagen JSON-bestand terug in
- **Wis Extra** — Verwijdert alle ingevoerde eenmalige extra uitgaven

---

## Gebruik

1. Open `pensioen.html` direct in een browser (geen server vereist)
2. Vul de instellingen in via het zijpaneel (desktop) of via de **Instellingen**-knop (mobiel)
3. De simulator herberekent direct bij elke wijziging
4. Gebruik Import/Export om scenario's op te slaan en te vergelijken

---

## Gebruikte technologieën

| Bibliotheek | Versie | Doel |
|---|---|---|
| [Vue 3](https://vuejs.org/) | 3 (unpkg CDN) | Reactieve UI-logica |
| [Bootstrap](https://getbootstrap.com/) | 5.3.0 | Layout, componenten, offcanvas |
| [Bootstrap Icons](https://icons.getbootstrap.com/) | 1.11.0 | Icoonset |
| [Chart.js](https://www.chartjs.org/) | latest (jsDelivr CDN) | Vermogensgrafiek |
| [Day.js](https://day.js.org/) | 1 (jsDelivr CDN) | Datum-parsing |

---

## Suggesties voor verbeteringen / alternatieve bibliotheken

### Grafieken — ApexCharts
[ApexCharts](https://apexcharts.com/) biedt rijkere interactiviteit dan Chart.js: inzoomen, annotaties op specifieke jaren (bijv. "AOW start"), tooltips met meer detail en mooiere standaardstijlen. Met name de **mixed chart** (lijn + gestapeld gebied) is ideaal voor dit type simulator.

```html
<script src="https://cdn.jsdelivr.net/npm/apexcharts"></script>
```

### CSS-framework — Tailwind CSS
[Tailwind CSS](https://tailwindcss.com/) geeft meer ontwerpvrijheid en een kleinere bundle bij gebruik van de CDN-build. Bootstrap is echter prima voor dit project en makkelijker te begrijpen zonder build-stap.

### Formulieren — Vue 3 Composition API + VueUse
Refactoring naar de Vue 3 Composition API (met `<script setup>`) verbetert de leesbaarheid van de simulatielogica. [VueUse](https://vueuse.org/) voegt handige composables toe zoals `useLocalStorage` (automatisch opslaan van instellingen) en `useWindowSize` (responsieve aanpassingen).

### Alternatief voor de gehele stack — Svelte
Voor een zeer slanke single-file app is [Svelte](https://svelte.dev/) een interessante keuze: geen virtual DOM, minimale bundle-grootte, en de reactieve syntax is bijzonder geschikt voor een calculator als deze.

### Aanbevolen volgende stap
De meest waardevolle toevoeging aan de huidige codebase zou **ApexCharts** zijn (als vervanging voor Chart.js) plus **`localStorage`** voor automatisch bewaren van de laatste invoer — zodat de gebruiker niet steeds opnieuw hoeft te importeren.
