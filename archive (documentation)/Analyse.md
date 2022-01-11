# Biddify | Rob Rutjes | Fontys ICT | Semester 3 | Analyse

## Planning Per Sprint (versie 1)
| Sprint | Werkzaamheden |
| ----------- | ----------- |
| Oplevering Sprint 1 | Documenten, Onderzoeken uitvoeren       |
| Oplevering Sprint 2 | Distributed software architectuur & Back-end development       |
| Oplevering Sprint 3 | Front-end development       |
| Oplevering Sprint 4 | Data persistence & Quality assurance       |
| Oplevering Sprint 5 | Software release management       |
| Eindoplevering | Distributed system & Software release management       |

*Elke sprint bestaat uit 3 weken.

---

## Requirements
- FR-01 Gebruiker kan zichzelf registeren.
    - B-01.1 Gegevens moeten op geldigheid gecontroleerd worden.
    - B-01.2 Recaptcha op het formulier.

- FR-02 Gebruiker kan zichzelf inloggen.
    - B-02.1 Bij 5 verkeerde pogingen wordt de gebruiker voor bepaalde tijd geblokkeerd.
    - B-02.2 Foute inlog pogingen dienen gelogd te worden.

- FR-03 Gebruiker kan zichzelf uitloggen.

- FR-04 Verkoper kan product aanbieden.
    - B-04.1 Bij het aanbieden wordt de titel, omschrijving & prijs vastgelegd.
    - B-04.2 Foto’s van het product kunnen worden toegevoegd.

- FR-05 Aankoper kan product kopen.
    - B-05.1 Aankoop van product moet binnen ..X.. seconden verwerkt worden.

- FR-6 Aankoper kan een overzicht bekijken van zijn gekochte producten.
    - B-6.1 Als de gebruiker geen producten heeft gekocht staat er “No Records Found”.

- FR-7 Verkoper kan een overzicht bekijken van zijn aangeboden producten.
    - B-7.1 Als de gebruiker geen producten heeft aangeboden staat er “No Records Found”.

- FR-8 Producten kunnen via bod worden aangeboden

- FR-9 Betalingen worden afgehandeld door een externe betaling service ([Stripe](https://stripe.com/))


- K-ALG.01 Bij onjuiste invoer moet een duidelijke foutmelding getoond worden.

- K-ALG.02 Foutmeldingen moeten aan de gebruiker getoond worden.

---

## User Stories
US01
- **Als** (nieuwe) gebruiker, **wil ik** mijzelf kunnen registeren, **zodat ik** gebruik kan maken van de applicatie.

Acceptatiecriteria:
- Gebruiker kan zichzelf registeren.

US02
- **Als** gebruiker, **wil ik** kunnen inloggen, **zodat ik** gebruik kan maken van de applicatie.

Acceptatiecriteria:
-	Gebruiker kan zichzelf inloggen.

US03
- **Als** gebruiker, **wil ik** kunnen uitloggen, **zodat ik** de applicatie veilig kan afsluiten.

Acceptatiecriteria:
- Gebruiker kan zichzelf uitloggen.

US04
- **Als** verkoper, **wil ik** een product kunnen aanbieden, **zodat ik** deze kan verkopen.

Acceptatiecriteria:
-	Gebruiker kan een product aanbieden.

US05
- **Als** aankoper, **wil ik** een product kunnen kopen, **zodat ik** deze kan gaan ophalen of verstuurd kan worden.

Acceptatiecriteria:
-	Gebruiker kan een product kopen.

US06
- **Als** aankoper, **wil ik** een overzicht kunnen zien van mijn gekochte producten, **zodat ik** mijn aankoop geschiedenis kan zien.

Acceptatiecriteria:
- Gebruiker kan een overzicht zien van zijn gekochte producten.

US07
- **Als** verkoper, **wil ik** een overzicht kunnen zien van mijn verkochte producten, **zodat ik** mijn verkoop geschiedenis en winst kan zien.

Acceptatiecriteria:
- Gebruiker kan een overzicht zien van zijn verkochte producten.

US08
- **Als** aankoper, **wil ik** op producten kunnen bieden, **zodat ik** niet gelijk de vraagprijs hoef te betalen.

Acceptatiecriteria:
- Gebruiker kan op een product bieden.

US09
- **Als** aankoper, **wil ik** via een makkelijke en veilige manier kunnen betalen, **zodat ik** niet opgelicht kan worden.

Acceptatiecriteria:
- Gebruiker kan via een externe betaling service betalen.

US-10
- **Als** programmeur, **wil ik** kunnen zien hoe de programma-architectuur eruit komt te zien, **zodat ik** snel een overview krijg hoe de applicatie werkt.

Acceptatiecriteria:
- Ontwerp, analyse, etc documenten.

US-11
- **Als** programmeur, **wil ik** dat mijn code automatisch getest en uitgerold gaat worden, **zodat ik** bij nieuwe commits geen bugs veroorzaak en de applicatie altijd deployment ready is.

Acceptatiecriteria:
-	Automatisch testen en uitrollen.

---