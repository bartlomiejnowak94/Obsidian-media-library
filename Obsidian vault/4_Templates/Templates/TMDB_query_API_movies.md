<%*
const apiKey = "";

const query = await tp.system.prompt("Podaj nazwę filmu");
if (!query) return;

const res = await fetch(`https://api.themoviedb.org/3/search/movie?api_key=${apiKey}&query=${encodeURIComponent(query)}&language=pl-PL`);
const data = await res.json();

if (data.results.length === 0) {
  tR += "Nie znaleziono.";
  return;
}

const picked = await tp.system.suggester(
  data.results.map(r => `${r.title} (${r.release_date?.slice(0,4) || "?"})`),
  data.results
);
if (!picked) return;

const details = await (await fetch(`https://api.themoviedb.org/3/movie/${picked.id}?api_key=${apiKey}&language=en-US`)).json();

const credits = await (await fetch(`https://api.themoviedb.org/3/movie/${picked.id}/credits?api_key=${apiKey}`)).json();

const directorsList = credits.crew.filter(c => c.job === "Director");
const directors = directorsList
  .map(d => `  - "[[${d.name}]]"`)
  .join("\n");

const external = await (await fetch(`https://api.themoviedb.org/3/movie/${picked.id}/external_ids?api_key=${apiKey}`)).json();

const year = picked.release_date?.slice(0,4) || "";

const normalizeGenre = (name) => {  
  if (name === "Science Fiction") return ["science fiction"];
  return [name.toLowerCase()];
};

const genres = details.genres  
  .flatMap(g => normalizeGenre(g.name))  
  .map(g => `  - "[[${g}]]"`)  
  .join("\n");

const runtime = details.runtime || "";

const countryMap = {
  AF: "Afghanistan",
  AL: "Albania",
  DZ: "Algeria",
  AO: "Angola",
  AQ: "Antarctica",
  AR: "Argentina",
  AM: "Armenia",
  AU: "Australia",
  AT: "Austria",
  AZ: "Azerbaijan",
  BD: "Bangladesh",
  BY: "Belarus",
  BE: "Belgium",
  BZ: "Belize",
  BJ: "Benin",
  BT: "Bhutan",
  BO: "Bolivia",
  BA: "Bosnia and Herzegovina",
  BW: "Botswana",
  BR: "Brazil",
  BN: "Brunei",
  BG: "Bulgaria",
  BF: "Burkina Faso",
  BI: "Burundi",
  KH: "Cambodia",
  CM: "Cameroon",
  CA: "Canada",
  CF: "Central African Republic",
  TD: "Chad",
  CL: "Chile",
  CN: "China",
  CO: "Colombia",
  CR: "Costa Rica",
  HR: "Croatia",
  CU: "Cuba",
  CY: "Cyprus",
  CZ: "Czech Republic",
  CD: "Democratic Republic of the Congo",
  DK: "Denmark",
  DJ: "Djibouti",
  DO: "Dominican Republic",
  TL: "East Timor",
  EC: "Ecuador",
  EG: "Egypt",
  SV: "El Salvador",
  UK: "England",
  GQ: "Equatorial Guinea",
  ER: "Eritrea",
  EE: "Estonia",
  ET: "Ethiopia",
  FK: "Falkland Islands",
  FJ: "Fiji",
  FI: "Finland",
  FR: "France",
  TF: "French Southern and Antarctic Lands",
  GA: "Gabon",
  GM: "Gambia",
  GE: "Georgia",
  DE: "Germany",
  GH: "Ghana",
  GR: "Greece",
  GL: "Greenland",
  GT: "Guatemala",
  GN: "Guinea",
  GW: "Guinea Bissau",
  GY: "Guyana",
  HT: "Haiti",
  HN: "Honduras",
  HU: "Hungary",
  IS: "Iceland",
  IN: "India",
  ID: "Indonesia",
  IR: "Iran",
  IQ: "Iraq",
  IE: "Ireland",
  IL: "Israel",
  IT: "Italy",
  CI: "Ivory Coast",
  JM: "Jamaica",
  JP: "Japan",
  JO: "Jordan",
  KZ: "Kazakhstan",
  KE: "Kenya",
  XK: "Kosovo",
  KW: "Kuwait",
  KG: "Kyrgyzstan",
  LA: "Laos",
  LV: "Latvia",
  LB: "Lebanon",
  LS: "Lesotho",
  LR: "Liberia",
  LY: "Libya",
  LT: "Lithuania",
  LU: "Luxembourg",
  MK: "Macedonia",
  MG: "Madagascar",
  MW: "Malawi",
  MY: "Malaysia",
  ML: "Mali",
  MR: "Mauritania",
  MX: "Mexico",
  MD: "Moldova",
  MN: "Mongolia",
  ME: "Montenegro",
  MA: "Morocco",
  MZ: "Mozambique",
  MM: "Myanmar",
  NA: "Namibia",
  NP: "Nepal",
  NL: "Netherlands",
  NC: "New Caledonia",
  NZ: "New Zealand",
  NI: "Nicaragua",
  NE: "Niger",
  NG: "Nigeria",
  KP: "North Korea",
  NO: "Norway",
  OM: "Oman",
  PK: "Pakistan",
  PA: "Panama",
  PG: "Papua New Guinea",
  PY: "Paraguay",
  PE: "Peru",
  PH: "Philippines",
  PL: "Poland",
  PT: "Portugal",
  PR: "Puerto Rico",
  QA: "Qatar",
  RS: "Republic of Serbia",
  CG: "Republic of the Congo",
  RO: "Romania",
  RU: "Russia",
  RW: "Rwanda",
  SA: "Saudi Arabia",
  SN: "Senegal",
  SL: "Sierra Leone",
  SK: "Slovakia",
  SI: "Slovenia",
  SB: "Solomon Islands",
  SO: "Somalia",
  ZA: "South Africa",
  KR: "South Korea",
  SS: "South Sudan",
  ES: "Spain",
  LK: "Sri Lanka",
  SD: "Sudan",
  SR: "Suriname",
  SZ: "Swaziland",
  SE: "Sweden",
  CH: "Switzerland",
  SY: "Syria",
  TW: "Taiwan",
  TJ: "Tajikistan",
  TH: "Thailand",
  BS: "The Bahamas",
  TG: "Togo",
  TT: "Trinidad and Tobago",
  TN: "Tunisia",
  TR: "Turkey",
  TM: "Turkmenistan",
  US: "USA",
  UG: "Uganda",
  UA: "Ukraine",
  AE: "United Arab Emirates",
  TZ: "United Republic of Tanzania",
  UY: "Uruguay",
  UZ: "Uzbekistan",
  VU: "Vanuatu",
  VE: "Venezuela",
  VN: "Vietnam",
  PS: "West Bank",
  EH: "Western Sahara",
  YE: "Yemen",
  ZM: "Zambia",
  ZW: "Zimbabwe"
};

const countries = details.production_countries.map(c => {
  const name = countryMap[c.iso_3166_1] || c.iso_3166_1;
  return `  - "[[${name}]]"`;
}).join("\n");

const cover = picked.poster_path
  ? `https://image.tmdb.org/t/p/w500${picked.poster_path}`
  : "";

const tmdb_url = `https://www.themoviedb.org/movie/${picked.id}`;
const imdb_url = external.imdb_id ? `https://www.imdb.com/title/${external.imdb_id}/` : "";

const slug = picked.title
  .toLowerCase()
  .replace(/[^\w\s-]/g, "")
  .replace(/\s+/g, "-");

const lboxd_url = `https://letterboxd.com/film/${slug}/`;

const now = tp.date.now("YYYY-MM-DD HH:mm");

const enTitle = picked.original_title;
const plTitle = picked.title;

const cleanTitle = (title) => {  
  return title  
    .replace(/[\/\\:\*\?"<>\|]/g, "")  
    .replace(/\s+/g, " ")  
    .trim();  
};

await tp.file.rename(`${cleanTitle(enTitle)} (${year})`);

// YAML
tR += `---
tags:
  - "movie"
  - "backlog"
aliases:
  - "${plTitle}"
rating:
paused: false
dropped: false
watched:
seria:
nr_seri:
lboxd_url: "${lboxd_url}"
imdb_url: "${imdb_url}"
tmdb_url: "${tmdb_url}"
cover: "${cover}"
year: ${year}
directors:
${directors}
countries:
${countries}
genres:
${genres}
runtime: ${runtime}
created: ${now}
last_modified: ${now}
---

![cover|200](${cover})
`;
%>