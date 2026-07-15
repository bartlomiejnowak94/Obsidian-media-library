<%*
const apiKey = "";

const query = await tp.system.prompt("Podaj nazwę serialu");
if (!query) return;

const res = await fetch(`https://api.themoviedb.org/3/search/tv?api_key=${apiKey}&query=${encodeURIComponent(query)}&language=pl-PL`);
const data = await res.json();

if (data.results.length === 0) {
  tR += "Nie znaleziono.";
  return;
}

const picked = await tp.system.suggester(
  data.results.map(r => `${r.name} (${r.first_air_date?.slice(0,4) || "?"})`),
  data.results
);
if (!picked) return;

const details = await (await fetch(`https://api.themoviedb.org/3/tv/${picked.id}?api_key=${apiKey}&language=en-US`)).json();

const seasonsFiltered = details.seasons.filter(s => s.season_number !== 0);

const pickedSeason = await tp.system.suggester(
  seasonsFiltered.map(s => `Season ${s.season_number} (${s.episode_count} odc.)`),
  seasonsFiltered
);
if (!pickedSeason) return;

const seasonDetails = await (await fetch(`https://api.themoviedb.org/3/tv/${picked.id}/season/${pickedSeason.season_number}?api_key=${apiKey}&language=en-US`)).json();

const year = pickedSeason.air_date?.slice(0,4) || picked.first_air_date?.slice(0,4) || "";

const normalizeGenre = (name) => {  
if (name === "Action & Adventure") return ["Action", "Adventure"];  
if (name === "Sci-Fi & Fantasy") return ["Science Fiction", "Fantasy"];  
return [name];  
};  
  
const genres = details.genres  
.flatMap(g => normalizeGenre(g.name))  
.map(g => ` - "[[${g.toLowerCase()}]]"`)  
.join("\n");

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

const seasonImagesRes = await fetch(  
`https://api.themoviedb.org/3/tv/${picked.id}/season/${pickedSeason.season_number}?api_key=${apiKey}&language=en-US`  
);  
  
const seasonData = await seasonImagesRes.json();  
const seasonCoverPath = seasonData.poster_path;  
const coverPath = seasonCoverPath || picked.poster_path;  
  
const cover = coverPath  
? `https://media.themoviedb.org/t/p/w500${coverPath}`  
: "";

const tmdb_url = `https://www.themoviedb.org/tv/${picked.id}/season/${pickedSeason.season_number}`;

const now = tp.date.now("YYYY-MM-DD HH:mm"); 
const enTitle = picked.original_name;  
const plTitle = picked.name;

const cleanTitle = (title) => {  
return title  
.replace(/[\/\\:\*\?"<>\|]/g, "")
.replace(/\s+/g, " ")  
.trim();  
};

await tp.file.rename(`${cleanTitle(enTitle)} S${pickedSeason.season_number} (${year})`);

// 9. YAML
tR += `---
tags:
  - tv
  - backlog
aliases:  
  - "${plTitle}"
rating: 
current: false
paused: false
dropped: false
start:
end:
odcinek: 0
odcinki: ${seasonDetails.episodes.length}
seria: "[[${picked.original_name}]]"
nr_seri: "${pickedSeason.season_number}"
tmdb_url: ${tmdb_url}
cover: ${cover}
year: ${year}
genres:
${genres}
countries:
${countries}
created: ${now}
last_modified: ${now}
---

![cover|200](${cover})
`;
%>