# AI dnevnik

## T1 — Postavljanje projekta (22.11.2026.)
- Cilj termina: Odabrati nišu i modul te pripremiti alate i repozitorij za projekt.
- Niša: Klub društvenih igara koji organizira turnire, pobjednicima dodjeljuje bodove i omogućuje igračima zamjenu bodova za nagrade.
- Modul: Nagradna trgovina.
- Najkorisniji prompt (kopiran u cijelosti): Želim napraviti projekt za klub društvenih igara. Organizator bi vodio turnire i nakon potvrde rezultata pobjedniku dodijelio 10 bodova, a igrači bi bodove mogli zamijeniti za nagrade. Pogledaj pravila u AGENTS.md pa mi pomogni razraditi kako se dodjeljuju bodovi, kako aplikacija treba raditi i koje tehnologije trebamo koristiti (Vue, Solidity i Sepolia). Pitaj me jedno po jedno o pravilima koja još nismo odlučili. Prije promjena predloži kratak plan, a nakon svakog koraka reci što je napravljeno i provjereno, te što slijedi. Commit napravi tek kada ti zadam opis.
- Što je AI napravio dobro: Postavio je pet pitanja jedno po jedno i sažeo uloge organizatora i igrača, te pravila za dodjelu bodova i zamjenu za nagrade.
- Što je AI napravio drugačije nego što sam očekivao: U ranijem planiranju predložio je sezone, ligašku ljestvicu, te otkazivanje zahtjeva s povratom bodova. Nisam prihvatio sezone jer su za projekt dovoljni pojedinačni turniri, ljestvicu jer nije potrebna za zamjenu bodova za nagrade, te otkazivanje i povrat jer bi proširili opseg projekta.
- Što sam dodao u AGENTS.md: Naziv, zajednicu i opis projekta i zabilješke o stvarnim AI pogreškama i načinu vođenja dnevnika.
- Provjera: U projektnoj okolini provjereni su `node -v` (`v24.22.3`) i `git --version` (`2.51.2.windows.1`), instalirana je ekstenzija Vue – Official, a repozitorij je povezan s granom `main` na GitHubu.
- Commit: `eac59c3` — `E1 - priprema okoline i opis projekta` (poslan na `main`).

## T1 — Sepolia transakcija i dopuna dnevnika (23.11.2026.)
  - Poslao sam 0,001 Sepolia ETH kolegi i provjerio potvrđenu transakciju na Etherscanu.
  - Moja adresa: `0x63bDe20a078204dE940F29Df303E66E91c2A69a0`
  - Adresa primatelja: `0xb9B45187EA815BBbFa348d93BFB5FE8B33E4CB74`
  - Hash: [`0xa84fe6165f64581a078b27d8c00c2489092e1a95a15eaa05b47ac6de780ee744`](https://sepolia.etherscan.io/tx/0xa84fe6165f64581a078b27d8c00c2489092e1a95a15eaa05b47ac6de780ee744)
  - Broj bloka: `12197432`
  - Nonce: `0`
  - Potrošeni gas: `204600`
  - Cijena gasa: `1,500000017 Gwei`
  - Naknada: `0,0003069000034782 ETH`
  - Provjera naknade: `204600 × 1,500000017 Gwei = 0,0003069000034782 ETH`.
- Commit: 2. commit — `Sepolia transakcija i dopuna dnevnika` (poslan na `main`).

## T2 — AI intervju i prva specifikacija (24.11.2026.)
- **Što sam tražio:** Zamolio sam AI da mi pomogne razraditi specifikaciju tokena BOD i nagradne trgovine, prema uputama za Etapu 2, te da otvorena pitanja postavlja jedno po jedno.
- **Što je AI vratio:** Dopunio je `docs/SPEC.md` pravilima za turnire, bodove i nagrade, matricom ovlasti i kriterijima prihvaćanja.
- **Što sam provjerio ili ispravio:** Odlučio sam da administrator može ispraviti rezultat samo prije potvrde pobjednika, da se prijave zatvaraju unosom rezultata i da se za rezultat spremaju samo cijeli broj i adresa pobjednika. Potvrdio sam i pravila o modulu, administratorima, cijeni i zalihi nagrada. Iz nacrta smo uklonili dodatno spremanje spaljene cijene uz zahtjev i posebno ponašanje pri čitanju nepostojećih ID-eva jer to nisam tražio. AI je provjerio da SPEC ima 32 kriterija, od toga 20 neuspješnih, te matricu bez praznih ćelija.
- **Što sam naučio:** Shvatio sam da i male odluke, poput trenutka kada se zatvaraju prijave, trebaju biti zapisane prije pisanja ugovora. Inače bi AI pri izradi koda morao nagađati.
