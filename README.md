# Evidence projektů SEA-l

Webová appka pro evidenci projektů a podprojektů, prezenčních listin, zaměstnanců a jejich smluv, klientů a pracovních výkazů (kontrola → schválení → výplata). Je to jeden soubor `index.html` a běží zdarma na GitHub Pages, bez vlastního serveru.

Appka je samostatný projekt, úplně oddělený od evidence doučování: vlastní repozitáře, vlastní adresa, vlastní přístupy i vlastní šifrovací klíč.

## Jak jsou data chráněná

| Kde | Co tam je | Kdo to vidí |
|---|---|---|
| `evidence-projektu/index.html` (veřejný repozitář) | jen appka, **žádná data** | kdokoli |
| `evidence-projektu/pristupy.json` (veřejný repozitář) | seznam uživatelů a jejich **zašifrované** tokeny | kdokoli, ale bez hesla je obsah nečitelný |
| `evidence-projektu-data/data.json` (**soukromý** repozitář) | všechna data, **zašifrovaná** (AES-256) | jen lidé s tokenem, a i ti jen s heslem |

- Každý člověk má vlastní jméno, heslo a vlastní token z GitHubu.
- Heslo odemyká jeho token a klíč k datům (PBKDF2, 600 000 iterací). Hesla musí mít aspoň 12 znaků. Nejlepší je věta z několika slov.
- Ani při úniku tokenu nebo přístupu do GitHub účtu nejsou data čitelná bez hesla.
- Appka se odhlásí po 30 minutách nečinnosti nebo po zavření prohlížeče. Do počítače nic neukládá.
- Skeny listin a dokumenty se do GitHubu **neukládají**. V appce se jen zaznamená, že sken existuje a kde je uložený (např. sdílený disk SEA-l).
- Každá změna je commit v soukromém repozitáři se jménem toho, kdo ji udělal.

> **Důležité:** když všichni zapomenou heslo, data nejde obnovit. Správce (ty) by měl mít heslo bezpečně uložené, ideálně ve správci hesel.

## Nastavení (jednou, asi 15 minut)

### 1. Repozitář s appkou a GitHub Pages
1. Vpravo nahoře **+ → New repository** → název **`evidence-projektu`**, viditelnost **Public** (GitHub Pages zdarma vyžaduje veřejný repozitář – je v něm jen appka, žádná data), zaškrtni **Add a README file** → **Create repository**.
2. **Add file → Upload files** → přetáhni `index.html`, `README.md` a `.nojekyll` (ten může být v počítači skrytý; když ho nenajdeš, nevadí) → **Commit changes**.
3. **Settings → Pages** → *Build and deployment*: Source **Deploy from a branch**, Branch **main**, složka **/ (root)** → **Save**.
4. Za minutu nebo dvě bude appka na adrese **`https://khiempham311.github.io/evidence-projektu/`**.

### 2. Soukromý repozitář pro data
**+ → New repository** → název **`evidence-projektu-data`**, viditelnost **Private**, **nic dalšího nezaškrtávej** (repozitář může být prázdný) → **Create repository**.

### 3. Tvůj token (správce)
Avatar → **Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token**:
- *Token name:* `evidence-projektu-spravce`
- *Expiration:* např. 90 dní (zapiš si datum, před vypršením vytvoř nový a nastav ho v appce přes „Nové heslo“)
- *Repository access:* **Only select repositories** → **`evidence-projektu-data`** a **`evidence-projektu`**
- *Permissions → Repository permissions:* **Contents: Read and write**
- **Generate token**, zkopíruj (začíná `github_pat_`)

Druhý repozitář (`evidence-projektu`) token potřebuje proto, aby appka mohla zapisovat soubor s přístupy.

### 4. První spuštění
Otevři appku. Zobrazí se **První spuštění**: vyplň své jméno, token a heslo. Appka ověří, že repozitář s daty je soukromý, vytvoří šifrovací klíč, zašifrovaný prázdný `data.json` a `pristupy.json`.

### 5. Přidat další lidi
Každému vytvoř **vlastní token** stejně jako v kroku 3, jen s přístupem **pouze k `evidence-projektu-data`**. V appce pak **Nastavení → Účet a přístupy → Přidat člověka** vyplň jméno, jeho token a heslo. Heslo mu předej osobně.

Když někdo odchází: **Odebrat** v appce **a** smazat jeho token na GitHubu.

## Na co dát pozor
- **Dvoufázové ověření** (2FA) na GitHub účtu správce je nutnost: Settings → Password and authentication.
- Tokeny mají expiraci. Když vyprší, appka napíše, že GitHub token odmítl. Správce vytvoří nový a nastaví ho přes „Nové heslo“.
- Zálohu (Nastavení → Stáhnout zálohu) ukládej jen na bezpečné místo. Záloha **není** šifrovaná.
- Do veřejného repozitáře `evidence-projektu` nikdy nenahrávej zálohu ani export.
- Repozitář s daty nikdy nepřepínej na Public.
- Zpracování osobních údajů (GitHub jako zpracovatel, data dětí a rodin) by mělo být uvedené v záznamech o zpracování v SEA-l.
