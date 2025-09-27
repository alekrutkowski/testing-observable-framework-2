---
toc: false
---

<!-- Important! Input values returned by view() calls can be used only in a separate js code block. -->
<!-- Think: a separate spreadsheet cell to avoid a circular reference. -->
<!-- See https://gist.githubusercontent.com/alekrutkowski/797ca03a87743ca6e25f5a27584f8b53 -->
<!-- See https://talk.observablehq.com/t/how-to-use-input-values-for-computation-in-an-observable-doc/9055 -->



<!-- Imports async functon fetchCSVtoObjects -->
<!-- Fetches a CSV contents and puts its rows into an array of objects -->
<!-- See https://gist.githubusercontent.com/alekrutkowski/797ca03a87743ca6e25f5a27584f8b53 -->
<!-- The raw gist contents needs to be served through a proxy e.g. githack.com with proper Content-Type header (as application/javascript not text/plain MIME type) -->
<script src="https://gist.githack.com/alekrutkowski/797ca03a87743ca6e25f5a27584f8b53/raw/f3ba0f5b4f68c659f1d2cdba6e0eda956251ff3d/fetchCSVtoObjects.js"></script>

<!-- For JavaScript "data frames": https://idl.uw.edu/arquero -->
<!-- Arquero will be imported into the aq global object -->
<script src="https://cdn.jsdelivr.net/npm/arquero@latest"></script>

<h1 style="font-family:sans-serif;">Observable Framework meets Eurostat</h1>

<div class="mycontainer">

```js
const large_plots_toggle =
  view(Inputs.checkbox([" "],{label: html`<h3>Show large plots?</h3>`, value: false, width: 120}));
```

```js
const varmap =
    new Map([
      ["Real GDP growth (in %)","CLV_PCH_PRE"],
      ["Inflation (GDP deflator index change in %)","PD_PCH_PRE_NAC"]
    ]);
const inversed_varmap = 
  new Map([...varmap].map(([k, v]) => [v, k]));
    
const variable = view(
  Inputs.select(
    varmap,
    {value: "CLV_PCH_PRE", 
     label: html`<h3>Select a variable</h3>`}
  )
);
```

```js

const kbd_info =
  html`<div class="tooltip">🛈
  <span class="tooltiptext">
  <kbd>Ctrl</kbd> + Click or <kbd>Shift</kbd> + Click to select multiple items
  </span>
  </div>`

const mydata = 
  await fetchCSVtoObjects("https://ec.europa.eu/eurostat/api/dissemination/sdmx/3.0/data/dataflow/ESTAT/nama_10_gdp/1.0/*.*.*.*?c[freq]=A&c[unit]=CLV_PCH_PRE,PD_PCH_PRE_NAC&c[na_item]=B1GQ&c[TIME_PERIOD]=2015,2016,2017,2018,2019,2020,2021,2022,2023,2024&compress=false&format=csvdata&formatVersion=2.0&lang=en&labels=name");

const countries = view(
  Inputs.select(
    new Map([
      ["European Union","EU27_2020"],
      ["Belgium","BE"],
      ["Bulgaria","BG"],
      ["Czechia","CZ"],
      ["Denmark","DK"],
      ["Germany","DE"],
      ["Estonia","EE"],
      ["Ireland","IE"],
      ["Greece","EL"],
      ["Spain","ES"],
      ["France","FR"],
      ["Croatia","HR"],
      ["Italy","IT"],
      ["Cyprus","CY"],
      ["Latvia","LV"],
      ["Lithuania","LT"],
      ["Luxembourg","LU"],
      ["Hungary","HU"],
      ["Malta","MT"],
      ["Netherlands","NL"],
      ["Austria","AT"],
      ["Poland","PL"],
      ["Portugal","PT"],
      ["Romania","RO"],
      ["Slovenia","SI"],
      ["Slovakia","SK"],
      ["Finland","FI"],
      ["Sweden","SE"]
    ]),
    {value: ["EU27_2020"], 
     label: html`<h3>Select one or more countries</h3>${kbd_info}`,
     multiple: 2}
  )
);
```

</div> <!-- Closing tag for <div class="mycontainer"> -->

```js
const large_plots =
  large_plots_toggle == " ";
const col_num =
  large_plots ? 1 : 2;
```


```js
const mydata1 = aq.from(mydata)
  .params({countries, variable})
  .filter(d => aq.op.includes(countries, d.geo))
  .filter(d => d.unit==variable);
  
const scaling_factor =
  3 - col_num;

const chart1 = resize(width => {
  return Plot.plot({
    width: width*scaling_factor,
    height: .5*width*scaling_factor,
    title: inversed_varmap.get(variable),
    style: {overflow: "visible", fontSize: 12*scaling_factor},
    y: {grid: true, label: null},
    x: {tickFormat: "", label: null,  // tickFormat: "" shows 2015 not 2,015
        ticks: d3.range(d3.min(mydata1, d => d.TIME_PERIOD), d3.max(mydata1, d => d.TIME_PERIOD) + 1, 1)}, // to avoid fractional ticks, doesn't make sense for years
    color: {legend: true},
    marks: [
      Plot.ruleY([0]),
      Plot.lineY(mydata1, {x: "TIME_PERIOD", y: "OBS_VALUE", stroke: "geo", strokeWidth: 1.5*scaling_factor}),
      Plot.text(mydata1, Plot.pointerX({x: 'TIME_PERIOD', y: 'OBS_VALUE', text: 'OBS_VALUE'}))
    ]
  });
});

```

```js
const mydiv = html`
  <div class="${"grid grid-cols-" + col_num}">
    <div class="card">${chart1}</div>
  </div>`
```


<div>${mydiv}<div>


<!-- Uncomment if you want to remove the "Built with Observable on ...." text.
```js
const observer = new MutationObserver(() => {
  const footer = document.getElementById("observablehq-footer");
  if (footer) {
    footer.remove();
    observer.disconnect(); // stop observing once it's gone
  }
});
observer.observe(document.body, { childList: true, subtree: true });
```
-->

<style>

.hero {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: var(--sans-serif);
  margin: 4rem 0 8rem;
  text-wrap: balance;
  text-align: center;
}

.hero h1 {
  margin: 1rem 0;
  padding: 1rem 0;
  max-width: none;
  font-size: 14vw;
  font-weight: 900;
  line-height: 1;
  background: linear-gradient(30deg, var(--theme-foreground-focus), currentColor);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.hero h2 {
  margin: 0;
  max-width: 34em;
  font-size: 20px;
  font-style: initial;
  font-weight: 500;
  line-height: 1.5;
  color: var(--theme-foreground-muted);
}

@media (min-width: 640px) {
  .hero h1 {
    font-size: 90px;
  }
}

/* Base input style */
input[type="text"],
input[type="number"],
input[type="email"],
input[type="search"],
input[type="password"],
textarea,
select {
  width: 100%;
  padding: 0.5rem 0.75rem;
  border: 1px solid var(--theme-border, #d0d7de);
  border-radius: 0.5rem;
  background-color: var(--theme-bg, #fff);
  font-size: 0.95rem;
  font-family: inherit;
  transition: border-color 0.2s ease, box-shadow 0.2s ease;
  box-sizing: border-box;
}

/* On focus */
input:focus,
textarea:focus,
select:focus {
  outline: none;
  border-color: var(--theme-accent, #1f77b4);
  box-shadow: 0 0 0 3px rgba(31, 119, 180, 0.25);
}

/* Placeholder styling */
::placeholder {
  color: #9ca3af;
}

/* Disabled */
input:disabled,
textarea:disabled,
select:disabled {
  background-color: #f3f4f6;
  color: #9ca3af;
  cursor: not-allowed;
}

/* Buttons */
button {
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
  border: none;
  background-color: var(--theme-accent, #1f77b4);
  color: white;
  font-weight: 500;
  cursor: pointer;
  transition: background-color 0.2s ease, box-shadow 0.2s ease;
}

button:hover {
  background-color: #155d8a;
}

button:disabled {
  background-color: #9ca3af;
  cursor: not-allowed;
}

kbd {
  display: inline-block;
  padding: 0.2em 0.4em;
  font-size: 0.85em;
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
  line-height: 1;
  color: var(--theme-text, #111);
  background-color: var(--theme-bg2, #f3f4f6);
  border: 1px solid var(--theme-border, #d0d7de);
  border-radius: 0.25rem;
  box-shadow: 0 1px 0 rgba(0,0,0,0.2), inset 0 0 0 1px #fff;
  white-space: nowrap;
}

.tooltip {
  position: relative;
  display: inline-block;
  cursor: pointer;
}

.tooltip .tooltiptext {
  visibility: hidden;
  opacity: 0;
  transition: opacity 0.2s;

  position: absolute;
  bottom: 125%;
  left: 50%;
  transform: translateX(-50%);

  background: #fff;            /* white background */
  color: #333;                 /* dark text */
  border: 1px solid #ccc;      /* light border */
  padding: 6px 10px;
  border-radius: 4px;
  white-space: nowrap;
  box-shadow: 0 2px 6px rgba(0,0,0,0.15);
  z-index: 1;
}

.tooltip:hover .tooltiptext {
  visibility: visible;
  opacity: 1;
}

.mycontainer {
  width: 100%;
}
.mycontainer > div {
  display: inline-block;
  margin: 5px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  height: 50px;
}

</style>
