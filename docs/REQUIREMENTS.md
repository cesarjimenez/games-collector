# Context & Objective
We want to develop an HTTP service that allow users to keep track of their videogames collection.

There will be two types of users:
* Customers: will track their collections via the service
* Admins: will maintain the internal database of available games

The basic usage loop will be like this:
* The user will request the list of all available videogames in the platform
* Then it will use the ID of one videogame to add it to their collection
* The user will list their collection


# Functional Requirements (User Stories)

## Customer User Stories

[REQ-001]
Given I am a customer, 
When I retrieve the list of all the available games,
Then the service will return a 200 HTTP JSON response with all the available games. 
  The returned list of games must be paginated.
  The customer can filter games by platform and genre.

[REQ-002]
Given I am a customer,
When I add an available game using its ID to my collection,
Then the game will be added to the customer's collection
  and the service will return a 204 empty response.
  Adding a game is an idempotent operation.

[REQ-003]
Given I am a customer,
When I retrieve my collection,
Then the service will return a 200 HTTP JSON response with all the games previously added to my collection
  The list of games will be ordered alphabetically by their name.

## Admin User Stories

[REQ-004]
Given I am an admin,
When I add a new customer,
Then the service will store the new customer in the database
  and the service will return a 204 empty response.
  The service will automatically create a new empty collection for the new customer.
  Adding a customer is NOT an idempotent operation.

[REQ-005]
Given I am an admin,
When I create a new platform,
Then the service will be stored in the database
  and the service will return a 204 empty response. 
  Adding a system is NOT an idempotent operation.

[REQ-006]
Given I am an admin,
When I create a new available game,
Then the system will store the game in the database
  and the service will return a 204 empty response.
  Adding an available game is NOT an idempotent operation.


# Technical Constraints

## Architecture
The service must be coded in Java.
All service inputs and output in the HTTP interface will be in JSON format.
The service must be easily run locally.
The service must use a simple file based database.
The service design must follow an hexagonal architecture.
The code must follow well-know best practices:
* Do not over engineer the solutions.
* Use SOLID patterns.
* Use software design patterns when needed.
* Avoid code duplication.
* Promote composition over inheritance.
* Software components must have high cohesion.
* Software components must be loosely coupled.
* There cannot be dependency cycles between software modules.
* Cyclomatic complexity of classes must be under 8.
* When possible, maximum algorithm complexity must be O(N). Higher complexities must be marked as a problem in the code.

## Security
* No TLS connection is needed.
* Customers will use an API Key to access the service. They will have to send the API Key on every request.
* Customers cannot execute Admin operations.
* Customer can only see their collections, they cannot see other customer's collection.
* Admins will use basic authentication with a fixed username and password that can be configured in the system.


# Data Schema / Interface

## Data Model

* Customer:
    * username - type: String, unique, mandatory, max size: 30

* Platform
    * ID - type: String, unique, mandatory, max size: 30
    * year - type: Integer, mandatory
    * description - type: String, optional, max size: 200

* Game
    * ID - type: String, mandatory, max size: 30
    * platform - ID of a Plaform record, the ID and platform pair must be unique
    * title - type: String, mandatory, max size: 50
    * description - type: String, optional, max size: 200
    * genre - type: Enum, mandatory, allowed values: "shmup", "racing", "platforms"

* Collection
    * ID - username of a Customer record
    * games - list of IDs of a Game record

## Data Model Relationships

* Games and Platforms have a 1:1 relationship. One Game can be developed to one Platform.
* Platforms and Games have a 0:N relationship. One Platform can be assigned to many Plaforms.
* Collections and Games have a 0:N relationship. One Collection can have zero or more Games.
* Games and Collections have a 0:N relationship. A Game can belong to zero or more Collections.

## HTTP interface

REQ-001 GET /games
REQ-002 POST /collection/{GAME_ID}
REQ-003 GET /collection
REQ-004 POST /customers
REQ-005 POST /platforms
REQ-006 POST /games
        

# Edge Cases & Error Handling
Failure COMMON.A: If a customer don't send their API Key or the API is not found in the database, a 401 error response will be returned.
Failure COMMON.B: If an admin don't send their username and password or the username or password is not found in the database, a 401 error response will be returned.
Failure REQ-002.A: If the ID of the game is not found in the database, a 422 JSON error response will be returned. The response will inform the user that the ID is not valid.
Failure REQ-004.A: If the customer already exists, a 409 JSON error response will be returned. The response will inform the user that the customer already exists.
Failure REQ-005.A: If the platform already exists, a 409 JSON error response will be returned. The response will inform the user that the platform already exists.
Failure REQ-006.A: If the game already exists, a 409 JSON error response will be returned. The response will inform the user that the game already exists.
Failure REQ-006.B: If a non existing platform is given when creating a game, a 422 JSON error response will be returned. The response will inform the user that the platform is invalid.
Failure REQ-006.C: If an invalid genre is given when creating a game, a 422 JSON error response will be returned. The response will inform the user that the genre is invalid.
