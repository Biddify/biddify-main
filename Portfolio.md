<div align="center">
    <h1>Portfolio | Rob Rutjes | Fontys ICT | Semester 3</h1>
</div>

<div align="center">
    <h2>Learning outcomes</h2>
</div>

# Personal Project

## 1. You design and build user-friendly, full-stack web applications.

### Product API
De product API verzorgt het bijhouden van alle product informatie. 
De API is geschreven in [Node.js](https://nodejs.org/en/) met het ORM (object-relational mapping) framework [Sequelize](https://sequelize.org/). 
In de achtergrond is een [MySQL](https://www.mysql.com/) database gekoppeld. 

<table>
    <tr>
        <td><img src="https://i.ibb.co/bdfR0hZ/Product-api-response.png"></td>
        <td><img src="https://i.ibb.co/frcG7Zv/Product-api-routes.png"></td>
    </tr>
</table>

Repository:
- [biddify-product-node.js](https://github.com/Biddify/biddify-product-node.js)

### React.js front-end
Voor de front-end moet je in semester 3 een framework kiezen. 
Hiervoor heb ik [React.js](https://reactjs.org/) gekozen. De (simpele) applicatie bestaat uit een producten overzicht, het aanmaken/bijwerken van producten en het verwijderen hiervan.

<table>
    <tr>
        <td><img src="https://i.ibb.co/xj3TgJ7/Product-list.png"></td>
        <td><img src="https://i.ibb.co/gVsvMps/Product-add.png)"></td>
    </tr>
    <tr>
        <td><img src="https://i.ibb.co/4p18qJY/Product-update.png"></td>
        <td><img src="https://i.ibb.co/M61snXy/Product-view.png)"></td>
    </tr>
</table>

Repository:
- [biddify-react.js](https://github.com/Biddify/biddify-react.js)

### Identity service Auth0
Het inlog/registratie systeem laat ik afhandelen door [Auth0](https://auth0.com/). 
Zelf is het lastig om een veilig login/registratie systeem te bouwen. 
Momenteel zijn producten nog niet gekoppeld aan gebruikers, maar dit kan later zeer eenvoudig toegevoegd worden. 

<table>
    <tr>
        <td><img src="https://i.ibb.co/B6bMHCp/Auth0.jpg"></td>
    </tr>
</table>

Repository:
- [biddify-react.js](https://github.com/Biddify/biddify-react.js)

## 2. You use software tooling and methodology that continuously monitors and improve the software quality during software development.

### Integration tests
Testen zijn erg belangrijk, deze helpen bij het voorkomen van bugs in een applicatie. Voor mijn tests gebruik ik [Supertest](https://www.npmjs.com/package/supertest). De testen zijn gebaseerd op user stories. 
Ik maak gebruik van een test database omdat dit aangereden/ondersteund is door [Sequelize](https://sequelize.org/). Op het begin van dit semester gebruikte ik hiervoor een [MySQL](https://www.mysql.com/) database, maar omdat deze veel opstart tijd heeft, ben ik later overgeschakeld naar een [SQLite](https://www.sqlite.org/index.html) database. 
De onderstaande testen zijn integratie testen, unit testen zijn in mijn geval overbodig omdat ik hiermee alleen het ORM zou testen, als het goed is test [Sequelize](https://sequelize.org/) dit zelf. 

<table>
    <tr>
        <td><img src="https://i.ibb.co/3spNkZp/Product-tests.png"></td>
    </tr>
</table>

### Automatic tests on pull requests
De bovenstaande integratie testen worden automatisch uitgevoerd op een pull request naar de master branch.  
Hiervoor gebruik ik GitHub actions met de onderstaande workflow. Verder zit hier ook een code analyse in via [SonarCloud](https://sonarcloud.io/), deze checkt verschillende dingen zoals de code coverage (testen), code smells, bugs, vulnerabilities & duplications.

```
name: Build, SonarCloud & Docker

on:
  pull_request:
    branches: [ master ]

  workflow_dispatch:

jobs:

  build:
    runs-on: ubuntu-latest
    env:
      DB_HOST: ${{ secrets.DB_HOST }}
      DB_USER: ${{ secrets.DB_USER }}
      DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
      DB_DATABASE: ${{ secrets.DB_DATABASE }}
      DB_PORT: ${{ secrets.DB_PORT }}
      DB_DIALECT: mysql
      PORT: ${{ secrets.PORT }}
      DB_DIALECT_TEST: ${{ secrets.DB_DIALECT_TEST }}
      
    strategy:
      fail-fast: false
      matrix:
        node-version: [14.x]
  
    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v2
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      - run: npm install
      - run: npm run build --if-present

  sonarcloud:
    runs-on: ubuntu-latest
    env:
      DB_DIALECT_TEST: ${{ secrets.DB_DIALECT_TEST }}
      
    steps:
      - name: Check Out Repo
        uses: actions/checkout@v2

      - name: Install dependencies
        run: npm install

      - name: Run Tests
        run: npm run test

      - name: SonarCloud Scan
        uses: sonarsource/sonarcloud-github-action@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

<table>
    <tr>
        <td><img src="https://i.ibb.co/L8ywdkp/Sonarcloud.png"></td>
        <td><img src="https://i.ibb.co/19Xb6LX/Github-actions.png"></td>
    </tr>
    <tr>
        <td><img src="https://i.ibb.co/89Bh0Z9/Github-actions-sonarcloud.png"></td>
        <td><img src="https://i.ibb.co/fY0D8SW/Sonarcloud-dashboard.png"></td>
    </tr>
</table>

Repository:
- [biddify-product-node.js](https://github.com/Biddify/biddify-product-node.js)

### Code review GitHub
Aan [@StijnSchellekens](https://github.com/StijnSchellekens) heb ik gevraagd mijn `README.md` te reviewen.
Hij gaf hierop goede feedback dat het momenteel niet duidelijk is waar/hoe testen aan te maken. 
Hierop heb ik mijn `README.md` bijgewerkt in de [Biddify-main](https://github.com/Biddify/biddify-main) repository. 

<table>
    <tr>
        <td><img src="https://i.ibb.co/VSNP7hB/Github-review.png"></td>
    </tr>
</table>

## 3. You design and implement a (semi)automated software release process.

### Docker images/containers
Voor mijn project heb ik momenteel 4 [Docker](https://www.docker.com/) containers draaien, front-end [React.js](https://reactjs.org/), product [Node.js](https://nodejs.org/en/), product [MySQL](https://www.mysql.com/), product [MySQL](https://www.mysql.com/) admin. 
Onder [Docker](https://github.com/Biddify/biddify-main/tree/development#docker) in de `README.md` van de [Biddify-main](https://github.com/Biddify/biddify-main) repository staat een uitleg hoe alle containers op te starten.

<table>
    <tr>
        <td><img src="https://i.ibb.co/92mmrBd/Dockerhub.png"></td>
        <td><img src="https://i.ibb.co/VLNSkZb/Docker-desktop.png"></td>
    </tr>
</table>

Nadat de `Build` en `SonarCloud` GitHub actions succesvol zijn uitgevoerd, draait automatisch het aanmaken van de [Docker](https://www.docker.com/) image, als deze is aangemaakt stuurt hij deze door naar mijn [DockerHub](https://hub.docker.com/) account.

```
docker:
needs: [build, sonarcloud]
runs-on: ubuntu-latest
steps:
  - name: Check Out Repo 
    uses: actions/checkout@v2

  - name: Login to Docker Hub
    uses: docker/login-action@v1
    with:
      username: ${{ secrets.DOCKER_USERNAME }}
      password: ${{ secrets.DOCKER_PASSWORD }}

  - name: Set up Docker Buildx
    id: buildx
    uses: docker/setup-buildx-action@v1

  - name: Build and push
    id: docker_build
    uses: docker/build-push-action@v2
    with:
      context: ./
      file: ./Dockerfile
      push: true
      tags: robfontys/biddify-product-node.js:latest

  - name: Image digest
    run: echo ${{ steps.docker_build.outputs.digest }}
```

<table>
    <tr>
        <td><img src="https://i.ibb.co/prWVF37/Github-actions-docker.png"></td>
        <td><img src="https://i.ibb.co/VQvjS2h/Dockerhub-product-service.png"></td>
    </tr>
</table>

## 4. You act in a professional manner during software development and learning.

### Project board
Voor Biddify heb ik twee project dashboarden gemaakt. De eerste is voor de user stories en de status (ToDo, In Progress & Done) hiervan. 
In de omschrijving van elke issue staat een lijstje met dingen die gedaan moeten worden voordat deze gesloten kan worden.
Het andere dashboard is voor bugs. In de omschrijving staat een uitleg van het probleem. En wat er eventueel al onderzocht is.

<table>
    <tr>
        <td><img src="https://i.ibb.co/vqjbFVY/Github-project-dashboard.png"></td>
        <td><img src="https://i.ibb.co/0243qfT/Github-issue-feature.png"></td>
    </tr>
    <tr>
        <td><img src="https://i.ibb.co/8s2wgRt/Github-issue-dashboard.png"></td>
        <td><img src="https://i.ibb.co/pWdk2MM/Github-issue-bug.png"></td>
    </tr>
</table>

### Readme
Om ervoor te zorgen dat mensen makkelijk kunnen instappen in Biddify staat in de [Biddify-main](https://github.com/Biddify/biddify-main) repository een `README.md` voor hun klaar. 
Deze moet alles uitleggen hoe binnen de repositories gewerkt wordt. De `README.md` bevat de volgende onderwerpen:
- Getting started
- Planning
- Docker 
- API Documentation 
- Coding Conventions 
- C4 Model 
- Branch  
- Pull Request

<table>
    <tr>
        <td><img src="https://i.ibb.co/FHPkdY5/Github-readme-1.png"></td>
        <td><img src="https://i.ibb.co/1b3j4Jt/Github-readme-2.png"></td>
        <td><img src="https://i.ibb.co/nsfm3t0/Github-readme-3.png"></td>
    </tr>
</table>

### Research
Mijn research heb ik geschreven over [Bcrypt](https://en.wikipedia.org/wiki/Bcrypt). 

De hoofdvraag was "Is het veilig om met Bcrypt wachtwoorden op te slaan?". 

Met de volgende deelvragen:
- Wat is Bcrypt?
- Hoe werkt Bcrypt?
- Waarom is Bcrypt veilig?
- Hoe implementeer je Bcrypt?

Alles is terug te lezen in de [Biddify-main](https://github.com/Biddify/biddify-main) repository in `Password-encryption-algorithm-research.md`.

<table>
    <tr>
        <td><img src="https://i.ibb.co/rfDt8tS/Github-research.png"></td>
    </tr>
</table>

# Group Project

## 1. You choose and implement the most suitable agile software development method for your software project.
Voor deze leeruitkomst heb ik een verslag geschreven over agile en hoe wij dit in ons project hebben geïmplementeerd. 
Op de eerste pagina staat uitgelegd wat agile is, welke agile methodes er zijn & welke het meeste gebruikt worden. 
De tweede pagina bestaat uit hoe [Scrum](https://www.scrumcompany.nl/wat-is-scrum/) en [Kanban](https://agilescrumgroup.nl/wat-is-kanban-methode/) werken. 
Op de laatste pagina heb ik omschreven hoe wij agile (Scrum) in ons project laten terug komen. 

<table>
    <tr>
        <td><img src="https://i.ibb.co/rpKvp5S/GP-scrum-1.png"></td>
        <td><img src="https://i.ibb.co/Nn7P68K/GP-scrum-2.png"></td>
        <td><img src="https://i.ibb.co/LpPYS1q/GP-scrum-3.png"></td>
    </tr>
</table>

Bron:
- 21 11 29 RR Leeruitkomsten groepsproject aantonen.docx

## 2. You analyze and describe simple business processes that are related to your project.
Op de eerste pagina van het verslag hieronder staat uitgelegd wat een business process is.
Daarna welk probleem er momenteel bij ISAAC is, en hoe dit het beste opgelost kan worden. 
Op de tweede pagina staat een [BPMN-model](https://www.lucidchart.com/pages/nl/wat-is-business-process-modeling-notation), deze moet ervoor zorgen dat de workflow van applicatie goed te begrijpen is.

<table>
    <tr>
        <td><img src="https://i.ibb.co/khZrVhj/GP-bus-1.png"></td>
        <td><img src="https://i.ibb.co/KKBbhc7/GP-bus-2.png"></td>
    </tr>
</table>

Bron:
- 21 11 29 RR Leeruitkomsten groepsproject aantonen.docx

## 3. You analyze (non-functional) requirements, elaborate (architectural) designs and validate them using multiple types of test techniques.
Voor het project van ISAAC hebben we een project plan geschreven. Eerst zonder template, daarna met. Om de overview van de applicatie te verduidelijken hebben we een C4 model level 2 gemaakt voor de applicatie. 
Op het begin van het project hebben we een brainstorm sessie gehouden, waarin iedereen zijn ideeën/ervaringen kon delen! 

<table>
    <tr>
        <td><img src="https://i.ibb.co/McbhJh4/Isaac-projectplan-1.png"></td>
        <td><img src="https://i.ibb.co/87B5kWj/Isaac-projectplan-2.png"></td>
        <td><img src="https://i.ibb.co/Ks6z60W/Isaac-projectplan-3.png"></td>
    </tr>
    <tr>
        <td><img src="https://i.ibb.co/34C0Qng/Isaac-projectplan-4.png"></td>
        <td><img src="https://i.ibb.co/D8TvHTm/Isaac-projectplan-5.png"></td>
        <td><img src="https://i.ibb.co/SVp9KqN/Isaac-projectplan-6.png"></td>
    </tr>
    <tr>
        <td><img src="https://i.ibb.co/w73gZK4/Isaac-research.png"></td>
        <td><img src="https://i.ibb.co/BndBtPn/Isaac-C4.png"></td>
        <td><img src="https://i.ibb.co/ZxRmgWQ/Isaac-overview.png"></td>
    </tr>
</table>

Bron: 
- annotated-Project Plan ISAAC S3 DB-01 group 3.docx
- ISAAC-Application overview.drawio.png
- Project Document ISAAC.docx

## 4. You recognize and take into account cultural differences between project stakeholders and ethical aspects in software development.
Voor deze leeruitkomst heb ik een verslag geschreven over cultuur. Hiervoor heb ik het [Hofstede](https://www.hofstede-insights.com/) framework gebruikt. 
Deze heb ik vergeleken met de Nederlandse cultuur en die van mijzelf. Op de tweede pagina staat een verslag over de ethiek in software development.

<table>
    <tr>
        <td><img src="https://i.ibb.co/60vkTg0/GP-cul-1.png"></td>
        <td><img src="https://i.ibb.co/ZWHHGWg/GP-cul-2.png"></td>
        <td><img src="https://i.ibb.co/9HMnw8Q/GP-cul-3.png"></td>
    </tr>
</table>

Bron: 
- 21 11 29 RR Leeruitkomsten groepsproject aantonen.docx

## 5. You act in a professional manner during software development and learning.
Voor elke sprint oplevering met ISAAC was er een Powerpoint presentatie. 
Tijdens het project heb ik verschillende keren (16 stuks) emails gestuurd over de MQTT-server.
Elke ochtend op project dagen was er  een start-up meeting. Hierin vertelde iedereen wat hij de dag ervoor gedaan had en wat hij die dag ging doen. 

<table>
    <tr>
        <td><img src="https://i.ibb.co/74f0zzX/Isaac-powerpoint-1.png"></td>
        <td><img src="https://i.ibb.co/DzFwS1c/Isaac-powerpoint-2.png"></td>
        <td><img src="https://i.ibb.co/2smvpd2/Isaac-email.png"></td>
    </tr>
</table>

Bron: 
- sprint 3 delivery.pptx
- office 365 (outlook)

## 6. You design and build user-friendly, full-stack web applications.
Voor ISAAC hebben wij een IOT-dashboard gemaakt. Waarin een medewerker de actuele temperatuur en luchtvochtigheid binnen het gebouw kan bekijken. 
Via een heatmap met (live) data van alle sensoren kan een medewerker zien waar hij het beste kan gaan zitten of moet gaan zitten. Losse sensoren kunnen worden in/uit geschakeld.
Wij hebben al onze services uitgesplitst. De back-end maakt gebruik van [Node.js](https://nodejs.org/en/), de front-end van [React.js](https://reactjs.org/). Beiden zijn Javascript frameworks. De databases gebruiken [MySQL](https://www.mysql.com/). 
Alle code staat in onze GitHub organisatie [s3-db01](https://github.com/s3-db01). 

<table>
    <tr>
        <td><img src="https://i.ibb.co/hXgb1XS/Isaac-github.png"></td>
        <td><img src="https://i.ibb.co/B6HSfvf/Isaac-app-dashboard.png"></td>
        <td><img src="https://i.ibb.co/pjgpJ75/Isaac-app-heatmap.png"></td>
    </tr>
</table>

Bron: 
- s3-db01 GitHub
- https://lit-beyond-41227.herokuapp.com/

# Mijn ervaring in semester 3
Terugkijkende op semester 3 heb ik het erg naar mijn zin gehad. 
Werken in de projectgroep was gezellig, en we hebben een mooi eindproduct opgeleverd aan ISAAC. 
Op het begin was het even wennen/lastig om Engels te spreken binnen de groep. Gesprekken verliepen stroef en er werd alleen formeel gecommuniceerd.  
Later ging dit stukken beter. 
 
Voor mijn persoonlijk project heb ik als eerste dit semester [Node.js](https://nodejs.org/en/) geleerd, als ORM gebruikte ik [Sequelize](https://sequelize.org/). 
Uiteindelijk was dit soms best lastig, dingen werkten niet zoals verwacht. Maar nu meer ervaring met Sequelize heb, kan ik het goed gebruiken, en zelfs aanraden als je er de tijd in wilt stoppen om het (goed) te leren. 
Hierna ben ik begonnen met onderzoek doen hoe [Docker](https://www.docker.com/) werkt. Na een YouTube video van 4 uur wist ik hoe het basis concept werkt, met het aanmaken van [Docker](https://www.docker.com/) images/containers. En het gebruik van een `docker-compose.yml` file. 
Als front-end framework heb ik gekozen voor [React.js](https://reactjs.org/). Ook hiervan heb ik een tutorial gekeken op YouTube. 

Tijdens het groepsproject heb ik veel samen gewerkt met Jules. Samen hebben we onderzocht hoe je automatisch unit testen uit moet voeren voor [Node.js](https://nodejs.org/en/) in Github actions. 
Zelf heb ik SonarCloud toegevoegd aan de GitHub actions van mijn persoonlijk project. Na (weer) wat vechten met [Sequelize](https://sequelize.org/) werden de unit testen uiteindelijk ook gecheckt op code coverage.

Er zijn zeker dingen die ik anders had aangepakt. Zo was het bijvoorbeeld niet handig om zo snel in het semester al [Docker](https://www.docker.com/) te leren.
En was het op het begin van het semester onduidelijk dat programmeren niet het belangrijkste was. Het hebben van overzichtelijke repositories wel.