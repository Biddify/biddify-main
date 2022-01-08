<div align="center">
    <h1>Security research</h1>
</div>

<div align="center">
    <h2>Bcrypt data encryption</h2>
</div>

<div align="center">
    <img width="500px" height="100%" src="https://triple-p.nl/wp-content/uploads/2020/10/shutterstock_619615334-1-1024x731.jpg">
</div>

## Wat is Bcrypt?
Bcrypt is ontworpen door [Niels Provos](https://twitter.com/NielsProvos) en David Mazières [David Mazières](https://twitter.com/dmazieres)
en is gebaseerd op [Blowfish](https://en.wikipedia.org/wiki/Blowfish_(cipher)). De B staat voor Blowfish en crypt voor de naam van de hashing functie gebruikt door het UNIX-wachtwoord systeem.

Crypt is een goed voorbeeld van het niet aanpassen aan technologische veranderingen.
Volgens [USENIX](https://www.usenix.org/legacy/events/usenix99/provos/provos_html/node1.html) kon crypt in 1976 minder dan 4 wachtwoorden per seconden hashen. 
Voor hackers om een hash te inverten moeten ze eerst de [pre-image](https://en.wikipedia.org/wiki/Preimage_attack) vinden. 
Dit maakte het UNIX-team comfortabel over de kracht van crypt. 
Echter, 20 jaar later, kan een snelle computer met geoptimaliseerde software en hardware 200.000 wachtwoorden per seconden hashen met deze functie.

Inherent zou een hacker een [dictionary attack](https://www.techtarget.com/searchsecurity/definition/dictionary-attack) kunnen uitvoeren met extreme efficiëntie. 
Dus moest er een cryptografie komen die exponentieel moeilijker te doorbreken is naarmate de hardware sneller wordt.

## Hoe werkt Bcrypt?
Provos en Mazières ontwikkelde met het al bestaande Blowfish, een nieuw sleutelconfiguratie-algoritme genaamd "eksblowfish", 
wat staat voor "expensive key schedule Blowfish". 

De functie `EksBlowfishSetup` wordt ingesteld met een cost, salt en het wachtwoord. 
Dit om de state van `eksblowfish` te initialiseren. Vervolgens besteedt Bcrypt veel tijd aan het uitvoeren van een sleutelschema dat bestaat uit het uitvoeren van een sleutelafleiding waarbij een reeks subsleutels afleiden van een primaire sleutel. 
Hierbij wordt het wachtwoord gebruikt als de primaire sleutel. 

Als de gebruiker een slecht of kort wachtwoord gekozen heeft rekken we dat uit tot een langer wachtwoord/sleutel. 
Het bovengenoemde staat ook bekend als [key stretching](https://en.wikipedia.org/wiki/Key_stretching). 

Een 192-bits "magic value" `OrpheanBeholderScryDoubt` wordt 64 keer versleuteld met `eksblowfish` in ECB-modus, en samengevoegd met de uitkomst van de vorige fase. 
De uitkomst van deze fase is de cost en een 128-bit salt waarde samen met het resultaat van de encryptie loop. 

```node
bcrypt(cost, salt, pwd)
    state <- EksBlowfishSetup(cost, salt, pwd)
    ctext <- "OrpheanBeholderScryDoubt"
    repeat(64)
        ctext <- EncryptECB(state, ctext)
    return Concatenate(cost, salt, ctext)
```

De resulterende hash start altijd met `$2a$`, ` $2y$` of ` $2b$`. 
Deze voorvoegsels worden toegevoegd om het gebruik van Bcrypt en de gebruikte versie aan te geven. 

```
$2b$15$Wx.kUJY72dw5fkMxMm4R5ejJ7NReyF7iB3A0mZtfX6BpvH0UXi9m6
\__/\__/\____________________/\____________________________/
 Id Cost        Salt                      Hash
```

In 1999 starte de originele Bcrypt hash met `$2$`. 
Hierna kwam versie `$2a$` hierin zaten nieuwe functies zoals hoe een non-ASCII character te verwerken en hoe een null terminator afgehandeld moet worden. 
In 2011 kwamen de versies `$2x$` en `$2y$`. In juni van dit jaar werd een bug ontdekt in `crypt_blowfish`, dit is een PHP-implementatie van Bcrypt. 
Alle systeembeheerders werd aangeraden hun bestaande wachtwoord databases te updaten en `$2a$` te vervangen met `$2x$`, om aan te geven dat die hashes slecht zijn (en het oude kapotte algoritme moeten gebruiken). 
Alle nieuwe hashen van `crypt_blowfish ` starten voortaan met `$2y$`.
In 2014 is de meeste recente versie van Bcrypt uitgekomen namelijk versie `$2b$`. Er was namelijk een bug ontdekt in de OpenBSD-implementatie van Bcrypt.

## Waarom is Bcrypt veilig? 
Om Bcrypt veilig te houden is het aan de software engineer om te bepalen welke `cost` te kiezen. 
De `cost` staat ook wel bekend als de work factor. 
OWASP beveelt als vuistregel voor het instellen van de werkfactor aan om de kosten zo af te stemmen dat de functie zo langzaam mogelijk werkt zonder de gebruikerservaring te beïnvloeden en zonder de noodzaak om extra hardware te gebruiken die mogelijk boven het budget ligt.

Laten we de OWASP aanbevelingen beter bekijken:
- Voer UX-onderzoek uit om te achterhalen wat acceptabele wachttijden voor uw gebruikers zijn voor registratie en authenticatie.
- Als de geaccepteerde wachttijd 1 seconde is, stem dan de kosten van bcrypt af zodat deze binnen 1 seconde op uw hardware wordt uitgevoerd.
- Analyseer of de rekentijd voldoende is om aanvallen af te zwakken en te vertragen.

Gebruikers zijn meestal tevreden met een wachttijd van 1 tot 2 seconden. Zolang zij zichzelf niet constant hoeven te authenticeren. 
Het proces wordt nog steeds als snel ervaren. Terwijl deze vertraging de inspanningen van een aanvaller zullen frustreren om snel een regenboogtabel te berekenen.

Doordat we de `cost` van Bcrypt kunnen afstemmen kunnen we schalen op nieuwe hardware optimalisatie.  
Volgens een definitie van Moore's Law verdubbelt het aantal transistors per vierkante inch op geïntegreerde systemen ongeveer elke 18 maanden. 
Elke 2 jaar kunnen we standaard de `cost` verhogen om aan elke verandering tegemoet te komen.
Je moet hier voorzichtig mee zijn, want als we gewoon de werkfactor van Bcrypt in onze code verhogen, wordt iedereen buitengesloten.
Een migratieproces is in dit geval noodzakelijk.

Hieronder volgt een voorbeeld van hoe de `cost` de werktijd verhoogt.
Het voorbeeld is een Node.js script dat de hash berekend van `Wachtwoord12345` gebruik makende van de `cost` 10 tot en met 20. 

```node
const bcrypt = require("bcrypt");
const password = "Wachtwoord12345";

for (let saltRounds = 10; saltRounds < 21; saltRounds++) {
  console.time(`cost: ${saltRounds}, hash time`);
  bcrypt.hashSync(password, saltRounds);
  console.timeEnd(`cost: ${saltRounds}, hash time`);
}
```

Specificaties van mijn computer:
- Processor: 2.8 (3.8 turbo) GHz [Intel Core I7 7700HQ](https://ark.intel.com/content/www/us/en/ark/products/97185/intel-core-i77700hq-processor-6m-cache-up-to-3-80-ghz.html)
- Memory: 16 GB 2400 MHz DDR4
- Grafisch: [NVIDIA GeForce GTX 1050ti](https://www.nvidia.com/en-gb/geforce/graphics-cards/geforce-gtx-1050-ti/specifications/) 4.096 MB, [Intel HD Graphics 630](https://ark.intel.com/content/www/us/en/ark/products/97185/intel-core-i77700hq-processor-6m-cache-up-to-3-80-ghz.html)
- Besturingsysteem: Windows 10, build 19044.1415

Dit zijn de resultaten:
```
cost: 10, hash time: 68.948ms
cost: 11, hash time: 141.034ms
cost: 12, hash time: 283.87ms
cost: 13, hash time: 577.135ms
cost: 14, hash time: 1.127s
cost: 15, hash time: 2.300s
cost: 16, hash time: 4.546s
cost: 17, hash time: 9.063s
cost: 18, hash time: 18.131s
cost: 19, hash time: 37.101s
cost: 20, hash time: 74.413s
```

Als we de data plotten in een Wolfram Alpha diagram, zien we dat de tijd om een wachtwoord te hashen exponentieel groeit als de `cost` verhoogt wordt met de hierboven genoemde hardware specificaties. 

![https://www.wolframalpha.com/input/?i2d=true&i=exponential+fit+68.948%5C%2844%29+141.034%5C%2844%29+283.87%5C%2844%29+577.135%5C%2844%29+1127%5C%2844%29+2300%5C%2844%29+4546%5C%2844%29+9063%5C%2844%29+18131%5C%2844%29+37101%5C%2844%29+74413](https://i.ibb.co/Kjvrcj4/Knipsel.png)

Voor deze dataset geeft Wolfram Alpha de best passende vergelijking met de kleinste kwadraten:
```
33.3494 e^(0.700986x)
```

Als we willen voorspellen hoe lang het duurt om een wachtwoord te hashen als we de `cost` op `30` zetten, kunnen we eenvoudig deze waarde voor `x` invullen. 
````
33.3494 e^(0.700986(30)) = 45302125152.6
````

Met een `cost` van `30` zou het `45302125152.6` milliseconden duren om de hash te berekenen. Dit komt overeen met `755035.42` minuten of `524.33` dagen! 
Een veel snellere machine die geoptimaliseerd is met de nieuwste en beste technologie die tegenwoordig beschikbaar is, zou kortere rekentijden kunnen hebben. 
Gelukkig kunnen we met Bcrypt makkelijk schalen om snellere hardware tegemoet te komen. 

Als een bedrijf ontdekt of vermoedt dat er een datalek van wachtwoorden heeft plaatsgevonden, ook in de hash vorm, moet het zijn gebruikers vragen om meteen hun wachtwoord te wijzigen.
Met hashing en slating kan een brute force aanval succesvol voorkomen worden, is een enkele wachtwoordkraak is rekenkundig zeker haalbaar. 
Een aanvaller kan, met een enorme hoeveelheid rekenkracht, of door puur geluk, een enkel wachtwoord kraken, maar zelfs dan zou het proces zeer zeker traag zijn vanwege de kenmerken van bcrypt, 
waardoor het bedrijf en zijn gebruikers kostbare tijd krijgen om hun wachtwoorden te wijzigen.

## Hoe implementeer je Bcrypt?
Hieronder een korte omschrijving over hoe de implementatie van Bcrypt met gebruik van Node.js in zijn werk gaat. 

`Node.bcrypt.js` wordt geïnstalleerd via `npm` (Node.js package manager), met het volgende command: 

```
npm install bcrypt
```

Hierna, in de `app.js` creëren we een set van variabelen hiernaar verwijzen we tijdens de implementatie.

```node
// app.js
const bcrypt = require("bcrypt");
const saltRounds = 15;
const password = "Wachtwoord12345";
```

`Bcrypt` geeft ons toegang tot een Node.js bibliotheek met hulpprogramma's om het hash-proces te vergemakkelijken. 
`saltRounds` geeft de cost aan. In het voorbeeld gebruiken we `Wachtwoord12345` als variable `password`. 

```node
//app.js
const bcrypt = require("bcrypt");
const saltRounds = 15;
const password = "Wachtwoord12345";

bcrypt
  .genSalt(saltRounds)
  .then(salt => {
    console.log(`Salt: ${salt}`);
    return bcrypt.hash(password, salt);
  })
  .then(hash => {
    console.log(`Hash: ${hash}`);
    // Wachtwoord opslaan in uw database.
  })
  .catch(err => console.error(err.message));
```

Als eerste maken we een `salt` aan met de functie `bcrypt.genSalt` deze functie vraagt ons om de cost `saltRounds`. 
Eenmaal geslaagd krijgen we de salt waarde terug, deze sturen we samen met het wachtwoord `password` door naar de functie `bcrypt.hash`.
Bij succes krijgen we een hash terug, deze kunnen we in onze database opslaan. 

In de code staat twee keer een `console.log` een toont de `salt` de ander de `hash`. 

Tijdens de eerste run zijn dit de resultaten:

```
Salt: $2b$15$Wx.kUJY72dw5fkMxMm4R5e
Hash: $2b$15$Wx.kUJY72dw5fkMxMm4R5ejJ7NReyF7iB3A0mZtfX6BpvH0UXi9m6
```

Het is niet mogelijk om deze resultaten te reproduceren, dit omdat de `salt` compleet random is elke keer dat `genSalt` uitgevoerd wordt.
Tijdens een tweede run zijn dit de resultaten:

```
Salt: $2b$15$qH74g5/5ttl9iafc7XjYXu
Hash: $2b$15$qH74g5/5ttl9iafc7XjYXuJL8CdV96KPPqYa40KbImemgzAWdl/w6
```

Door gebruik te maken van de `bcrypt.hash` methode kunnen we een wachtwoord vergelijken met een opgeslagen hash. 

```node
//app.js
const bcrypt = require("bcrypt");
const password = "Wachtwoord12345";
const hash = "$2b$15$yjRTPHS0CN3mjVhe6YxFAu6.rnjQMAh8f/3DZckYDZBQtBLdGiPC2";

bcrypt
  .compare(password, hash)
  .then(res => {
    console.log(res);
  })
  .catch(err => console.error(err.message));
```

In het bovenstaande voorbeeld is de `res` `true`, dit geeft aan dat het `password` na dat het gehashed is gelijk is aan het opgeslagen wachtwoord `hash`.

Als we het `password` vervangen met een ander wachtwoord verwachten we als `res` `false` is. 

```node
//app.js
const bcrypt = require("bcrypt");
const password = "Foutwachtwoord123";
const hash = "$2b$15$yjRTPHS0CN3mjVhe6YxFAu6.rnjQMAh8f/3DZckYDZBQtBLdGiPC2";

bcrypt
  .compare(password, hash)
  .then(res => {
    console.log(res);
  })
  .catch(err => console.error(err.message));
```

En inderdaad de `res` zou nu `false` moeten zijn.

Iets wat opvalt, is dat we nooit de `salt` opslaan. Maar hoe weet `bcrypt.compare` dan welke `salt` te gebruiken? 
Kijkende naar het vorige `hash/salt` resultaat, kun je zien dat de `hash` de `salt` is met hieraan de wachtwoord `hash` toegevoegd. 

```
Salt: $2b$15$Wx.kUJY72dw5fkMxMm4R5e
Hash: $2b$15$Wx.kUJY72dw5fkMxMm4R5ejJ7NReyF7iB3A0mZtfX6BpvH0UXi9m6
```

`bcrypt.compare` scheid het `salt` van de hash. En hashed vervolgens het verstrekte wachtwoord en vergelijkt deze. 

## Bibliografie
Wikipedia. (2021, 11 02). www.wikipedia.org. Opgehaald van wikipedia.org:
https://en.wikipedia.org/wiki/Blowfish_(cipher)

USENIX. (1999, 04 28). www.usenix.org. Opgehaald van usenix.org: 
https://www.usenix.org/legacy/events/usenix99/provos/provos_html/node1.html

Wikipedia. (2021, 11 21). www.wikipedia.org. Opgehaald van wikipedia.org:
https://en.wikipedia.org/wiki/Bcrypt

OWASP. (2021, 01 01). www.cheatsheetseries.owasp.org. Opgehaald van cheatsheetseries.owasp.org:
https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html#work-factors

Investopedia. (2021, 02 23). www.investopedia.com. Opgehaald van investopedia.com:
https://www.investopedia.com/terms/m/mooreslaw.asp

YouTube. (2014, 06 27). www.youtube.com. Opgehaald van YouTube.com:
https://www.youtube.com/watch?v=O6cmuiTBZVs

StackExchange. (2018, 04, 01) www.security.stackexchange.com. Opgehaald van Security.stackexchange.com
https://security.stackexchange.com/questions/184799/bcrypt-no-need-to-store-salt