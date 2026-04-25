---
layout: default
title: MERN App
permalink: /projects/ENG_Spring/
---

<div id="content"></div>

<h1>Spring Project</h1>

Intro
A basic application to track items an employee may have. Automatically sends email to boss users when an item needs servicing or renewal. 
The website is deployed via digital ocean on the link below.

https://itemhealtherm.tech/

The goal of this project was to familiarize myself with the spring boot platform and to build something similar to a business application that might be built at a SIER company. I hope that this app shows I am ready to build business applications.
The app has a scheduler that runs daily after midnight. It checks each registered item in the database and compares it to the new day: if an object requires some form of maintenence then an automatic email is sent to whoever is the boss of the item's owner. These emails are captured via Mailhog so that emails are not actually being sent out. The employee data and item data can also be downlaoded as an excel file.
This app has a working CI/CD pipeline using github actions docker, and unit and integration testing to build test and push each deployed change to the digital ocean's droplet (what digital ocean calls an instance of a server). The database used is postgres, has a mybatis mapper to map sql statements to the service layer, and Flywaydb to run db migrations. It uses the baked in spring routing and security for the rest of the backend. The frontend is thymeleaf, JS, html, and css served on the same spring-boot application making for an easy to configure single app build.


Database Diagram
<img src="{{ '/assets/css/img/ERM_ER_Chart.jpg' | relative_url }}" 
     alt="DB Architecture" 
     class="large-figure">
<br/>

System Diagram
<img src="{{ '/assets/css/img/ERM_Architecture_Diagram.jpg' | relative_url }}" alt="DB Architecture" class="large-figure"/>

Project Walkthrough System Architecture
The webapp is built in one monolith architecture as the frontend uses the lightweight Thymeleaf framework, some JS scripting, and html and css only. The main purpose for this app was for me to try spring-boot dev for backend usage so a basic frontend was fine.

Packages Used
Security : Bcrypt, Spring-boot security
Utility: Scheduler, Spring-mail, Jackson, Mybatis, Flywaydb, Mailhog
Testing: Spring-security testing, Junit
Frontend: Thymeleaf

Package Explination
Security
Spring-security: Spring-boot's baked in security, utilizing a security chain to authenticate requests. For the app, only boss users are allowed to do crud and view certain pages. This meant a login with password, password hashing, and authentication needs to be setup in the app. Spring-security served this purpose.
bcrypt: Hashing package for encrypting user passwords.

Utility:
Scheduler script: An automatic script runner used to check for expiring or needing to be updated items. If it finds one, it sends an automatic email to the boss user of the items owner.
Spring-mail: For sending the mail used in the scheduler-job script.
Mailhog: Captures the emails being sent from the server.
Jackson: JSON Oobjectmapper to process JSOn from front end to backend.
Mybatis: SQL mapper to make and connect SQL commands from backend service-layer to DB
Flywaydb: Run db migrations
apache-poi: For exporting data to excel sheet

Testing
Junit: Test runner and assertions.
Mockito: Mocking library.

Frontend
Thymeleaf: Lightweight frontend library that handles templatet and fragments.

File structure

|ERM-Employee-Item-Health-App/
├──.devcontainer/ # Build settings for deployment.
├──.github/workflows/ # Github actions.
├──logs/ # Where logs end up.
├──Dockerfile/ # Dockerfile build setup
├──tests/ # Testing setup teardown files
├──docker-compose.yml/ # Docker build settings
|──Everything else in root: Standard spring-boot build files
├──src/ # The source code.
| └──test/ # Where the testing suite is, unit testing and integration testing.
| └──main/ #
|   └────resources/ # Front end and db migration files
|     └────db/migrations/ # Flyway migration files for DB setup
|     └────mappers# MyBatis sql mapping files
|     └────static/js # JS script for frontend
|     └────templates # HTML pages
|     └────application.yml/propertiex/logback # settings setup, logs
|   └────java/com/healthapp/itemhealth/ # Backend
|     └────config # Flyway config file.
|     └────controller # Controller layer.
|     └────exception # Exception handler.
|     └────mapper # Connect Mybatis to backend service layer
|     └────model # Model layer
|     └────security # Spring-security filter chain.
|     └────service # service layer
|       └────health # health template logic for health service
|         └────scheduler # Scheduler scripts
|     └────ItemhealthApplication.java # App entry point 
  
Backend

Database
I choose to use postgres for this application to match what SIER companies most often use and to learn how to impliment postgres. I needed a relational database as there are quite a number of join statements needed to match the employees with the items.

Model
The models for the DB. I implemented primary and foreign keys for table joins in mybatis, the main foreign key being the employee_id attribute. A boss is an employee and subordinates are employees. The healthreport is for setting up the automated email, it is initialized and collects information when the scheduled health service check is run automatically. IDCard, Laptop, and Cars are items assigned to an employee. The complete ER table can be seen above.


Flywaydb
For running database migrations for consistency across deployments. It sets up the schemas for the models in the database and initializes it, and also injects some dummy data for viewing.

Mybatis (resourses mapper)
Raw sql is mapped to methods here. It is mostly made up of crud logic that has some table joins mixed in when dealing with boss, subordinate, employee logic. All the items have the foreign key employee_id which connects an employee to an item.

Mapper (backend)
Mybatis Java interface, connects Mybatis method to service layer.

Service
Crud operations triggered from the routes / api endpoints to run the mybatis methods. Also includes authorization checks to make sure only boss users are able to change data.

Health service
The logic that checks the dates of each item to see if they require updating or renewal, and sends an email out if they do. The health service check can be triggered manually on the boss dashboard as well.

Scheduler
The app currently uses a fixed rate counter to run the health check service automatically every day, and the clean up service every five days. This ensures the mailhog mailbox never becomes too full but there are still enough emails sent automatically so that mailhog can capture and show them.

Excel service
Uses apache-poi to export data to an excel sheet. The excel sheet can be downloaded from the frontend by clicking a button in the boss dashboard.

Email service
Spring-mail to send automatic emails should an item need to be renewed or updated. Sends to the email address of the boss of the employee with the expiring item. Mailhog captures this and displays it within the view email frontend view, accessible from the boss dashboard.

Controller
Backend rest API point, connect frontend to backend. Contains input validation. The healthcontroller.java file is the frontend thymeleaf page router.

Security
Spring-security, has security filter chain and authorization logic. Redirects to login page if not logged in. Passwords are hashed with bcrypt before being saved to the data base.

Frontend
Thymeleaf, fragments, css, and js script to handle logic. All crud applications are handled by the JS and sent to the controller, which does input validation and authorization testing.

Testing
Unit testing and integration testing. Test good path and bad paths. Uses spring-test for testing and Junit for mocking models and functions. Each endpoint is tested to make sure the app works.

CI/CD/Deployment
The project has a CI/CD pipeline handled by github actions that tests, dockerizes, and deploys to the cloud server. The website server is hosted through Digital Ocean, where a droplet is setup and configured with nginx. Website IP masking and security from external attacks are prevented through the use of Cloudflare and a Digital Ocean firewall.