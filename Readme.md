# FSND: Capstone Project

## Content

1. [Motivation](#motivation)
2. [Setup Project locally](#start-locally)
3. [API Documentation](#api)
4. [Authentification](#authentification)

<a name="motivation"></a>
## Motivations & Contents

This is the final project of the `Udacity-Full-Stack-Nanodegree` Nanodegree. Acter learning a lots of things like Flask, SQL-Alchemy, RBAC, database migration and security, and best practices for API development. I'm happy to share it with you.

I used the following `dependencies` for this project

1. Data modeling with `postgres` & `sqlalchemy` (see `models.py`)
2. API with CRUD functionality `Flask` (see `app.py`)
3. Automated testing using `Unittest` (see `test_app`)
4. Authorization & Role based Authentication with `Auth0` (see `auth.py`)
5. Deployment on `Heroku`

**More about dependencies**
`postgres`: I used postgres as the database backend and heruko provies a easy to setup postgres server as well

`sql-alchemy` : Using Flask SQL Alchemy ORM, our project can run on any SQL based dataset whether it be Oracle, SQLite ot other ones. Sql alchemy ORM gives flexibilty to our code in terms of querying and manipulating database. 

`Flask` : I used Flask as a backend of my choice. It's a lightweight framework for backend development.

`Unittest`: Automation testing saves us the most time and I chose Unittest for this cast.


<a name="start-locally"></a>
## Setup Project locally

Make sure you `cd` into the correct folder (with all app files) before following the setup steps.
Also, you need the latest version of [Python 3](https://www.python.org/downloads/)
and [postgres](https://www.postgresql.org/download/) installed on your machine.

To start and run the local development server,

1. Initialize and activate a virtual env (I used venv):
  ```bash
  $ python venv ./venv 
  $ source venv/scripts/activate
  ```

2. Install the dependencies:
```bash
$ pip install -r requirements.txt
```


3. Change database config so it can connect to your local postgres database
- Open `config.py` and change db_setup values as per your requirements and postgres installation
 ```python
db_setup = {
    "database_name_production" : "capstone_db",
    "database_name_test" : "capstone_test_db",
    "user_name" : "postgres", # default postgres user name
    "password" : "postgres", # if applicable. If no password, just type in None
    "port" : "localhost:5432" # default postgres port
}
```


4. Setup Auth0
To check with AuthO service, enter your `jwt-token`s in `config.py` file in the `dict` mentioned below;
```python
bearer_tokens = {
    "actor": "Bearer <JWT TOKEN>"",
    "director" : "Bearer <JWT TOKEN>"
}
```

5. Run the development server:
  ```bash 
  export FLASK_APP=app.py
  export FLASK_ENV=development
  flask run
  ```

6. (optional) To execute tests,
first create a test database with the name of your choice and load it with data located in  file `capstone_db_test.psql`

or you can follow instructions given below:
```bash
# set up test database 
createdb  capstone_test_db
psql capstone_test_db < capstone_db_test.psql

# run test
python test_app.py
```
If you want to run the test again, you'll have to `drop` your test database and run the above commands again
or you can these instructions
```bash
dropdb capstone_test_db && createdb capstone_test_db
psql capstone_test_db < capstone_db_test.psql
```
```python
python test_app.py
```
and a successfull test run will result in output looking like:
```bash
$ python test_app.py
...............
----------------------------------------------------------------------
Ran 15 tests in 4.635s

OK

```
## API Documentation
<a name="api"></a>

This section deals with using `endpoints`, thier required `requests` and `responses`

### Base URL

**_https://**

### Authentication

Please see [API Authentication](#authentication-bearer)

### Available Endpoints

Here is a short table about which ressources exist and which method you can use on them.

                          Allowed Methods
       Endpoints    |  GET |  POST |  DELETE | PATCH  |
                    |------|-------|---------|--------|
      /actors       |  [x] |  [x]  |   [x]   |   [x]  |   
      /movies       |  [x] |  [x]  |   [x]   |   [x]  |   

### How to work with each endpoint

Click on a link to directly get to the ressource.

1. Actors
   1. [GET /actors](#get-actors)
   2. [POST /actors](#post-actors)
   3. [DELETE /actors](#delete-actors)
   4. [PATCH /actors](#patch-actors)
2. Movies
   1. [GET /movies](#get-movies)
   2. [POST /movies](#post-movies)
   3. [DELETE /movies](#delete-movies)
   4. [PATCH /movies](#patch-movies)

Each ressource documentation is clearly structured:
1. Description in a few words
2. `curl` example that can directly be used in terminal
3. More descriptive explanation of input & outputs.
4. Required permission
5. Example Response.
6. Error Handling (`curl` command to trigger error + error response)

# <a name="get-actors"></a>
### 1. GET /actors

Get all actors

```bash
$ curl -X GET https://udacapfsnd.herokuapp.com/actors
```
- Fetches a list of dictionaries of actors
- Request Headers: **None**
- Requires permission: `read:actors`
- Returns: 
  1. List of dict of actors with following fields:
      - **integer** `id`
      - **string** `name`
      - **string** `gender`
      - **integer** `age`
  2. **boolean** `success`

#### Example response
```js
{
  "actors": [
    {
      "age": 26,
      "gender": "Male",
      "id": 1,
      "name": "Popye"
    }
  ],
  "success": true
}
```

# <a name="post-actors"></a>
### 2. POST /actors

Add new Actor into database.

```bash
$ curl -X POST https://udacapfsnd.herokuapp.com/actors
```

- Request Arguments: **None**
- Request Headers: (_application/json_)
       1. **string** `name` required
       2. **integer** `age` 
       3. **string** `gender` 
- Requires permission: `create:actors`
- Returns: 
  1. **integer** `id from newly created actor`
  2. **boolean** `success`

#### Example response
```js
{
    "created": 7,
    "success": true
}

```
#### Errors
If you try to create a actor without required value like `name`,
it will throw a `400` error:

```bash
$ curl -X GET https://udacapfsnd.herokuapp.com/actors>
```

will return

```js
{
  "error": 400,
  "message": "actor name missing from request",
  "success": false
}
```

# <a name="patch-actors"></a>
### 3. PATCH /actors

Edit details of an existing Actor

```bash
$ curl -X PATCH https://udacapfsnd.herokuapp.com/actors/<actor_id>
```

- Request Arguments: **integer** `id of actor you want to update`
- Request Headers: (_application/json_)
       1. **string** `name` 
       2. **integer** `age` 
       3. **string** `gender`
- Requires permission: `edit:actors`
- Returns: 
  1. **boolean** `success`
  2. List of dict of actors with following fields:
      - **integer** `id`
      - **string** `name`
      - **string** `gender`
      - **integer** `age`

#### Example response
```js
{
    "actor": [
        {
            "age": 21,
            "gender": "Female",
            "id": 3,
            "name": "Scarlet White"
        }
    ],
    "success": true,
}
```
#### Errors
If you try to update an actor with an invalid id it will throw an `404`error:

```bash
$ curl -X PATCH https://udacapfsnd.herokuapp.com/actors/<actor_id>
```

will return

```js
{
  "error": 404,
  "message": "Resource id not found.",
  "success": false
}
```
Additionally, an `PATCH` request with no body will result in an following error.

```js
{
  "error": 400,
  "message": "Bad Request, no content in request body",
  "success": false
}
```

# <a name="delete-actors"></a>
### 4. DELETE /actors

Delete an existing Actor

```bash
$ curl -X DELETE https://udacapfsnd.herokuapp.com/actors/<actor_id>

- Request Arguments: **integer** `id from actor you want to delete`
- Request Headers: `None`
- Requires permission: `delete:actors`
- Returns: 
  1. **integer** `delete : if of deleted actor`
  2. **boolean** `success`

#### Example response
```js
{
    "delete": 5,
    "success": true
}

```
#### Errors
If you try to delete actor with an invalid id, it will throw an `404`error:

```bash
$ curl -X DELETE https://udacapfsnd.herokuapp.com/actors/<actor_id>
```

will return

```js
{
  "error": 404,
  "message": "Resource id not found",
  "success": false
}
```

# <a name="get-movies"></a>
### 5. GET /movies

Get all movies

```bash
$ curl -X GET https://udacapfsnd.herokuapp.com/movies
```
- Fetches a list of dictionaries of movies
- Request Headers: **None**
- Requires permission: `read:movies`
- Returns: 
  1. List of dict of movies with following fields:
      - **integer** `id`
      - **string** `title`
      - **genre** `genre`
      - **date** `release_date`
  2. **boolean** `success`

#### Example response
```js
{
  "movies": [
    {
      "id": 2,
      "release_date": "Sun, 16 Feb 2020",
      "genre" : "action",
      "title": "Deadpool"
    }
  ],
  "success": true
}

```


# <a name="post-movies"></a>
### 6. POST /movies

Add a new Movie into database.

```bash
$ curl -X POST https://udacapfsnd.herokuapp.com/movies
```

- Request Arguments: **None**
- Request Headers: (_application/json_)
       1. **string** `title` required
       2. **date** `release_date` 
       3. **string** `genre`
- Requires permission: `create:movies`
- Returns: 
  1. **integer** `id of movie created `
  2. **boolean** `success`

#### Example response
```js
{
    "created": 5,
    "success": true
}
```
#### Errors
If you try to create a movie without required value like `title`,
it will throw a `400` error:

```bash
$ curl -X GET https://udacapfsnd.herokuapp.com/movies
```

will return

```js
{
  "error": 422,
  "message": "movie title missing from request.",
  "success": false
}
```

# <a name="patch-movies"></a>
### 7. PATCH /movies

Edit an existing Movie

```bash
$ curl -X PATCH https://udacapfsnd.herokuapp.com/movies/<movie_id>
```

- Request Arguments: **integer** `id from movie you want to update`
- Request Headers: (_application/json_)
       One or more from these
       1. **string** `title` 
       2. **date** `release_date` 
       3. **genre** `genre`
- Requires permission: `edit:movies`
- Returns: 
  1. **integer** `id of updated movie`
  2. **boolean** `success`
  3. List of dict of movies with following fields:
        - **integer** `id`
        - **string** `title` 
        - **string** `genre`
        - **date** `release_date` 

#### Example response
```js
{
    "movie": [
        {
            "id": 1,
            "release_date": "Sun, 16 Feb 2020 00:00:00 GMT",
            "title": "Test Movie 123"
        }
    ],
    "success": true
}

```
#### Errors
If you try to update an movie with an invalid id it will throw an `404`error:

```bash
$ curl -X PATCH https://udacapfsnd.herokuapp.com/movies/<movie_id>
```

will return

```js
{
  "error": 404,
  "message": "Resource id not found.",
  "success": false
}
```
Additionally, an `PATCH` request with no body will result in an following error.

```js
{
  "error": 400,
  "message": "Bad Request, no content in request body",
  "success": false
}
```

# <a name="delete-movies"></a>
### 8. DELETE /movies

Delete an existing movie

```bash
$ curl -X DELETE https://udacapfsnd.herokuapp.com/movies/<movie_id>
```

- Request Arguments: **integer** `id from movie you want to delete`
- Request Headers: `None`
- Requires permission: `delete:movies`
- Returns: 
  1. **integer** `id from deleted movie`
  2. **boolean** `success`

#### Example response
```js
{
    "deleted": 5,
    "success": true
}

```
#### Errors
If you try to delete movie with an invalid id, it will throw an `404`error:

```bash
$ curl -X DELETE https://udacapfsnd.herokuapp.com/movies/<movie_id>
```

will return

```js
{
  "error": 404,
  "message": "Resource id not found",
  "success": false
}
```

# <a name="authentification"></a>
## Authentication with Roles and Premissions

All API Endpoints are decorated with (Auth0)[https://auth0.com/] permissions. 
- permission, as the names sugests give authority to the permission holder of the resources defined in the permission

- roles, are a set of permissions which can be assumed by an resource or user. 

In this API I used Auth0 authorization with the premissions and roles defined below

### Permissions
Here's a list of our permissions

**ROLE**             **Description**

`post:actors`	  -  can add a new actor	

`post:movies`	  -  can add a new movie	

`patch:movies`	  -  can update existing movie details	

`delete:actors`	  -  can delete actors	

`delete:movies`	  -  can delete movies	

`patch:actors`	  -  can update existing actor details

Now these permisions can either be assigned directly to `users`, or to `roles`.

### Roles

These are our Roles in this project
**Name**	**Description**	
Actor	    movie actor	
Director	movie director	

Each role has different `permissions` assumed by them which are provided below

**Permissions: Actors**

`delete:actors`	    can delete actors	

`patch:actors`  	can update existing actor details	

`post:actors`	    can add a new actor	


**Permissions: Director**

`delete:actors`	    can delete actors		

`delete:movies`	    can delete movies		

`patch:actors`  	can update existing actor details

`patch:movies`  	can update existing movie details

`post:actors`	    can add a new actor		

`post:movies`	    can add a new movie	

### **Note**: Setting up Auth0 Account and getting JWT-Tokken:
- To get started with Auth0 authentication, create you account on [their website](https://auth0.com/)
- Create an application as per your requirement
- Create an API, and set permissions mentioned above in permissions section of API. 
- In user management, create roles and assign permissions likes of which I mentioned above.
- Create user on auth web console or by signing up from browser with url like:

https://{DOMAIN}/authorize?audience={API_AUDIENCE}&response_type=token&client_id={CLIENT_ID}&redirect_uri={http://127.0.0.1:5000/login (in my case) or URL_CALLBACK (one chosen by you)}
- after success sign up in the url you will see key value pair with key being `token` and value being `<a-JWT-token>. Take note of this `JWT-token` as this will be used by the User to make interact with API by adding this token in header of requests.

- Assign the roles you created above to the User using web console.

- you are now ready to make requests that are permited to the user as per the roles asigned

## Bonus: Deployment in Heroku
Follow these steps to deploy on Heroku. For those who are not aware of Heroku

It's platform which is very popular among students, startups and small scale companies to host their API's and Websites.

-It's very easy to get started and takes care of most things behind the scenes for you.

- The documentaion is also very well crafted and easy to understand

**Steps to deploy**
After your app is ready and working great locally, it's now time to take it to the world.

Before getting started make sure:
- You have a `requirements.txt` file in your project to install dependencies on heroku server. you can create one by using command `pip freeze > requirements.txt`

- You have a `Manager.py` file in your project root as well.

We're ready now. Let's get started

1. Create account on [Heroku](https://www.heroku.com/platform)
2. We'll do our deployment using commandline so get yours as per your OS from [here](https://devcenter.heroku.com/categories/command-line)
3. Set up `Procfile` file in root directory. Because our Flask is lightweight is not strong enough to work run as production envionment. we'll `Gunicorn webserver`. 
  - Install it using `pip install gunicorn`
  - In your `Procfile` add this text `web: gunicorn app:app`, make sure your application is housed in app.py
4. Now Using Cli login to your heroku account.Using `heroku login`
5. Create an app on heroku 
  - use command `heroku create name_of_your_app` to create an app on heroku. This will return a git url.
6. Add git remote for Heroku to local repository
  - Using the git url from above, in terminal run: `git remote add heroku heroku_git_url`
7. Add `postgresql` resource to our app
  - In terminal use `heroku addons:create heroku-postgresql:hobby-dev --app name_of_your_application`
8. Configure environment variables
  - Go to Heroku Dashboard > you app > application settings
  - Reveal your `config variables`
  - Enter your environment variables in key value form there.
Note: You can use thse environment variables in your app using:
`os.environ.get('NAME_OF_ENV_VAR')`
9. We're ready let's push our code
 - using `git push heroku master`
10. App will be build and deployed. Now we have to run migrations.
  - In terminal run `heroku run python manage.py db upgrade --app name_of_your_application`

Great, app will is now a live application. 