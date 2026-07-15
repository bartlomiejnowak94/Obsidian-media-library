---
tags:
  - category
icon: "[[movie_icon.png]]"
created: 2025-09-28 19:20
last_modified: 2026-03-22 19:30
---

Cel tegoroczny:  `$= "<progress style='width:100px' value='" + dv.pages('"4_Listy/41_Movies/Database" and #y2026').length + "' max='80'></progress>" + " " + dv.pages('"4_Listy/41_Movies/Database" and #y2026').length + "/80 obejrzane"`

`$= dv.pages('"4_Listy/41_Movies/Database"').where(p => p.rating).length` obejrzanych
`$= dv.pages('"4_Listy/41_Movies/Database" and #backlog').length` planowanych

![[41_Movies.base#Ostatnio oglądane]]

![[41_Movies.base#Paused]]

---
[[41_Movies.base#Backlog|Backlog]]

---
[[Movies 2026]]
[[Movies 2025]]
[[Movies 2019]]

---
## Ile co roku?
```dataviewjs
const pages = dv.pages('"4_Listy/41_Movies/Database"').file.etags.where(b => b.startsWith("#y")).groupBy(tag => tag).map(p => `${p.key},${p.rows.length})`)
dv.span(["~~~tinychart", ...pages, "~~~"].join("\n"))
```

## Rozkład ocen
```dataviewjs
// CONFIG
const sourceFolder = "4_Listy/41_Movies/Database"
const yamlField = "rating";
//--------------------------------------------------
const all = dv.pages(`"${sourceFolder}"`).where(p => p.rating)
    .groupBy(p => p.file.frontmatter[yamlField])
    .values // dv-array to regular array
    .map(p => `${p.key},${p.rows.length}`)
dv.span(["~~~tinychart", ...all, "~~~"].join("\n"))
```

## Kraje
```dataviewjs
// 🌍 Pobierz gotową mapę GeoJSON
const url = "https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/world.geojson";
const geo = await fetch(url).then(r => r.json());

// 🔢 ZLICZANIE krajów (obsługa [[linków]] i tekstu)
const pages = dv.pages('"4_Listy/41_Movies/Database"');
let countryCount = {};

// funkcja wyciągająca nazwę kraju z linku lub tekstu
function getCountryName(c) {
  if (!c) return null;
  if (typeof c === "object" && c.path) {
    return c.path.split("/").pop().replace(".md", "");
  }
  return c;
}

// iteracja po notatkach
for (let p of pages) {
  let countries = p.countries;
  if (!countries) continue;

  if (Array.isArray(countries)) {
    for (let c of countries) {
      const name = getCountryName(c);
      if (!name) continue;
      countryCount[name] = (countryCount[name] || 0) + 1;
    }
  } else {
    const name = getCountryName(countries);
    if (!name) continue;
    countryCount[name] = (countryCount[name] || 0) + 1;
  }
}

// 🎨 Skala kolorów
const max = Math.max(...Object.values(countryCount), 1);
const getColor = v => `rgba(162,241,70,${0.1 + (v/max)*0.8})`;

// 🧠 Rysowanie SVG
let paths = "";

// funkcja rysująca poligon
function drawPolygon(poly) {
  return poly.map(ring =>
    "M" + ring.map(p => `${p[0]*2+500},${-p[1]*2+250}`).join("L") + "Z"
  ).join("");
}

geo.features.forEach(f => {
  const name = f.properties.name;
  const count = countryCount[name] || 0;
 // const color = getColor(count);
  const color = count === 0
	  ? "#444"
	  : getColor(count);

  const coords = f.geometry.coordinates;
  let d = "";

  if (f.geometry.type === "Polygon") {
    d = drawPolygon(coords);
  } else if (f.geometry.type === "MultiPolygon") {
    d = coords.map(drawPolygon).join("");
  }
  
  paths += `<path d="${d}" fill="${color}" stroke="#000" stroke-width="0.5">
  <title>${name}: ${count}</title>
</path>`;
});

// 🌐 Wstawienie SVG do kontenera
dv.container.innerHTML = `
<svg viewBox="0 0 1000 500" style="width:100%;background:transparent">
${paths}
</svg>
`;
```