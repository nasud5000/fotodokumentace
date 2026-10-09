# Fotodokumentace — poznámky k projektu

Jednosouborová webová aplikace (`index.html`, bez build kroku). MSAL Browser 3.30.0 se SRI,
Microsoft Graph, přísná CSP. Nasazeno na Netlify z větve `main`.

Druhá verze s přepínačem úložiště OneDrive/SharePoint se udržuje mimo repozitář
(`index-volitelne-uloziste.html`).

## Textové soubory ve složce provozovny

Rozhodnuto 2026-10-09. Všechny soubory jsou **datované a připojované** — žádný se nikdy
nepřepisuje ani nemaže.

| Soubor | Kdy vzniká | Obsah |
|---|---|---|
| `Aktualni_zarizeni_RRRR-MM-DD.txt` | při každém exportu | Revize všech 18 zařízení: co na prodejně je (s počtem fotek a režimem) a co ne |
| `Absence_zarizeni_RRRR-MM-DD.txt` | při každém exportu | Jen seznam chybějících zařízení |
| `Vymeneno_RRRR-MM-DD.txt` | jen proběhla-li výměna | Vyměněná zařízení s počtem fotek a časem |
| `Doplneno_RRRR-MM-DD.txt` | jen proběhlo-li doplnění | Doplněná zařízení |
| `Poznamky_RRRR-MM-DD.txt` | při uložení poznámky | Ruční poznámky, každý zápis s časem a režimem |
| `Puvodni_zarizeni_RRRR-MM-DD.txt` | při první fotce v režimu Výměna | Přejmenovaný starší `Absence_zarizeni_*.txt` |

Všechny zápisy jdou přes `pripojDoDenniho()`, který načte stávající obsah a nový blok
připojí na konec. Volitelný parametr `stitek` mění text v hranaté závorce (`revize`,
`výměna`, `doplnění`); bez něj se použije aktuální režim.

### Přejmenování na Puvodni_zarizeni

`prejmenujNaPuvodni()` se volá při **prvním nahrání fotky v režimu Výměna**, jednou za
relaci a provozovnu (`puvodniHotovo`). Vylistuje složku provozovny, najde všechny
`Absence_zarizeni*.txt` a přejmenuje je na `Puvodni_zarizeni*.txt` se zachováním data.
Narazí-li na obsazený název (druhá výměna), přidá `_2`, `_3`… — nic se nepřepíše.
Prázdná nebo neexistující složka je úspěch. Neúspěch nahrávání fotek **nezastaví**, jen
se zopakuje u další fotky a zmíní v závěrečné hlášce (`puvodniChyba`).

`Zmeny_*.txt` z commitu 916adb7 se už nezakládá — nahradily ho `Vymeneno` a `Doplneno`.

## Přihlášení

Firemní registrace Datasys je zabudovaná v kódu jako `VYCHOZI_CLIENT_ID`
(`03c14057-865d-4f3f-9eff-ae4ba9503885`) a `VYCHOZI_TENANT`
(`fd51798d-962f-4a26-aa45-66afc4240642`), aby se na každém zařízení nemusela zadávat.
U jednostránkové aplikace nejde o tajné údaje — Entra je dostává v každém
přihlašovacím požadavku.

`aktivniCfg()` dává přednost uložené konfiguraci z `localStorage` před zabudovanou,
takže kdo má v prohlížeči nastavené vlastní ID (např. osobní registraci
`49b6369d-217f-48fe-abce-fe05d5364d48` s prázdným tenantem), toho to neovlivní.
Pole v ⚙ zůstávají editovatelná a `obnovVychozi()` do nich vrátí zabudované hodnoty.
Nastavení se při startu už neotevírá.

Dvoufaktorové ověření se nenastavuje v aplikaci — vynucuje ho tenant
(Conditional Access / Security defaults) a MSAL ho jen respektuje.

## Režimy práce

`pasport` (první nafocení), `vymena` (staré fotky se přesunou do podsložky `old`,
nic se nemaže), `doplneni` (nové zařízení). Evidence toho, co se v relaci dělo, je
v `zmenyEvidence` pod klíčem `id|rezim`; nuluje se při přechodu na jinou provozovnu.

## Testy

jsdom harness mimo repozitář, 426 testů (přihlášení 78, nové funkce 216, regrese 132).
Po každé úpravě `index.html` je spustit a ověřit inventář funkcí proti předchozí verzi —
dřív se stalo, že příliš hladový regex smazal celé funkce.
