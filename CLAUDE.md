# Fotodokumentace — poznámky k projektu

Jednosouborová webová aplikace (`index.html`, bez build kroku). MSAL Browser 3.30.0 se SRI,
Microsoft Graph, přísná CSP. Nasazeno na Netlify z větve `main`.

Druhá verze s přepínačem úložiště OneDrive/SharePoint se udržuje mimo repozitář
(`index-volitelne-uloziste.html`).

## OTEVŘENÉ ROZHODNUTÍ — struktura textových souborů

**Parkováno 2026-10-04 na výslovné přání majitele. Neimplementovat bez jeho rozhodnutí.**

Až napíše „pokračuj", je třeba mu znovu položit tento dotaz se třemi variantami
(AskUserQuestion), ne rovnou něco stavět.

> **Jak mají být soubory pojmenované?** Minule bylo zásadní, aby se nic nepřepisovalo
> (proto datum v názvu), ale „aktuální zařízení" naopak zní jako jeden soubor,
> který vždy ukazuje současný stav.
>
> 1. **Vše s datem, připojuje se (doporučeno)** — `Aktualni_zarizeni_2026-10-04.txt`,
>    `Vymeneno_2026-10-04.txt`, `Doplneno_2026-10-04.txt`. Nic se nikdy nepřepíše,
>    každá návštěva má svůj záznam.
> 2. **Aktuální pevný, ostatní s datem** — `Aktualni_zarizeni.txt` se přepisuje tak, aby
>    vždy ukazoval současný stav; `Vymeneno` a `Doplneno` se hromadí s datem.
> 3. **Všechny pevné názvy** — `Aktualni_zarizeni.txt`, `Vymeneno.txt`, `Doplneno.txt`;
>    vždy jen poslední stav, starší záznamy se ztrácejí.

Zadání, ze kterého to vzešlo: tři txt soubory — při výměně po pasportizaci přejmenovat
`Absence_zarizeni` na `Puvodni_zarizeni`, dále `Vymeneno`, `Doplneno`, a `Aktualni_zarizeni`
s revizí všech aktuálních zařízení.

Navazující otázky, které zatím nemají odpověď:

- Kdy přejmenovat na `Puvodni_zarizeni` — při prvním nahrání v režimu Výměna, až při
  exportu, nebo ručně? A co se stane při druhé výměně, přepíše se, nebo verzuje?
- Nahradí `Vymeneno` + `Doplneno` stávající `Zmeny_*.txt`, nebo poběží vedle sebe?
- Má se `Absence_zarizeni` dál zakládat?

## Stav před zaparkováním (commit 916adb7)

Do složky `Fotodokumentace/<provozovna>/` se zapisují tři textové soubory, všechny
datované a **připojované** — žádný se nikdy nepřepisuje:

| Soubor | Obsah |
|---|---|
| `Absence_zarizeni_RRRR-MM-DD.txt` | Ucelený soupis: chybějící i nafocená zařízení s počty fotek |
| `Zmeny_RRRR-MM-DD.txt` | Sekce Výměna / Doplnění / Pasportizace — co se v relaci dělo |
| `Poznamky_RRRR-MM-DD.txt` | Ruční poznámky, každý zápis s časem a režimem |

Všechny tři jdou přes společný helper `pripojDoDenniho()`, který načte stávající obsah
a nový blok připojí na konec.

## Testy

jsdom harness mimo repozitář, 374 testů (přihlášení 78, nové funkce 166, regrese 130).
Po každé úpravě `index.html` je spustit a ověřit inventář funkcí proti předchozí verzi —
dřív se stalo, že příliš hladový regex smazal celé funkce.
