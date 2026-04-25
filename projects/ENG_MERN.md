---
layout: default
title: MERN App
permalink: /projects/ENG_MERN/
---

<div id="content"></div>

# MERN Stack Chess App Introduction

## Link:

[https://chesspuzzlekappa54.me/](https://chesspuzzlekappa54.me/)

## Github link:

[https://github.com/Kappa5400/mern_chess](https://github.com/Kappa5400/mern_chess)

## Intro

This is a repo of this website that is deployed on Digital Ocean. Besides the normal parts of this website that are easy to see, this application has a fully working CI/CD pipeline that on each Github push will test and lints code, create a docker image for the front end and back ends, have a self hosted Github runner on the website server pull the docker images, build the images, then deploy the images.

Testing includes unit testing of both expected failures and expected success of each part of the service layer and also includes integration testing for each used API point. The app does a couple of things currently: It supports account creation and login. It calls the Lichess daily puzzle endpoint and saves the puzzle to the database. It shows the last ten daily lichess puzzles. It supports a basic score system, solving a puzzle while logged into an account gives you a point. It shows the top 5 scoring users across the website.

For logged in users it supports puzzle creation, viewing, and deleation. You can browse and play through all saved user puzzles. The purpose of this app is to showcase my software engineering abilities. As such, I will go through a more detailed overview below.

Database Design<br/>
<img src="{{ '/assets/css/img/DB_arc.jpg' | relative_url }}" alt="DB Architecture" style="width: 100%; height: auto;" />
<br/>
System Design<br/>
<img src="{{ '/assets/css/img/Mern_arc.jpg' | relative_url }}" alt="System Architecture" style="width: 100%; height: auto;"/>

## Project Walkthrough System Architecture

The webapp is built with a client-server architecture, where as the backend is seperate from the frontend. The backend contains the database, mongodb in this case, the service layer, and the routing layer. It also contains some middleware for data validation and authentication, unit and integration testing, logging utlities, and a simple job script.

The apis are accessed via the frontend's api handler layer which is either accessed through react hooks or directly in components or pages. The frontend also contains authentication contexts to handle tokens.

### Packages Used

- **Security:** `bcrypt`, `exprewss-jwt`, `express-mongo-sanatize`, `joi`, `helmet`, `jsonwebtoken`
- **Utility:** `axios`, `dotenv`, `compression`, `cors`, `cron`, `mongoose`, `morgan`, `winston`, `swagger`, `eslint`, `cross-env`
- **Testing:** `jest`, `mongodb-memory-server`, `supertest`
- **Frontend:** `React`, `Vite`, `Chess.js`, `react-chessboard`, `tanstack`, `jwt-decode`, `react-router-dom`

### Package Explination

#### Security

- **bcrypt:** Hashing package for encrypting user passwords.
- **express-jwt / express-mongo-sanitize:** Mongo-Sanatization middleware to prevent sql injection attacks.
- **joi:** Middleware for input validation.
- **helmet:** Helmet middleware for HTTP security.
- **jsonwebtoken:** JSON web tokens for session authentication.

#### Utility

- **axios:** API Request package, used to send HTTP GET request to Lichess dailypuzzle backend.
- **compression:** Compresses HTTP responses.
- **cors:** Cross-Origin Resource Sharing, so that the backend api is accessable to the frontend.
- **cron:** An automatic script runner, used to run the fetch jobs to retreive the lichess dailypuzzle daily. I have the script run twice a day, in the morning and evening, to fetch the daily chess puzzle when it is udpated. The lichess daily puzzle updates randomly during the day. Duplicate puzzles are ignored. The job scripts are in the following filepath: `chess/backend/src/service/jobs`
- **mongoose:** Database ARM to run queries into mongodb.
- **morgan:** middleware for logging http requests.
- **winston:** Logging.
- **swagger:** Backend API Documentation.
- **eslint:** JS linter.
- **cross-env:** Enviornmental variable.

#### Testing

- **Jest:** The testing suite I used for unit testing and integration testing.
- **mongodb-memory-server:** Memory server used during testing. Instead of using my development db, I use an in memory db to mock db usage for testing.
- **supertest:** Integration and end to end test package.

#### Frontend

- **React:** Frontend framework.
- **Vite:** Frontend build tool.
- **Chess.js:** Handles the chess game logic.
- **react-chessboard:** React chessboard component to display the board conditions, interacts with chess.js for rule handling.
- **tanstack:** Querying tool for react.
- **jwt-decode:** for decoding jwt tokens made in the backend, for validating frontend user session.
- **react-router-dom:** routing for frontend in react.

### File structure

#### Backend File Structure

```text
mern_chess/chess/src/
├── __tests__/      # Unit test and integration testing.
├── db/             # Stores the database init script and models.
├── middleware/     # joi data validation and jwt auth.
├── routes/         # routing logic.
├── service/        # business logic.
│   └── jobs/       # cronjob scripts.
├── swagger/        # swagger endpoint documentation setup files
├── utils/          # Winston logging.
├── app.js          # entry point
└── server.js       # server setup
```

#### Frontend File Structure

```text
mern_chess/chess/backend/src/
├── api/ # Where the frontend connects to backend api
├── components/ # React components
├── contexts/ # Authentication context for security.
├── hooks/ # React hooks for api calls and querying.
├── pages/ # Page templates
├── App.jsx # Routing
└── main.jsx # Entry point.
```

#### Backend

The backend follows a basic database model <——< service <——< route pattern with some middleware for input validation and logging. For the actual logic, I seperated files into three buckets: user account logic, puzzle logic, and user made puzzle logic. There are 3 db models, 3 service files, and 3 route files setup, seperating the logic between the buckets.

The user made puzzle has crud capabilities and expected crud endpoints which can be accessed on the frontend with authentication, as well as a way to 'publicly' view user made puzzles without authentication. The puzzle logic is for viewing puzzles in the frontend only, as puzzles are retrieved with cronjob scripts in the backend. Finally, the user logic has support for creating users and logging in to the created accounts.

#### Cron Job

There are two fetch scripts to get the daily puzzle. runDailyFetch.js is run manually via node script calling in the cmd line. The other script, dailypuzzleget.js, sets up two cronjobs to attempt to fetch the lichess dailypuzzle. As the lichess dailypuzzle is not updated at the same time every day, I had to have two jobs setup to 'check' if the puzzle was updated or not.

The script gets the puzzle currently in the lichess endpoint, and if it already matches a puzzle that is in the database, it ignores it. If it doesn't match, it will add it to the database, then call delete oldest puzzle. As this is just a toy app, it was important to make sure the database size was self regulated.

#### Security

To support account creation and login, I used jsonwebtokens to track tokens during sessions, allowing for user authenticated required actions and page views. The passwords are hashed before being saved to the database and are used in the frontend for verification. All user input is sanatized and most are type validated with joi schemas. In total, this app is not spectacualrly security focused, but it has a basic support for expected website security.

#### Frontend

The frontend uses react component based structure with pages, components, state hooks, and api. The flow of logic is as follows: pages <--< components <--< hooks <--< api. Sometimes inside a component, however, I did call api functions instead of using hooks in the event that a state wasn't necessary.

The frontend uses the react-chessboard library and chess.js to handle the chess logic and board state visualization: however, in order to make user created puzzles, I had to write the logic to input the puzzle information in the front end. The frontend also has token conditional pages and actions, such as creating user puzzles, deleting user puzzles, and viewing all self made puzzles.

The frontend also has a page to view all user made puzzles that all uses can access: however, users are not able to do user restricted actions, such as delete those puzzles. There is also a scoreboard to view the top five users and their scores, you get a point for each puzzle you solve.

### Testing, CI/CD, and Deployment

#### Testing

Testing currently includes unit testing and integration testing using the jest library. Unit tests make sure both expected failures and expected success cases are operating correctly in the logic layer and the integration tests make sure that the API is able to send data successfully. There exists unit tests for every utilized business logic function, both for expected passes and failures, as well as integration tests for each utilized API endpoint. These make sure that every update to the codebase will not destroy the base functionality of the application.

#### CI/CD

CI/CD is done with GitHub actions so that each Github push will test and lint the backend, create new docker images on the server for the front end and backend, and the images will then be deployed to the server. On the website server there is a self-hosted GitHub runner that receives Github actions to pull the updated docker image from Dockerhub and the deploy the built images. This easy GitHub actions to Docker to server pipeline ensures frictionless CI/CD.

#### Deployment

The website server is hosted through Digital Ocean, where a droplet is setup and configured with a self hosted GitHub runner to handle the CI/CD aspects of deployment. The server docker images are built on each GitHub push that passes linting and testing, ensuring faulty code is never deployed. Website IP masking and security from external attacks are prevented through the use of Cloudflare and a Digital Ocean firewall.
