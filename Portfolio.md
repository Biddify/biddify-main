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

![](https://i.ibb.co/bdfR0hZ/Product-api-response.png)

![](https://i.ibb.co/frcG7Zv/Product-api-routes.png)

Bron:
- [biddify-product-node.js](https://github.com/Biddify/biddify-product-node.js)

### React.js front-end
Voor de front-end moet je in semester 3 een framework kiezen. 
Hiervoor heb ik React.js gekozen. De (simpele) applicatie bestaat uit een producten overzicht, het aanmaken/bijwerken van producten en het verwijderen hiervan.

![](https://i.ibb.co/xj3TgJ7/Product-list.png)

![](https://i.ibb.co/gVsvMps/Product-add.png)

![](https://i.ibb.co/4p18qJY/Product-update.png)

![](https://i.ibb.co/M61snXy/Product-view.png)

Bron:
- [biddify-react.js](https://github.com/Biddify/biddify-react.js)

### Identity service Auth0
Het inlog/registratie systeem laat ik afhandelen door [Auth0](https://auth0.com/). 
Zelf is het heel lastig om een veilig inlog/registratie systeem te bouwen. 
Momenteel zijn producten nog niet gekoppeld aan gebruikers, maar dit kan later zeer eenvoudig toegevoegd worden. 

![](https://i.ibb.co/tbf7PbK/Auth0.png)

Bron:
- [biddify-react.js](https://github.com/Biddify/biddify-react.js)

## 2. You use software tooling and methodology that continuously monitors and improve the software quality during software development.

### Integration tests
Testen zijn erg belangrijk, deze helpen bij het voorkomen van bugs in de applicatie. Voor mijn tests gebruik ik [Supertest](https://www.npmjs.com/package/supertest). De testen zijn gebaseerd op een user story. 
Ik maak gebruik van een test database omdat dit aangereden/ondersteund word door Sequelize. Op het begin gebruikte ik hiervoor een MySQL database, maar omdat deze veel opstart tijd heeft, ben ik later overgeschakeld naar een SQLite database. 
De onderstaande testen zijn integratie testen, unit testen zijn in mijn geval overbodig omdat ik hiermee alleen de ORM zou testen, als het goed is test Sequelize deze zelf. 

![](https://i.ibb.co/3spNkZp/Product-tests.png)

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

![](https://i.ibb.co/L8ywdkp/Sonarcloud.png)

![](https://i.ibb.co/19Xb6LX/Github-actions.png)

![](https://i.ibb.co/89Bh0Z9/Github-actions-sonarcloud.png)

### Code review GitHub

## 3. You design and implement a (semi)automated software release process that matches the needs of the project context.

### 1. Docker containers

### 2. Deploy webservices with Github actions

## 4. You act in a professional manner during software development and learning.

### 1. Contribute to open source

### 2. Project board

### 3. GitHub flow

### 4. Research
