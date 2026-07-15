---
tags:
  - year_summary
created: 2026-01-07 22:34
last_modified: 2026-07-04 19:53
---

Obejrzałeś `$= dv.pages('"4_Listy/41_Movies/Database"').file.frontmatter.watched.filter(p => p.toString().substring(2,12).substring(0,4) == 2026).length` filmów (w tym powtórki)
Widziałeś `$= dv.pages('"4_Listy/41_Movies/Database"').where(p => p.file.tags.includes("#movie/kino/2026")).length` w kinie

## Rozkład ocen filmów obejrzanych
```dataviewjs
const pages = dv.pages('"4_Listy/41_Movies/Database" and #y2026')
.where(p => p.rating)
.groupBy(p => p.rating)
.map(p => `${p.key},${p.rows.length}`)
dv.span(["~~~tinychart", ...pages, "~~~"].join("\n"))
```

## Ile co miesiąc
```dataviewjs
const pages = dv.pages('"4_Listy/41_Movies/Database" and #y2026')
.watched
.map(p => p.toString().replace("[[","").replace("]]","").split("|").slice(1,2).toString().substring(0, 15))
.where(p => p.substring(0, 4) == 2026)
.map(p => p.substring(5,7)).groupBy(t => t)
.map(p => `${p.key.replace("01","styczeń").replace("02","luty").replace("03","marzec").replace("04","kwiecień").replace("05","maj").replace("06","czerwiec").replace("07","lipiec").replace("08","sierpień").replace("09","wrzesień").replace("10","październik").replace("11","listopad").replace("12","grudzień")},${p.rows.length}`);
dv.span(["~~~tinychart", ...pages, "~~~"].join("\n"));
```

## Ile w dany dzień tygodnia
```dataviewjs
const pages = dv.pages('"4_Listy/41_Movies/Database" and #y2026')
.watched
.where(p => p.toString().substring(2,6) == 2026)
.map(p => dv.date(p.toString().substring(2, 12)))
.groupBy(p => p.weekday)
.map(p => `${p.key.toString().replace("1","Pn").replace("2","Wt").replace("3","Śr").replace("4","Cz").replace("5","Pt").replace("6","Sb").replace("7","Nd")},${p.rows.length}`)
dv.span(["~~~tinychart", ...pages, "~~~"].join("\n"))
```

```dataviewjs
const pages = dv.pages('"4_Listy/41_Movies/Database" and #y2026')
.watched
.map(p => p.toString().replace("[[","").replace("]]","").split("|").slice(1,2).toString())
.where(p => p.substring(0,4) == 2026)
.groupBy(p => p.substring(11,13))
.map(p => `${p.key},${p.rows.length}`);
dv.span(["~~~tinychart", ...pages, "~~~"].join("\n"));
```

## Lista filmów
1. [[Wake Up Dead Man (2025)]]
2. [[The Family Stone (2005)]]
3. [[Avatar Fire and Ash (2025)]]
4. [[Friz & Wersow. Love in the Online Age (2025)]]
5. [[Alice in Wonderland –Dive in Wonderland– (2025)]]
6. [[How to Train Your Dragon (2010)]]
7. [[Dom Dobry (2025)]]
8. [[How to Train Your Dragon 2 (2014)]]
9. [[How to Train Your Dragon The Hidden World (2019)]]
10. [[Dirty Dancing (1987)]]
11. [[Teściowie 3 (2025)]]
12. [[Teściowie (2021)]]
13. [[Sentimental Value (2025)]]
14. [[The Assessment (2024)]]
15. [[Harry Potter and the Philosopher's Stone (2001)]]
16. [[Harry Potter and the Chamber of Secrets (2002)]]
17. [[Finding Harry The Craft Behind the Magic (2026)]]
18. [[Chrzciny (2022)]]
19. [[Angel's Egg (1985)]]
20. [[The Drama (2026)]]
21. [[Project Hail Mary (2026)]]
22. [[Kung Fu Panda 4 (2024)]]
23. [[Summer Wars (2009)]]
24. [[The Devil Wears Prada 2 (2026)]]
25. [[Mortal Kombat II (2026)]]
26. [[Untold UK Liverpool's Miracle of Istanbul (2026)]]
27. [[Masters of the Universe (2026)]]
28. [[Good Fortune (2025)]]
29. [[Backrooms (2026)]]
30. [[Gladiator II (2024)]]
31. [[The Sheep Detectives (2026)]]

## Gdzie oglądałem
```dataviewjs
const kino = "#movie/kino/2026"
const netflix = "#movie/netflix/2026"
const prime = "#movie/prime/2026"
const hbo = "#movie/hbo/2026"
const pages = dv.pages('"4_Listy/41_Movies/Database" and #y2026')
.file.etags
.where(b => b.startsWith(kino) || b.startsWith(netflix) || b.startsWith(prime) || b.startsWith(hbo))
.groupBy(tag => tag)
.map(p => `${p.key.replace(kino,"kino").replace(netflix,"netflix").replace(prime,"prime").replace(hbo,"hbo")},${p.rows.length}`);
dv.span(["~~~tinychart", ...pages, "~~~"].join("\n"))
```

## Ile filmów z danego roku obejrzałem
```dataviewjs
const pages = dv.pages('"4_Listy/41_Movies/Database" and #y2026')
.where(p => p.rating)
.groupBy(p => p.year)
.map(p => `${p.key},${p.rows.length}`)
dv.span(["~~~tinychart", ...pages, "~~~"].join("\n"))
```

## Najczęściej oglądane z krajów:
```dataviewjs
const pages = dv.pages('"4_Listy/41_Movies/Database" and #y2026')
.countries
.groupBy(t => t)
.sort(p => p.rows.length, "desc")
.map(p => `${p.key.path},${p.rows.length}`).limit(10)
dv.span(["~~~tinychart", ...pages, "~~~"].join("\n"))
```

## Najczęściej oglądane gatunki
```dataviewjs
const pages = dv.pages('"4_Listy/41_Movies/Database" and #y2026')
.genres
.groupBy(t => t)
.sort(p => p.rows.length, "desc")
.map(p => `${p.key.path.replace("4_Listy/Gatunki/","").replace(".md","")},${p.rows.length}`).limit(10)
dv.span(["~~~tinychart", ...pages, "~~~"].join("\n"))
```

## Najczęściej oglądany reżyser
```dataview
Table length(rows.directors) AS ile, rows.file.link
FROM "4_Listy/41_Movies/Database" and #y2026
FLATTEN directors
GROUP BY directors
SORT length(rows.directors) DESC
LIMIT 5
```