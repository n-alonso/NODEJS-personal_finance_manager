# backend_personal-finance-manager

Server: https://pfm-api.herokuapp.com/  
NOTE: Heroku's deployment and thus url are currently not working as of Heroku's retirement of it's free tier.

## Introduction

Personal Finance Manager project to set your salary, manage your spending limits per categories or [envelopes](https://www.thebalance.com/what-is-envelope-budgeting-1293682), and add expenses that can automatically subtract their amounts from their respective categories.  

Tech-stack: 
 * Implementation:
    * Node.js 
    * Express
 * Testing:
    * Mocha
    * Chai
    * Supertest
 * Infrastructure:
    * Heroku
    * PostgreSQL 
 * Documentation:
    * OpenAPI Specification (OAS)

## Table of Contents

1. [Installation](#installation)
2. [Documentation](#documentation)
   * [Quickstart Guide](#quickstart-guide)
   * [OpenAPI Specification](#openapi-specification)
   * [Database](#database)
   * [Constraints](#constraints)
   * [Testing Suite](#testing-suite)

## Installation

This project assumes that you have Node.js properly installed in your computer.
To install it, follow these steps:
 * Clone this project locally
 * Navigate to the local repository `cd <file_path>`
 * Install this project's dependencies `npm install`
 * Run the project `npm start` or `npm start-dev` for Nodemon
 * Test the project `npm test` 

## Documentation

### Quickstart Guide

1. Get familar with the API via exploring the [OpenAPI Specification](https://pfm-api.herokuapp.com/ui/)
2. First you will need to update your Salary:
   ```
   curl --location --request PUT 'https://pfm-api.herokuapp.com/salary' \
   --header 'Content-Type: application/json' \
   --data-raw '{
       "amount": {{your_salary}}
   }'
   ```
   The response will include the updated salary.
3. Now that you have set your Salary, you will need to create your expense categories:
   ```
   curl --location --request POST 'http://localhost:3000/envelopes' \
   --header 'Content-Type: application/json' \
   --data-raw '{
       "name": {{envelope_name}},
       "spending_limit": {{envelope_limit}},
       "spending_available": {{envelope_available}}
   }'
   ```
4. Start logging expenses:
   ```
   curl --location --request POST 'http://localhost:3000/expenses' \
   --header 'Content-Type: application/json' \
   --data-raw '{
       "amount": {{expense_amount}},
       "envelope_id": {{envelope_id}},
       "description": {{expense_description}}
   }'
   ```
5. Next steps: Import this project's OpenAPI Specification into Postman to generate a Postman Collection.

### OpenAPI Specification

Please discover this API via it's [Swagger UI](https://pfm-api.herokuapp.com/ui/) represented OpenAPI Specification

### Database

Explore the [Database Schema](https://dbdiagram.io/d/62b8326969be0b672c421b5d):  
![image](https://user-images.githubusercontent.com/63936366/179350799-3e0cbb19-a7de-474d-be84-e4cb92d3d7e3.png)

### Constraints

* All properties within the schemas are mandatory for all endpoints and methods exept `id`
* You are not expected to provide `id` in any endpoint
* All properties provided must have values with correct data types
* Cannot create an already existing resource
* `salary.amount` cannot be lower than `0`
* The sum of all `envelopes.spending_limit` cannot be higher than the value of `salary.amount`
* When creating/updating an envelope, the value of `spending_available` cannot be higher than the value of `spending_limit`
* Deleting an envelope will delete all related expenses
* When creating an expense the `amount` will be deducted to the realted `envelope.spending_available`. The result of that operation cannot be lower than `0`

### Testing Suite

It tests the following cases:  
* /salary  
   * GET  
     * ✔ Retrieves a salary object  
     * ✔ Salary has the right schema  
     * ✔ Salary is above `0`  
   * PUT  
     * ✔ Retrieves the salary in the response  
     * ✔ Updates `salary.amount` to the provided one  

* /envelopes  
   * GET  
     * ✔ Retrieves an array  
     * ✔ Envelopes have the right schema  
   * POST  
     * ✔ Retrieves the envelope in the response  
     * ✔ Fails if envelope does not have the right schema  
     * ✔ Fails if `envelopes.name` already exists  
     * ✔ Fails if `spending_available` > `spending_limit`  

* /envelopes/id  
   * GET  
     * ✔ Retrieves an object  
     * ✔ The envelope has the right schema  
   * PUT  
     * ✔ Updates the `name` to the provided one  
     * ✔ Fails if envelope does not have the right schema  
     * ✔ Fails if `spending_available` > `spending_limit`  
   * DELETE  
     * ✔ Deletes the right envelope  

* /expenses  
   * GET  
     * ✔ Retrieves an array  
     * ✔ Expenses have the right schema  
   * POST  
     * ✔ Retrieves the expense in the response  
     * ✔ Fails if expense does not have the right schema  

* /expenses/id  
   * DELETE  
     * ✔ Deletes the right expense  
