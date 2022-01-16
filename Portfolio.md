<div align="center">
    <h1>Portfolio | Rob Rutjes | Fontys ICT | Semester 3</h1>
</div>

<div align="center">
    <h2>Learning outcomes</h2>
</div>

## 1. You design and build user-friendly, full-stack web applications.

### Product API
De product API verzorgt het bijhouden van alle product informatie. 
De API is geschreven in Node.js met het ORM (object-relational mapping) framework [Sequelize](https://sequelize.org/). 
In de achtergrond is een MySQL database gekoppeld. 

<table>
    <tr>
        <td><img src="https://i.ibb.co/bdfR0hZ/Product-api-response.png"></td>
        <td><img src="https://i.ibb.co/frcG7Zv/Product-api-routes.png"></td>
    </tr>
</table>

Bron:
- [biddify-product-node.js](https://github.com/Biddify/biddify-product-node.js)

### React.js front-end
Voor de front-end moet je in semester 3 een framework kiezen. 
Hiervoor heb ik React.js gekozen. De (simpele) applicatie bestaat uit een producten overzicht, het aanmaken/bijwerken van producten en het verwijderen hiervan.

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

Bron:
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

Bron:
- [biddify-react.js](https://github.com/Biddify/biddify-react.js)

## 2. You use software tooling and methodology that continuously monitors and improve the software quality during software development.

### Integration tests
Testen zijn erg belangrijk, deze helpen bij het voorkomen van bugs in de applicatie. Voor mijn tests gebruik ik [Supertest](https://www.npmjs.com/package/supertest). De testen zijn gebaseerd op user stories. 
Ik maak gebruik van een test database omdat dit aangereden/ondersteund is door Sequelize. Op het begin van dit semester gebruikte ik hiervoor een MySQL database, maar omdat deze veel opstart tijd heeft, ben ik later overgeschakeld naar een SQLite database. 
De onderstaande testen zijn integratie testen, unit testen zijn in mijn geval overbodig omdat ik hiermee alleen de ORM zou testen, als het goed is test Sequelize dit zelf. 

<table>
    <tr>
        <td><img src="https://i.ibb.co/3spNkZp/Product-tests.png"></td>
    </tr>
</table>

### Automatisch testen op pull requests
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

### Code review GitHub

## 3. You design and implement a (semi)automated software release process.

### Docker images/containers
Voor mijn project heb ik momenteel 4 docker containers, front-end React.js, Product Node.js, Product MySQL, Product MySQL admin. 
In de readme.md van de [Biddify-main](https://github.com/Biddify/biddify-main) repository staat een uitleg hoe alle docker containers op te starten.

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
In de overschrijving van elke issue staat een lijstje met dingen die gedaan moeten worden voordat deze gesloten kan worden.
Het andere dashboard is voor bugs. In de omschrijving staat een uitleg van het probleem. En eventueel wat er al onderzocht is. 

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

### Research
Mijn research heb ik geschreven over Bcrypt. Mijn hoofdvraag was "Is het veilig om met Bcrypt wachtwoorden op te slaan?". 
Met de volgende deelvragen:
- Wat is Bcrypt?
- Hoe werkt Bcrypt?
- Waarom is Bcrypt veilig?
- Hoe implementeer je Bcrypt?

Alles is terug te vinden in de [Biddify-main](https://github.com/Biddify/biddify-main) repository in `Password-encryption-algorithm-research.md`.

<table>
    <tr>
        <td><img src="https://i.ibb.co/rfDt8tS/Github-research.png"></td>
    </tr>
</table>