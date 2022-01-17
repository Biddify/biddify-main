<div align="center">
    <img width="400px" height="100%" src="https://user-images.githubusercontent.com/33746824/144393714-eaa2554e-a483-4519-ad94-f83c43b01a11.gif">
</div>

# Biddify | Rob Rutjes | Fontys ICT | Semester 3

## Repository
#### [Biddify-main](https://github.com/Biddify/biddify-main)
[![GitHub issues](https://img.shields.io/github/issues/Biddify/biddify-main?logo=GitHub)](https://github.com/Biddify/biddify-main/issues)

#### [Biddify-product-node.js](https://github.com/Biddify/biddify-product-node.js)
![workflow](https://github.com/Biddify/biddify-product-node.js/actions/workflows/main.yml/badge.svg)
[![GitHub issues](https://img.shields.io/github/issues/Biddify/biddify-product-node.js?logo=GitHub)](https://github.com/Biddify/biddify-product-node.js/issues)

#### [Biddify-react.js](https://github.com/Biddify/biddify-react.js)
![workflow](https://github.com/Biddify/biddify-react.js/actions/workflows/react.js.yml/badge.svg)
[![GitHub issues](https://img.shields.io/github/issues/Biddify/biddify-react.js?logo=GitHub)](https://github.com/Biddify/biddify-react.js/issues)

## Getting Started
Welcome the biddify repository! 

If you are new here carefully read this readme as it contains helpful information. This way you should have all the information needed to collaborate on the project. 

Biddify is an application where a seller can sell his (second hand) products. And buyers can buy them. 
There should be an easy login & register page. After this a user can take a picture of his product and add a title, description, price for the product he wants to place on Biddify. 
Other users can scroll through all the listed products and buy them for the asking price.
Also, there will be a list of bought and sold products for every user.

In the second phase of the development there could be support for live bidding and payment via external service for example [Stripe](https://stripe.com/).

React.js / Node.js install npm packages and run the application. 
```
npm install
npm start
```

To run unit/integration tests run the following command. 
```
npm test
```

All tests can be found in the `/test` folder of the project. 

If not already exists you can create your own test file here.

Example `product.test.js`.

All tests are based on user stories, the name/description of the test should make this clear. 

Example `describe('US-04 | Als verkoper, wil ik een product kunnen aanbieden, zodat ik deze kan verkopen')`.

## Planning
For an overview of where the project stands at the moment view the Biddify [GitHub Projects](https://github.com/orgs/Biddify/projects/1) dashboard.
In this dashboard you can view which features still need to be implemented and the priority of them.
When you start implementing the status of a feature goes to "in progress" when fully implemented the status goes to "done".

Every user story is stored as an issue, in the description are the acceptance criteria for the user story. 

## Docker
To start the (complete) project in Docker you need to download/pull the biddify-main repository.

In the root folder open a terminal window and run the following command to get inside the biddify-main folder.

`cd biddify-main`

Next run this command to get inside the Docker folder.

`cd docker`

Finally, to start all the project containers in docker you run the following command.

`docker-compose up`

| **Service** | **URL**                                          |
| --- |--------------------------------------------------|
| biddify-product-database | [http://localhost:3306](http://localhost:3306)  |
| biddify-product-db-admin | [http://localhost:8080](http://localhost:8080)  |
| biddify-product-node.js | [http://localhost:3002](http://localhost:3002)  |
| biddify-react.js | [http://localhost:3001](http://localhost:3001)  |

## API Documentation
The API documentation is automatically generated with [Swagger](https://swagger.io/). 

After `http://localhost:{PORT}` add `/api-docs` for the Swagger documentation. 

For example (product API): `http://localhost:3002/api-docs`. 

## Coding Conventions
In Biddify we use the third party addon [Standard JS](https://github.com/standard/standard) for keeping the JavaScript coding conventions in line.

How to use Standard JS?

First run Standard JS with npx, this should return a list with recommendations/errors:

`npx standard`

```
Error: Use JavaScript Standard Style
lib/torrent.js:950:11: Expected '===' and instead saw '=='.
```

To automatically fix all recommendations/errors, simply run the following command.

`npx standard --fix`

To ignore folders/files add them to the `pakage.json`. All paths in the project root `.gitignore` are automatically ignored.

```
  "standard": {
    "ignore": [
      "/test/"
    ]
  }
```

## C4 Model
In the C4 level 2 model below you can see how all the services work together. 
Services starting with API are returning JSON, all database are using MySQL, the front-end is written in React.js. 

![](https://i.ibb.co/WsHcPjz/22-01-12-RR-C4-Model-Biddify-Versie-2-drawio.png)

## Branch
Pushing to the `master` branch is locked.

When developing a new user stories you can push to the `development` branch. 
After this is completed a pull request is created to the `master` branch. 
The unit tests & code analysis will run automatically. 
When the pull request is accepted the docker image is automatically updated.

## Pull Request
Within all the Biddify repositories we use pull requests to the `master` branch. 
A pull request should be reviewed within 2 working days after creation. 