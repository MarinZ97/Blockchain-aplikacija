# Pravila projekta

## Projekt
- Naziv: Turnirski bodovi
- Zajednica: klub društvenih igara koji organizira turnire
- Opis: Aplikacija omogućuje organizatorima unos rezultata turnira i dodjelu bodova pobjednicima, a igračima zamjenu bodova za nagrade.

## Stack (koristi točno ove verzije)
- Frontend: Vue 3 (script setup), Pinia, Vite, u mapi frontend/
- Pametni ugovori (od T4): Solidity ^0.8.24, OpenZeppelin Contracts 5.x,
  Hardhat 3 (ESM, TypeScript), testovi: Mocha + ethers v6
- Mreža: Sepolia (chainId 11155111)

## Pravila rada
- Prije pisanja koda predloži kratak plan i pričekaj potvrdu.
- Mijenjaj samo datoteke potrebne za zadatak.
- Ne dodaj nove biblioteke bez pitanja.
- Svaka nova funkcija ugovora mora imati test, uključujući neuspješan slučaj.
- Za slanje ETH-a koristi call s provjerom, nikad transfer.
- Poruke u require piši bez dijakritike.
- Nikad ne traži, ne ispisuj i ne spremaj privatne ključeve.
- Na kraju ukratko objasni što si promijenio i zašto.

## Dnevnik AI-ja
- Vodi se u docs/DNEVNIK.md kroz projekt i mora imati najmanje 8 zapisa.
- Zadrži postojeći T1 predložak u docs/DNEVNIK.md. Pri popunjavanju obuhvati: što si tražio, što je AI vratio, što si provjerio ili ispravio i što si naučio.
- Zapisuj stvarne provjere i pogreške AI-ja. Student provjerava činjenice i piše dnevnik svojim riječima.

## Greške koje je AI već radio u ovom projektu
- AI je u početnom planu predložio sezone, ljestvicu i dodatne postupke prije provjere željenog opsega. Drži se dogovorenog opsega i postavi pitanje kada pravila nisu jasna.
- AI je zamijenio postojeći T1 predložak dnevnika novim poljima. Ne briši zadane rubrike; četiri elementa iz uputa uklopi u postojeći predložak.
