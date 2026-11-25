# Specifikacija: Turnirski bodovi

## Opis

Klub društvenih igara vodi pojedinačne turnire. Organizator prijavljuje sudionike, unosi samo nenegativni cjelobrojni rezultat pobjednika i njegovu adresu novčanika te potvrđuje jednog pobjednika. Potvrđeni pobjednik, koji mora biti sudionik, dobiva točno 10 BOD. Igrač može bodove zamijeniti za nagrade ograničene zalihe. Token ERC-20 i modul turnira i nagrada zasebni su ugovori.

## Korisnici i uloge

| Uloga | Ovlasti |
| --- | --- |
| Vlasnik ugovora | Dodaje i uklanja administratore. Vlasništvo samo po sebi ne daje administratorske ovlasti. |
| Administrator / organizator | Jedna ista uloga: vodi turnire i nagrade, potvrđuje pobjednika i preuzimanje te povećava granicu bodova. |
| Igrač | Račun novčanika koji sudjeluje, dobiva bodove i može za sebe zatražiti nagradu. |
| Posjetitelj | Pregledava javne podatke. |
| Modul | Ugovor koji jedini smije od tokena zatražiti izdavanje i spaljivanje. Nije ljudska uloga. |

Vlasnik može sebi dodijeliti ulogu administratora. Isti račun može biti i igrač. Ako vlasnik izgubi ključ, vlasništvo se automatski ne oporavlja; za novog vlasnika potreban je novi ugovor. Postojeći administratori i dalje imaju svoje ovlasti, ali se njihov popis više ne može mijenjati.

Popis administratora nalazi se u tokenu; modul za svaku administratorsku radnju provjerava isti popis. Igrač ne dobiva zasebnu administratorsku ulogu zato što ima bodove.

## Odluke o tokenu

| Odluka | Pravilo |
| --- | --- |
| Naziv, oznaka, decimale | Bod; BOD; 0. Jedan token je jedan cijeli bod. |
| Izdavanje | Isključivo pri prvoj potvrdi pobjednika turnira, točno 10 BOD sudioniku tog turnira. Modul tehnički poziva token; administrator nema samostalnu funkciju za izdavanje. |
| Granica | Početno 1000 BOD. Ograničava trenutačni totalSupply, a ne broj bodova ikad izdanih. Dodjela koja bi prekoračila granicu ne uspijeva. |
| Povećanje granice | Samo administrator; nova granica mora biti strogo veća. Nema smanjenja. |
| Prenosivost | Zabranjeni su transfer i transferFrom, uključujući prijenos uz odobrenje. Standardni ERC-20 approve može postaviti allowance, ali ne omogućuje prijenos BOD. Neuspjeli transferFrom ne mijenja allowance. |
| Trošenje | Pri uspješnom zahtjevu za nagradu modul odmah spaljuje cijenu iz salda podnositelja. Spaljivanje oslobađa prostor ispod granice. Nema trošenja tuđih bodova. |
| Administratori | Samo vlasnik dodaje i uklanja administratore. Administrator ne može ukloniti vlasnika kao vlasnika. |

Da bi dva ugovora radila zajedno, vlasnik tokena jednokratno povezuje adresu već objavljenog ugovora modula. Token odbija adresu bez objavljenog koda; vlasnik prije povezivanja provjerava da je unio adresu namijenjenog modula. Prije povezivanja potvrda pobjednika ne može dodijeliti bodove. To je tehnički korak, ne dodatan način dodjele bodova.

Standardne funkcije approve i allowance ostaju dostupne: svaki račun može postaviti vlastito odobrenje, a allowance je javno čitljiv. Odobrenje ne zaobilazi zabranu transferFrom.

## Odluke o modulu

| Odluka | Pravilo |
| --- | --- |
| Turnir | Administrator otvara turnir i prijavljuje adrese sudionika do unosa rezultata. Nakon unosa rezultata nove prijave nisu dopuštene. Za rezultat unosi samo cjelobrojni rezultat pobjednika i njegovu adresu novčanika te potvrđuje jednog prijavljenog pobjednika. Dok pobjednik nije potvrđen, administrator može ispraviti rezultat ponovnim unosom koji zamjenjuje prethodni rezultat i upisanog pobjednika. Nakon potvrde rezultat se ne može mijenjati. Potvrda i dodjela 10 BOD jedna su transakcija. Ne mogu se ponoviti. |
| Nagrada | Administrator određuje naziv, cijenu u cijelim BOD i početnu zalihu u komadima. Cijena mora biti najmanje 1 BOD, a početna zaliha najmanje 1 komad. Cijena i početna zaliha nakon objave su fiksne. |
| Zahtjev | Igrač za sebe može zatražiti istu nagradu više puta ako svaki put ima dovoljno BOD i nagrada je na zalihi. Svaki uspješan zahtjev dobiva novi ID, jednom spaljuje cijenu i odmah smanjuje zalihu za jedan. Neuspjeh ništa ne mijenja. |
| Preuzimanje | Administrator izvan lanca predaje nagradu i jednom označava konkretan zahtjev preuzetim. Oznaka je njegova evidencija; ugovor ne dokazuje da je fizička predaja nastupila. Na lancu se bilježi samo oznaka preuzimanja, bez detalja fizičke predaje. Ta potvrda ne troši dodatne bodove i ne mijenja zalihu. |
| Poništenje | Nema otkazivanja zahtjeva ni povrata bodova. |

Rezultat pobjednika je `uint256`: dopuštena je nula i nema dodatne gornje granice izvan raspona tog tipa. Negativan broj ili broj veći od `uint256` nije valjan ulaz za tu funkciju. Unos rezultata prepoznaje se po nenultoj adresi upisanog pobjednika, a ne po vrijednosti rezultata, jer je rezultat 0 dopušten.

Administrator je odgovoran za istinitost unesenih podataka o turniru. Ugovor provjerava prijavu pobjednika, ovlasti i jednokratnu dodjelu bodova, ali ne može potvrditi da se turnir stvarno održao. Za potvrdu pobjednika dovoljan je jedan administrator; isti račun može biti i administrator i igrač.

## Funkcije

Nazivi funkcija i tekstovi navedenih poruka grešaka u tablici potvrđeni su. Preostale Solidity tipove i događaje utvrdit ćemo prije sučelja u Etapi 3. Sve poruke pogrešaka koje će ući u ugovor napisane su bez dijakritike.

| Funkcija | Tko i što radi | Kada se odbija; točna poruka |
| --- | --- | --- |
| postaviAdministratora(adresa, aktivan) | Vlasnik dodaje ili uklanja administratora. | Nije vlasnik: "Nisi vlasnik"; nulta adresa: "Neispravna adresa". |
| povecajGranicu(novaGranica) | Administrator povećava granicu totalSupply. | Nije administrator: "Nisi administrator"; vrijednost nije veća: "Granica mora biti veca". |
| postaviModul(adresa) | Vlasnik jednom povezuje nenultu adresu već objavljenog ugovora modula. | Nije vlasnik: "Nisi vlasnik"; nulta adresa: "Neispravna adresa"; adresa nema objavljeni kod: "Modul mora biti ugovor"; već povezano: "Modul je vec postavljen". |
| dodijeliPobjedniku(adresa) | Samo povezani modul izdaje točno 10 BOD tijekom potvrde pobjednika. | Modul nije povezan: "Modul nije postavljen"; nije povezani modul: "Nisi modul"; prekoračuje granicu: "Granica bodova je dosegnuta". |
| potrosiZaNagradu(adresa, cijena) | Samo modul spaljuje bodove podnositelja uspješnog zahtjeva. | Nije modul: "Nisi modul"; nedostaje bodova: "Nedovoljno bodova". |
| transfer / transferFrom | Nitko ne može prenositi BOD. | "Prijenos bodova nije dozvoljen". |
| otvoriTurnir(naziv) | Administrator stvara novi turnir s ID-em. | Nije administrator: "Nisi administrator". |
| prijaviSudionika(turnirId, adresa) | Administrator dodaje adresu jednom u turnir, samo prije prvog unosa rezultata. | Turnir ne postoji: "Turnir ne postoji"; nije administrator: "Nisi administrator"; nulta adresa: "Neispravna adresa"; već prijavljen: "Sudionik je vec prijavljen"; rezultat je već unesen, a pobjednik nije potvrđen: "Rezultat je vec unesen"; već potvrđen pobjednik: "Pobjednik je vec potvrdjen". |
| unesiRezultat(turnirId, pobjednik, rezultat) | Administrator bilježi samo rezultat pobjednika tipa `uint256` i njegovu adresu novčanika prije potvrde. Ponovni unos prije potvrde zamjenjuje prethodni rezultat i upisanog pobjednika. | Turnir ne postoji: "Turnir ne postoji"; nije administrator: "Nisi administrator"; pobjednik nije sudionik: "Pobjednik nije sudionik"; već potvrđen: "Pobjednik je vec potvrdjen". |
| potvrdiPobjednika(turnirId) | Administrator potvrđuje upisanog pobjednika; istom transakcijom modul mu dodjeljuje 10 BOD. | Turnir ne postoji: "Turnir ne postoji"; nije administrator: "Nisi administrator"; rezultat nedostaje: "Rezultat nije unesen"; već potvrđen: "Pobjednik je vec potvrdjen"; modul nije povezan: "Modul nije postavljen"; nema mjesta ispod granice: "Granica bodova je dosegnuta". |
| dodajNagradu(naziv, cijena, zaliha) | Administrator stvara nagradu s novim ID-em. | Nije administrator: "Nisi administrator"; cijena nula: "Cijena mora biti veca od nule"; zaliha nula: "Zaliha mora biti veca od nule". |
| zatraziNagradu(nagradaId) | Igrač poziva za sebe: spaljuje cijenu, umanjuje zalihu za jedan i stvara jedan zahtjev s novim ID-em u jednoj transakciji. | Nagrada ne postoji: "Nagrada ne postoji"; zaliha nula: "Nagrada nije dostupna"; nema dovoljno bodova: "Nedovoljno bodova". |
| potvrdiPreuzimanje(zahtjevId) | Administrator jednom označava konkretan zahtjev preuzetim. | Zahtjev ne postoji: "Zahtjev ne postoji"; nije administrator: "Nisi administrator"; već preuzet: "Nagrada je vec preuzeta". |

## Matrica ovlasti

"Da" uz uvjet znači da uz ulogu moraju vrijediti i ostala pravila. Uloge se mogu preklapati; vlasnik koji je ujedno administrator ima obje skupine ovlasti. Standardni approve dostupan je svakom računu za vlastiti allowance, uključujući račun bez posebne uloge; ne daje pravo na prijenos BOD.

| Radnja | Vlasnik | Administrator | Igrač | Posjetitelj | Modul |
| --- | --- | --- | --- | --- | --- |
| Dodati/ukloniti administratora | Da | Ne | Ne | Ne | Ne |
| Povećati granicu | Ne | Da | Ne | Ne | Ne |
| Povezati modul | Da | Ne | Ne | Ne | Ne |
| Izdati 10 BOD / spaliti za zahtjev | Ne | Ne | Ne | Ne | Da, u okviru radnje modula |
| Voditi turnir i potvrditi pobjednika | Ne | Da | Ne | Ne | Ne |
| Dodati nagradu / potvrditi preuzimanje | Ne | Da | Ne | Ne | Ne |
| Zatražiti nagradu za sebe | Ne | Da, ako ima BOD | Da, ako ima BOD | Ne | Ne |
| Postaviti vlastiti allowance (approve) | Da | Da | Da | Da | Da |
| transfer / transferFrom | Ne | Ne | Ne | Ne | Ne |
| Čitati javne podatke | Da | Da | Da | Da | Da |

## Model podataka na lancu

- Token: saldo po adresi, totalSupply, granica, vlasnik, ovlasti administratora i adresa povezanog modula.
- Turnir: jedinstveni ID, naziv bez osobnih podataka, adrese sudionika, rezultat pobjednika tipa `uint256`, njegova adresa novčanika i potvrda. Nenulta adresa upisanog pobjednika označava da je rezultat unesen. Stanje, događaji i javne metode čitanja ne izlažu dodatne podatke o rezultatu; ID samo označava turnir.
- Nagrada: jedinstveni ID, naziv, fiksna cijena, početna i preostala zaliha.
- Zahtjev: jedinstveni ID, adresa podnositelja, ID nagrade i oznaka preuzimanja. Za istog igrača i istu nagradu mogu postojati različiti ID-evi.

## Što se NE sprema na blockchain

Imena, kontaktni i drugi osobni podaci igrača, mjesto, način i podaci o osobi pri fizičkoj predaji nagrade, privatni ključevi, fotografije, tekstualni opisi rezultata i rezultati ostalih sudionika. Na lancu ostaje samo oznaka preuzimanja konkretnog zahtjeva. Adrese novčanika i stanje turnira i nagrada na lancu su javni. Nazivi turnira i nagrada slobodan su tekst; administrator prije objave provjerava da ne sadrže osobne podatke jer ugovor to ne može prepoznati. Fizička predaja dogovara se izvan lanca.

## Kriteriji prihvaćanja

- **K1:** Zadano je token upravo objavljen, kada se čitaju naziv, oznaka, decimale, granica i totalSupply, tada su vrijednosti Bod, BOD, 0, 1000 i 0.
- **K2:** Zadano vlasnik ima pristup, kada adresu doda kao administratora pa je ukloni, tada adresa prvo dobiva pa gubi administratorske ovlasti.
- **K3 (neuspjeh):** Zadano račun nije vlasnik, kada pozove postaviAdministratora, tada transakcija pada s "Nisi vlasnik" i ovlasti se ne mijenjaju.
- **K4:** Zadano granica je 1000, kada administrator pozove povecajGranicu(1200), tada granica postaje 1200, a totalSupply ostaje isti.
- **K5 (neuspjeh):** Zadano račun nije administrator i granica je 1000, kada pozove povecajGranicu(1200), tada transakcija pada s "Nisi administrator" i granica ostaje 1000.
- **K6 (neuspjeh):** Zadano granica je 1000, kada administrator pokuša postaviti 1000 ili manje, tada transakcija pada s "Granica mora biti veca" i granica se ne mijenja.
- **K7:** Zadano modul nije povezan, kada ga vlasnik poveže, tada samo ta adresa može pozvati izdavanje i spaljivanje.
- **K8 (neuspjeh):** Zadano je modul povezan, a pozivatelj nije njegova adresa, kada pozove dodijeliPobjedniku, tada transakcija pada s "Nisi modul", a saldo i totalSupply se ne mijenjaju.
- **K9:** Zadano administrator otvori turnir i prijavi adresu igrača, kada se očitaju podaci turnira, tada novi ID postoji i adresa je sudionik.
- **K10 (neuspjeh):** Zadano je adresa već sudionik turnira, kada je administrator prijavi ponovno, tada transakcija pada s "Sudionik je vec prijavljen" i popis se ne mijenja.
- **K11 (neuspjeh):** Zadano adresa pobjednika nije prijavljena, kada administrator pokuša unijeti rezultat s njom, tada transakcija pada s "Pobjednik nije sudionik" i rezultat nije zabilježen.
- **K12:** Zadano je rezultat unesen za prijavljenog pobjednika, modul je povezan, turnir nije potvrđen, totalSupply je 0 i granica 1000, kada administrator potvrdi pobjednika, tada on dobiva 10 BOD, turnir je potvrđen i totalSupply je 10.
- **K13 (neuspjeh):** Zadano je pobjednik već potvrđen i dobio 10 BOD, kada administrator ponovi potvrdu, tada transakcija pada s "Pobjednik je vec potvrdjen" i saldo i totalSupply ostaju isti.
- **K14 (neuspjeh):** Zadano je totalSupply 995, granica 1000 i turnir spreman za potvrdu, kada administrator potvrdi pobjednika, tada transakcija pada s "Granica bodova je dosegnuta", turnir ostaje nepotvrđen, a saldo i totalSupply isti.
- **K15 (neuspjeh):** Zadano igrač ima 10 BOD, kada pozove transfer prema drugoj adresi, tada transakcija pada s "Prijenos bodova nije dozvoljen" i oba salda ostaju ista.
- **K16 (neuspjeh):** Zadano igrač ima 10 BOD i allowance prema drugoj adresi je 0, kada igrač pozove approve za 10 BOD pa druga adresa pozove transferFrom za 10 BOD, tada approve uspijeva i allowance postaje 10, a transferFrom pada s "Prijenos bodova nije dozvoljen"; oba salda i allowance 10 ostaju nepromijenjeni nakon odbijenog prijenosa.
- **K17:** Zadano administrator doda nagradu s cijenom 6 BOD i zalihom 2, kada je pregleda pri objavi, igrač s dovoljno BOD zatraži jedan komad pa administrator potvrdi preuzimanje tog zahtjeva, tada su pri svakom očitanju cijena 6 BOD i početna zaliha 2, dok se preostala zaliha nakon zahtjeva smanjuje s 2 na 1 i nakon potvrde ostaje 1.
- **K18 (neuspjeh):** Zadano administrator stvara nagradu s početnom zalihom 1, kada unese cijenu 0 BOD, tada transakcija pada s "Cijena mora biti veca od nule" i nagrada ne nastaje.
- **K19:** Zadano igrač ima 10 BOD, a nagrada košta 6 BOD i ima zalihu 2, kada je zatraži, tada saldo postaje 4, totalSupply se smanjuje za 6, zaliha postaje 1 i nastaje točno jedan novi zahtjev za njega.
- **K20:** Zadano isti igrač ponovno ima dovoljno BOD i istoj nagradi preostaje jedan komad, kada je ponovno zatraži, tada nastaje drugi zahtjev s drugim ID-em, cijena se ponovno spaljuje samo jednom i zaliha pada na 0.
- **K21 (neuspjeh):** Zadano nagrada košta 6 BOD, zaliha je 1, a igrač ima 4 BOD, kada je zatraži, tada transakcija pada s "Nedovoljno bodova", a saldo, totalSupply, zaliha i broj zahtjeva ostaju isti.
- **K22 (neuspjeh):** Zadano nagrada ima zalihu 0 i igrač ima dovoljno BOD, kada je zatraži, tada transakcija pada s "Nagrada nije dostupna", a saldo, totalSupply, zaliha i broj zahtjeva ostaju isti.
- **K23:** Zadano zahtjev nije preuzet, kada ga administrator označi preuzetim, tada samo taj zahtjev mijenja status, saldo, totalSupply i zaliha ostaju isti, a stanje i događaji ne sadrže mjesto, način ni osobne podatke o fizičkoj predaji.
- **K24 (neuspjeh):** Zadano je zahtjev već označen preuzetim, kada administrator ponovi potvrdu, tada transakcija pada s "Nagrada je vec preuzeta" i stanje je nepromijenjeno.
- **K25 (neuspjeh):** Zadano igrač zna ID tuđeg zahtjeva, kada pozove potvrdiPreuzimanje, tada transakcija pada s "Nisi administrator" i zahtjev ostaje nepreuzet.
- **K26:** Zadano turnir ima dva prijavljena sudionika, za prvog je unesen cjelobrojni rezultat 3, a pobjednik nije potvrđen, kada administrator ponovno unese rezultat 4 s drugim prijavljenim pobjednikom, tada su na lancu rezultat 4 i adresa drugog pobjednika umjesto prethodnih vrijednosti, a bodovi još nisu dodijeljeni.
- **K27 (neuspjeh):** Zadano pobjednik je potvrđen i rezultat je upisan, kada administrator pokuša ponovno unijeti rezultat, tada transakcija pada s "Pobjednik je vec potvrdjen", a rezultat, pobjednik i saldo ostaju nepromijenjeni.
- **K28 (neuspjeh):** Zadano je rezultat turnira unesen, pobjednik još nije potvrđen, a nova adresa nije prijavljena, kada administrator pokuša prijaviti tu adresu, tada transakcija pada s "Rezultat je vec unesen" i popis sudionika ostaje nepromijenjen.
- **K29:** Zadano je turnir otvoren, adresa igrača prijavljena, a rezultat još nije unesen, kada administrator unese cjelobrojni rezultat 3 i tu adresu kao pobjednika, tada se u stanju ugovora, događajima i javnim metodama čitanja kao podaci o rezultatu pojavljuju samo broj 3 i adresa tog igrača, uz ID koji označava turnir; nema rezultata drugih sudionika ni slobodnog teksta, a BOD se ne dodjeljuje prije potvrde.
- **K30 (neuspjeh):** Zadano vlasnik je već povezao adresu modula, kada pokuša povezati drugu adresu modula, tada transakcija pada s "Modul je vec postavljen" i povezana adresa ostaje ista.
- **K31 (neuspjeh):** Zadano vlasnik je uklonio adresu s jedinog popisa administratora u tokenu, kada ta adresa pokuša pozvati otvoriTurnir u modulu, tada transakcija pada s "Nisi administrator" i novi turnir ne nastaje.
- **K32 (neuspjeh):** Zadano administrator stvara nagradu s cijenom 1 BOD, kada unese početnu zalihu 0, tada transakcija pada s "Zaliha mora biti veca od nule" i nagrada ne nastaje.
- **K33 (neuspjeh):** Zadano token nema povezan modul, kada vlasnik pokuša povezati adresu običnog novčanika bez objavljenog koda, tada transakcija pada s "Modul mora biti ugovor" i adresa modula ostaje nepovezana.
- **K34:** Zadano je turnir otvoren, igrač prijavljen, modul povezan i rezultat nije unesen, kada administrator upiše rezultat 0 s adresom tog igrača pa potvrdi pobjednika, tada upisani rezultat ostaje 0, turnir je potvrđen i igrač dobiva točno 10 BOD.
- **K35:** Zadano je turnir otvoren, igrač prijavljen i rezultat nije unesen, kada administrator upiše najveću vrijednost tipa `uint256` kao rezultat pobjednika, tada se ta vrijednost i adresa igrača pohranjuju bez promjene salda BOD prije potvrde.
- **K36 (neuspjeh):** Zadano je administrator otvorio turnir i prijavio sudionika, ali rezultat nije unesen, kada pozove potvrdiPobjednika, tada transakcija pada s "Rezultat nije unesen", turnir ostaje nepotvrđen, a rezultat, saldo i totalSupply ostaju nepromijenjeni.
- **K37 (neuspjeh):** Zadano je pobjednik turnira potvrđen, kada administrator pokuša prijaviti novog sudionika, tada transakcija pada s "Pobjednik je vec potvrdjen", a sudionici, rezultat, pobjednik, saldo i totalSupply ostaju nepromijenjeni.
- **K38 (neuspjeh):** Zadano ID turnira ne postoji, kada administrator zasebno pozove prijaviSudionika, unesiRezultat i potvrdiPobjednika s tim ID-em, tada svaki poziv pada s "Turnir ne postoji", a postojeći turniri, salda i totalSupply ostaju nepromijenjeni.
- **K39 (neuspjeh):** Zadano je rezultat unesen za prijavljenog pobjednika, a modul još nije povezan s tokenom, kada administrator pozove potvrdiPobjednika, tada transakcija pada s "Modul nije postavljen", turnir ostaje nepotvrđen, a saldo pobjednika i totalSupply ostaju nepromijenjeni.
- **K40:** Zadano je granica 1000 BOD, totalSupply je 1000, modul je povezan, turnir je spreman za potvrdu, a igrač ima najmanje 10 BOD i dostupna je nagrada cijene 10 BOD, kada administrator pokuša potvrditi pobjednika, zatim igrač uspješno zatraži nagradu pa administrator ponovi potvrdu istog turnira, tada prvi pokušaj pada s "Granica bodova je dosegnuta" bez potvrde turnira i bez promjene salda ili totalSupply; zahtjev spaljuje 10 BOD i smanjuje totalSupply na 990; ponovljena potvrda uspijeva, pobjednik dobiva točno 10 BOD i totalSupply se vraća na 1000.
- **K41:** Zadano je preostala zaliha nagrade 1, a dva igrača imaju dovoljno BOD i svaki pošalje zahtjev za tu nagradu, kada se njihove transakcije obrade, tada točno jedan zahtjev uspijeva, zaliha postaje 0, cijena se spaljuje samo uspješnom podnositelju i nastaje jedan novi ID zahtjeva; drugi poziv pada s "Nagrada nije dostupna" bez spaljivanja bodova drugog igrača i bez dodatnog ID-a.

## Izvan opsega

Sezone, ligaška ljestvica, poništavanje zahtjeva i povrat bodova, prijenos ili prodaja bodova, proizvoljno izdavanje bodova, uređivanje objavljene cijene ili početne zalihe nagrade, automatski oporavak vlasničkog ključa, osobni podaci na lancu, dokaz stvarnog održavanja turnira, potvrda dvaju administratora, dodatna potvrda igrača pri preuzimanju nagrade i katalog unaprijed odobrenih naziva.

## Otvorena pitanja

Nema otvorenih pitanja o pravilima Etape 2.

## Sljedivost

Tablica Funkcija | Kriteriji popunjava se u Etapi 3 pri definiranju sučelja.
