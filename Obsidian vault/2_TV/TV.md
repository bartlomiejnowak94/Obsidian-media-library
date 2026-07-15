---
tags:
  - category
icon: "[[tv_icon.png]]"
created: 2025-10-12 13:40
last_modified: 2026-02-19 22:51
---

Ukończyłeś `$= dv.pages('"4_Listy/44_TV/Database"').where(p => p.rating).length` sezonów
Obejrzałeś `$= dv.pages('"4_Listy/44_TV/Database"').file.lists.text.map(p => p.toString().substring(2, 9)).where(p => p.substring(0, 2).match(20)).length` odcinków
`$= dv.pages('"4_Listy/44_TV/Database" and #backlog').length` planowanych

![[44_TV.base#Oglądam]]

---
[[44_TV.base#Backlog|Backlog]]

---
[[TV 2025]]
[[TV 2026]]

---

## Ile co roku?
```dataviewjs
const pages = dv.pages('"4_Listy/44_TV/Database"').file.etags.where(b => b.startsWith("#y")).groupBy(tag => tag).map(p => `${p.key},${p.rows.length})`)
dv.span(["~~~tinychart", ...pages, "~~~"].join("\n"))
```

## Rozkład ocen
```dataviewjs
// CONFIG
const sourceFolder = "4_Listy/44_TV/Database"
const yamlField = "rating";
//--------------------------------------------------
const all = dv.pages(`"${sourceFolder}"`).where(p => p.rating)
    .groupBy(p => p.file.frontmatter[yamlField])
    .values // dv-array to regular array
    .map(p => `${p.key},${p.rows.length}`)
dv.span(["~~~tinychart", ...all, "~~~"].join("\n"))
```
