---
theme: dashboard
toc: false
---

<style>
  body { background-color: #0a1628; }
  .card { background-color: #112240; }
</style>

# Emisiones Tóxicas en Puerto Rico 1987-2024

**Fuente:** https://www.epa.gov/toxics-release-inventory-tri-program/tri-basic-data-files-calendar-years-1987-present
```js
import * as aq from "npm:arquero";
const op = aq.op;
```
```js
async function readData(year) {
  try {
    let csvURL = `https://cdat.uprh.edu/~eramos/data/datos_EPA/${year}_pr.csv`;
    return aq.fromCSV(await fetch(csvURL).then(res => res.text()));
  } catch(e) {
    return aq.from(await FileAttachment("2024_pr.csv").csv({typed: true}));
  }
}
```
```js
const year = view(Inputs.range([1987, 2024], {label: "Año", step: 1, value: 2024}));
```
```js
const zoom = view(Inputs.range([7, 14], {label: "Zoom", step: 0.5, value: 9.5}));
```
```js
const showLegend = view(Inputs.toggle({label: "Leyenda", value: true}));
```
```js
const datosCrudos = await readData(year);
```
```js
const data = datosCrudos.select({
  "1. YEAR": "YEAR",
  "4. FACILITY NAME": "FACILITY",
  "6. CITY": "CITY",
  "12. LATITUDE": "LATITUDE",
  "13. LONGITUDE": "LONGITUDE",
  "23. INDUSTRY SECTOR": "INDUSTRY",
  "37. CHEMICAL": "CHEMICAL"
});
```
```js
const industryColors = {
  "Chemicals": "red",
  "Petroleum Bulk Terminals": "blue",
  "Electric Utilities": "green",
  "Electrical Equipment": "yellow",
  "Food": "purple",
  "Miscellaneous Manufacturing": "orange",
  "Fabricated Metals": "pink",
  "Petroleum": "brown",
  "Chemical Wholesalers": "gray",
  "Beverages": "cyan",
  "Machinery": "magenta",
  "Wood Products": "lime",
  "Primary Metals": "indigo",
  "Transportation Equipment": "teal",
  "Paper": "violet",
  "Other": "turquoise",
  "Hazardous Waste": "maroon",
  "Tobacco": "olive",
  "Plastics and Rubber": "silver",
  "Computers and Electronic Products": "gold"
};
```
```js
function plotHistogram(data, variable, cantidad, label) {
  const dataAgrupada = data
    .groupby(variable)
    .rollup({ count: op.count() })
    .orderby(aq.desc("count"))
    .reify()
    .slice(0, cantidad);

  return Plot.plot({
    title: `Distribución de Emisiones por ${label}`,
    width,
    height: 1200,
    marginLeft: 150,
    marginRight: 30,
    marginTop: 30,
    marginBottom: 60,
    x: { label: "FRECUENCIA", grid: true, nice: true },
    y: { label: label, grid: true },
    color: { legend: false },
    marks: [
      Plot.barX(dataAgrupada.objects(), {
        x: "count",
        y: variable,
        sort: { y: "-x" },
        fill: variable
      })
    ]
  });
}
```
```js
const L = await import("https://cdn.jsdelivr.net/npm/leaflet@1.9.4/dist/leaflet-src.esm.js");
```
```js
const leafletCss = document.createElement("link");
leafletCss.rel = "stylesheet";
leafletCss.href = "https://cdn.jsdelivr.net/npm/leaflet@1.9.4/dist/leaflet.css";
document.head.appendChild(leafletCss);
```
```js
function createLegend() {
  const div = document.createElement("div");
  div.style.cssText = `
    position: absolute;
    top: 10px;
    right: 10px;
    background: rgba(0,0,0,0.8);
    color: white;
    padding: 12px;
    border-radius: 8px;
    z-index: 1000;
    font-size: 12px;
    max-height: 400px;
    overflow-y: auto;
  `;
  div.innerHTML = `<b style="font-size:14px;">Sector Industrial</b><br><br>` +
    Object.entries(industryColors).map(([industry, color]) =>
      `<div style="display:flex; align-items:center; margin-bottom:4px;">
        <div style="width:12px; height:12px; background:${color}; border-radius:50%; margin-right:6px; flex-shrink:0;"></div>
        <span>${industry}</span>
      </div>`
    ).join("");
  return div;
}
```
```js
function createMap(data, zoom, showLegend) {
  const wrapper = document.createElement("div");
  wrapper.style.cssText = "width:100%; height:850px; position:relative;";

  const container = document.createElement("div");
  container.style.cssText = "width:100%; height:100%;";
  wrapper.appendChild(container);

  requestAnimationFrame(() => {
    const map = L.map(container).setView([18.22, -66.4], zoom);
    L.tileLayer("https://api.mapbox.com/styles/v1/mapbox/satellite-streets-v12/tiles/512/{z}/{x}/{y}@2x?access_token=YOUR_MAPBOX_TOKEN", {
      attribution: "© Mapbox © OpenStreetMap"
    }).addTo(map);

    data.objects().forEach(row => {
      const lat = parseFloat(row["LATITUDE"]);
      const lng = parseFloat(row["LONGITUDE"]);
      const industry = row["INDUSTRY"] || "Other";
      const color = industryColors[industry] || "white";

      if (!isNaN(lat) && !isNaN(lng)) {
        L.circleMarker([lat, lng], {
          radius: 6,
          fillColor: color,
          color: "transparent",
          weight: 0,
          fillOpacity: 0.8
        })
        .bindPopup(`
          <b>Año:</b> ${row["YEAR"]}<br>
          <b>Municipio:</b> ${row["CITY"]}<br>
          <b>Nombre de la Facilidad:</b> ${row["FACILITY"]}<br>
          <b>Sector Industrial:</b> ${row["INDUSTRY"]}<br>
          <b>Químico:</b> ${row["CHEMICAL"]}
        `)
        .addTo(map);
      }
    });

    if (showLegend) wrapper.appendChild(createLegend());
    invalidation.then(() => map.remove());
  });

  return wrapper;
}
```

<div class="grid grid-cols-3">
  <div class="card grid-colspan-2" style="min-height:850px;">${createMap(data, zoom, showLegend)}</div>
  <div style="display:flex; flex-direction:column; gap:1rem;">
    <div class="card">${resize((width) => plotHistogram(data, "CITY", 20, "MUNICIPIO"))}</div>
    <div class="card">${resize((width) => plotHistogram(data, "INDUSTRY", 20, "SECTOR INDUSTRIAL"))}</div>
  </div>
</div>