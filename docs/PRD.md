# PRD — Osobní katalog receptů

## 1. Přehled

Webová aplikace pro sdílený katalog receptů. Kdokoli na internetu může recepty prohlížet, přidávat, vyhledávat a filtrovat. Každý uživatel může upravovat a mazat pouze recepty, které sám přidal (identifikace přes prohlížeč/zařízení).

**Verze:** 1.0  
**Datum:** 2026-04-24  
**Platforma:** Desktop (mobilní verze plánována do v2)  
**Deployment:** Vercel + Cloudflare + GitHub

---

## 2. Cíle

- Mít jeden sdílený katalog receptů přístupný komukoli
- Rychle najít recept podle ingrediencí nebo způsobu přípravy
- Jednoduché přidávání receptů bez registrace

---

## 3. Uživatelé

Jeden typ uživatele — anonymní návštěvník. Žádná autentizace, žádné role.

Identita je vázána na prohlížeč (token v localStorage/cookie), aby bylo možné rozlišit „vlastní" recepty.

---

## 4. Funkční požadavky

### 4.1 Recepty — datový model

| Pole | Typ | Povinné |
|---|---|---|
| Název | text | ✓ |
| Ingredience | strukturovaný seznam (množství + jednotka + název položky) | ✓ |
| Postup | rich text (tučné, odrážky, číslovaný seznam) | ✓ |
| Čas přípravy | číslo (minuty) | ✓ |
| Počet porcí | číslo | ✓ |
| Kategorie | výběr ze seznamu | ✓ |
| Fotka | obrázek (upload) | — |

### 4.2 Kategorie

- Pevný výchozí seznam (např. Polévky, Hlavní jídla, Přílohy, Dezerty, Nápoje, Snídaně, Ostatní)
- Kdokoli může přidat novou kategorii
- Kategorie jsou globální (sdílené pro všechny recepty)

### 4.3 Vyhledávání a filtrování

- Fulltextové vyhledávání přes **název**, **ingredience** i **postup**
- Příklady dotazů: „kuřecí", „grilovat", „čokoláda"
- Filtrování podle kategorie (dropdown nebo tagy)
- Výsledky v reálném čase nebo po potvrzení (UX rozhodnutí při implementaci)

### 4.4 Správa vlastních receptů

- Recept lze **upravovat** a **mazat** pouze ze zařízení/prohlížeče, ze kterého byl přidán
- Identifikace přes unikátní token uložený v localStorage (vygenerovaný při prvním přidání receptu)
- Žádná globální administrace — není admin role

### 4.5 Fotky

- Volitelný upload obrázku k receptu
- Zobrazení v náhledu na kartě receptu i v detailu
- Úložiště: Cloudflare R2 nebo Vercel Blob (rozhodnutí při implementaci)

---

## 5. Nefunkční požadavky

- **Přístupnost:** Veřejná URL bez přihlášení
- **Platforma:** Desktop prohlížeče (Chrome, Firefox, Safari, Edge) — responzivita není požadavkem v1
- **Výkon:** Fulltextové vyhledávání musí odpovídat do 500 ms pro katalog do ~1 000 receptů
- **Perzistence:** Data přežijí restart serveru (databáze, ne in-memory)

---

## 6. Doporučený tech stack

| Vrstva | Technologie | Důvod |
|---|---|---|
| Frontend + API | **Next.js (App Router)** | Nativní podpora Vercelu, SSR + API routes v jednom projektu |
| Databáze | **Neon (PostgreSQL)** | Serverless Postgres, bezplatný tier, plná podpora fulltext search |
| Obrázky | **Cloudflare R2** | Levné, CDN zahrnut, přímá integrace |
| Styling | **Tailwind CSS** | Rychlý vývoj, bez nutnosti vlastního CSS systému |
| ORM | **Prisma** | Typová bezpečnost, snadné migrace |

---

## 7. Mimo scope v1

- Mobilní / responzivní design
- Komentáře a hodnocení receptů
- Import receptů z URL
- Nutričné hodnoty
- Tisk receptu
- Pokročilé filtrování (čas přípravy, počet porcí)

---

## 8. Rozhodnutí

| # | Otázka | Rozhodnutí |
|---|---|---|
| 1 | Strukturované ingredience vs. volný text | Strukturovaný seznam (množství + jednotka + název) |
| 2 | Rich text vs. plain text v postupu | Rich text (tučné, odrážky, číslovaný seznam) |
| 3 | Výchozí řazení receptů | Nejnovější nahoře (podle data přidání DESC) |
| 4 | Limit na počet kategorií | Bez limitu v1 |
