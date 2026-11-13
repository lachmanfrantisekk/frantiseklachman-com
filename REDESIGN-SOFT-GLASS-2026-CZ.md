# František Lachman — Soft Glass Blue (listopad 2026)

## Co tato verze dělá

Tato verze vznikla z **původního ZIPu `frantiseklachman-web-main.zip`**, nikoli z předchozí krémové/terakotové úpravy. Záměr: přibližně 40% modernizace, zachovat modrou identitu, zvětšit osobní vizitku a zachovat původní aplikaci.

**Design:** ledově modrá + námořnická, Inter, velmi jemné sklo, střídmé akcenty, přístupná navigace, mobilní menu, tmavě navy režim. Není použit žádný generovaný obrázek; náhled OG vznikl ze screenshotu lokální verze. Efekty pozadí jsou výrazně utlumené.

## Stránky

- `/` — úvodní vizitka, služby, výběr projektů, představení a výzva ke kontaktu.
- `/sluzby/` — původní přehled všech šesti služeb + podrobnější popisy čtyř oblastí.
- `/portfolio/` — původní projekty z Cloudflare D1, filtrování a možnost otevřít detail.
- `/kontakt/` — původní kontaktní formulář plus tři nepovinné otázky: funkce webu, inspirace, termín.
- `/project/<slug>` — původní detail projektu, načítání z API.
- `/admin/` — původní administrace **bez úprav JavaScriptu**.
- `/recenze.html` — původní stránka hodnocení s jemným sladěním barev; logika odesílání nezměněna.
- `/coffee-picker/` — původní projekt CoffeePicker včetně PWA souborů, beze změny.

Pro tři nové sekce existují skutečné složky `sluzby`, `portfolio`, `kontakt` se souborem `index.html`. Nezávisejí na 404 fallbacku, ale 404 zůstává zachována pro dynamické projektové cesty.

## Co zůstává zachováno

- React aplikace, původní API `/api/projects`, `/api/projects/:slug`, `/api/messages`, `/api/session` i administrace.
- Autentizace, práce se zprávami, správa projektů, editace, obrázky, podrobnosti projektů a ostatní operace stávajícího backendu.
- GitHub Pages a Cloudflare Worker/D1 — bez migrace databáze a bez změny DNS.
- Česká a anglická verze, světlý/tmavý režim (nyní se navíc preference ukládají v localStorage).
- Ochrana proti spamu v původním formuláři (honeypot), validace e-mailu a délky zprávy, chybové a úspěšné stavy.
- Stránka hodnocení a aplikace CoffeePicker.

Nové otázky v poptávkovém formuláři se přidávají jako **strukturovaný text do stávajícího pole `message`** v JSON payloadu `/api/messages`; backend nepotřebuje nová databázová pole. `name`, `email`, `subject`, `company`, `website` a `budget` zůstávají beze změny.

## Technické změny

- `assets/softglass.css`: nová vizuální vrstva. Jádro původní CSS zůstává v HTML.
- Hlavní React bundle je nadále vložený v HTML, stejně jako v původním ZIPu. Je rozšířený pouze o nové routy, vizitku, menu a poptávkové otázky.
- `index.html` a `404.html` sdílejí stejný upravený bundle; nové routy mají i vlastní HTML soubory.
- `sitemap.xml`: přidány tři nové adresy; `robots.txt` beze změny, soukromé cesty zůstávají vyloučené.
- Unikátní titulky, popisky, canonical a Open Graph metadata pro nové stránky, plus `social-preview.jpg`.
- Navigace má skutečné href a aktivní stav; na mobilu je jazyk přístupný v rozbalovacím menu.
- Všechna formulářová pole mají původní napojení i validaci; nové otázky jsou nepovinné.

## Výsledky lokálních testů

- JS syntaxe: kontrola Node.js `--check` úspěšná.
- Devět šířek: **360, 375, 390, 430, 768, 1024, 1280, 1440, 1920 px** bez horizontálního přetečení (automatizovaný test v Chromium s virtuálními daty).
- Navigace: Domů, Služby, Portfolio, Kontakt, mobilní menu. Kontrolovány obě velikosti 390 a 1440 px.
- CZ/EN a světlo/tma: včetně přepínání jazyků v mobilním menu.
- Formulář: validní mock POST na `/api/messages`; ověřené klíče a obsah nových tří otázek; zobrazení úspěšného stavu.
- Projekt: detail skutečně volá odpovídající API logiku; kontrola vykreslení na mock datech.
- Administrace: původní `admin/index.html` nebyl změněn; v lokálním mock testu se zobrazila vstupní stránka.

**Omezení:** produkční Cloudflare Worker a databáze D1 nejsou v nahraném ZIPu. Testy API proběhly na simulovaných odpovědích. Úspěšný skutečný zápis do produkční D1, přihlášení reálného administrátora a všechny admin operace vyžadují ověření po nasazení. Není vhodné předem tvrdit, že produkční integrace prošla end-to-end testy.

## Nasazení na GitHub Pages — bezpečný postup

1. **Zálohujte současný funkční repozitář** (GitHub Desktop: vytvořit nový branch nebo stáhnout ZIP).
2. Otevřete stažený ZIP, uvnitř je složka `frantiseklachman-web-main`.
3. **Zkopírujte obsah této složky**, ne samotnou nadřazenou složku, do kořene stávajícího repozitáře. Nahraďte odpovídající soubory a přidejte složky `sluzby`, `portfolio`, `kontakt` a CSS/OG asset.
4. Zkontrolujte, že `CNAME` zůstává v kořeni a obsahuje `frantiseklachman.com`.
5. Proveďte `Commit` a potom `Push origin` přes GitHub Desktop. Není potřeba Node.js ani build.
6. Otevřete `/`, `/sluzby/`, `/portfolio/`, `/kontakt/`, `/recenze.html`, `/admin/` a `/coffee-picker/`. Kontrolujte i přímé otevření odkazů v novém panelu.
7. V administraci se přihlaste, zkontrolujte seznam projektů a zpráv, otestujte úpravu projektu. Poptávkový formulář vyzkoušejte testovací zprávou a ověřte, že dorazila do D1/adminu.
8. Pokud dojde k problému, vraťte commit nebo přepněte na záložní branch.

### Co se **nesmí** udělat

- Nemazat Cloudflare Worker, D1 databázi, routy `/api` ani Cloudflare Access nastavení.
- Nepřepisovat původní `CNAME`, `/admin/` či `/coffee-picker/` podle náhodných vzorů.
- Nepředpokládat, že statický GitHub Pages umožní fungování formulářů bez již nasazeného Workeru.

## Srovnání s předchozí verzí

Předchozí redesign s krémovou a terakotovou kombinací **se v tomto balíčku vůbec nepoužívá**. Výsledkem je vlastní iterace původního modrého stylu.
