---
author:
- Irene Pérez, Carlos Borrego, Eloi Puertas
date: Febrer 2021
title: Pràctica 2 - Flask and Vue application (Software distribuït)
---

Introduction 
============

In this practice we are going to develop a web page able to show
information about artists and different festivals, concerts or other
events that they play in. But first of all we are going to develop an
API able to give us the needed information in a known format (similar to
API that you have tested in previous exercises). In this case we also we
are going to develop some features more related to data management like
add, delete or modify data stored.

For this purpose we are going to use python library called Flask. Flask
is a lightweight web application framework with specialized API
development extension called Flask-RESTful. This its flexibility and
versatility Flask has become one of the most popular web frameworks in
Python.

IMPORTANT 
---------

This guided exercise assume that you already have some Python 3 version
installed and you are familiar with package installation using pip.

Note that pipcommand showed in this tutorial will correspond to pip3 or
pip3.7 depending of how many different versions of python you have
installed in your computer.

Notation used in this tutorial: 
-------------------------------

    commands to introduce to Terminal or pieces of Python code

    >>>commands to introduce to Python console

```python
# Piece of code corresponding to some python file. 
from flask import *
```

Session 1
=========

Flask Installation and our first Flask Application
--------------------------------------------------

Flask is an extra Python package that we have to install if we want to
use it:

    pip install flask

Then open PyCharm Editor and Create a Flask project (*Flask\>New
Project*). Select the following options:

![image](figures/newproject.png)

If you are familiar with Python development and you want to isolate
other Flask or Python installation packages you can use virtualenv.
**Don't do it if you have never used it before**.\
After Flask project creation you will see that a file called `app.py`
and some folders that are created by default in the project (we are not
going to use these folders at the moment):

```python
from flask import Flask

app = Flask(__name__)


@app.route('/')
def hello_world():
    return 'Hello World!'


if __name__ == '__main__':
    app.run()
```

You can run this application by execute Run in PyCharm menu. After that
you can visit your fist Flask web application by opening this link in
your web browser: <http://127.0.0.1:5000/>\
You can also create an `app.py` file, copy the previous code on it and
then execute it on the Terminal by doing:\

    python3 app.py

Take a look at previous code line: `app.route(’/’)`. This decorator
indicates in which URL is executed the function that precedes.

### Exercise 1: 

Add this piece of code to `app.py` after `def hello_ world`:

```python
@app.route('/python')
def like_python():
    return 'I like Python!'
```

Rerun your application and try to open the url corresponding to this new
message.

Developing your first API with Flask
------------------------------------

Add a new file to the project called `data.py` and copy this code on it:

``` python
artists = [
    {'id': 0,
     'name': 'Oques Grasses',
     'country': 'Spain',
     'genre': ['REGGAE', 'POP']},
    {'id': 1,
     'name': 'Txarango',
     'country': 'Spain',
     'genre': ['FUSION', 'REGGAE','POP']},
    {'id': 2,
     'name': 'Bad Gyal',
     'country': 'Spain',
     'genre': ['TRAP', 'HIP HOP', 'URBAN']},
    {'id': 3,
     'name': 'Billie Eilish',
     'country': 'USA',
     'genre': ['INDIE', 'POP']}
]

events = [
    {'id': 0,
     'place': 'Festival Cruilla 2020',
     'city': 'Barcelona',
     'country': 'Spain',
     'date': '2020-07-03',
     'artists': []},
    {'id': 1,
     'place': 'Canet Rock 2020',
     'city': 'Canet de Mar',
     'country': 'Spain',
     'date': '2020-07-04',
     'artists': []}
]
```

As you can imagine, this will be the information that we will use to
show desired information throw our API.\
Modify `app.py` file with a line importing this data to our app:

```python
from data import events, artists
```

Also modify the line `app.run()` with:

```python
app.run(port=5000, debug=True)
```

With this line we force our application to port 5000 and indicate that
we are in debug mode or development, which means that we will obtain
more information in case some error happens. This command should be
modified before deploying our application.

### Exercise 2:

Add two new urls to your `app.py` specifying that you use a GET HTTP
request to get the data (add this option `methods=[’GET’]` to
`@app.routeof` with these new functions):

-   http://127.0.0.1:5000/artists: that has to return in JSON format all
    artists (tip: `return ’artists’: artists`).

-   http://127.0.0.1:5000/events: that has to return in JSON format all
    events (same tip can be applied).

Try with requests to validate that returning data is the expected.
Requests allows you to send HTTP requests extremely easily. There is no
need to manually add query strings to your URLs. Just an example in
Python console:

```python
import requests
url = "YOUR_URL"
reponse = requests.get(url)
response.json()
```

On other hand, there is another famous tool to check methods called
Postman, that offers a friendly user interface with which to make HTML
requests:\
<https://www.postman.com/downloads/>

![image](figures/postman.png)

Developing APIs with Flask-RESTful
----------------------------------

Flask-RESTful is an extension for Flask that adds support for quickly
building REST APIs with specialized features. Use Flask alone is a good
option if we only want a web framework or a very basic API but
Flask-RESTFul usage for more complex API is recommended.\
Before to could use Flask-RESTFul and its features you should install
it:\

    pip install flask_restful

The way to specify urls and methods in Flask-RESTful is by classes
called `Resource`. That usually corresponds with and url (with its input
variables as we will see) and in every resource we write the different
types of HTTP requests that we need in each case:

-   GET : used to request information

-   POST: used to add new elements in our data structure

-   DELETE: used to delete elements from our data structure

-   PUT: used to modify existing in our data structure

Usually POST, DELETE and PUT are only used by specialized data managers
and are not available without permissions. That avoids unknown people
from modifying our data.

### Using Resource structure

Let's see this class Resource structure and our new `app.py`:

```python
from flask import Flask
from flask_restful import Resource, Api
from data import artists, events

app = Flask(__name__)
api = Api(app)


class Artist(Resource):

    def get(self, id):
        return {'message': "Not developed yet"}, 404

    def post(self, id):
        return {'message': "Not developed yet"}, 404

    def delete(self,id):
        return {'message': "Not developed yet"}, 404

    def put(self,id):
        return {'message': "Not developed yet"}, 404

api.add_resource(Artist, '/artist/<int:id>')

if __name__ == '__main__':
    app.run(port=5000, debug=True)
```

Notice that now our app is linked to flask\_ restful and its extra
functionalities by: `api = Api(app)`.\
Also notice that now our urls (also know as endpoints) are specified
this way:

     api.add_resource(Artist, '/artist/<int:id>') 

. The part `’/artist/<int:id>’` indicates that we will take an integer
value as input in our Resource methods.\
Then, a valid url that call this Resource should have this format:
`http://127.0.0.1:5000/artist/0`. The request type is going to determine
the type of action we want to perform on our data.\
As you can see with our return we also return a 404 corresponding to a
`"Not found"` http request error indicating that this option is not
available. After developing these methods we should also return 200 if
everything is Ok or 400 (or related) if some problem occurs. We can also
return messages that will help you get more information about possible
problems.\
If you want more information about Http Response codes you can find more
information in this web page (section *HTTP response status codes*):
https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods

### How to manage our data with python

Before showing you how to develop previous class methods we are going to
take a look to Python Console doing some exercises related to our data
managing.\
Open Python Console in our project directory (same folder as `data.py`)
and import the data:

![image](figures/flask1.png)

Take a look at the content of artists and events:

![image](figures/flask2.png)

Notice that artists and events are lists, and every element of them is a
dictionary:

![image](figures/flask3.png)

We can iterate over a list to find desired information. For example,
return a new list with all artists from Spain:

![image](figures/flask4.png)

Return an artist by its id:

![image](figures/flask5.png)

Or return artists of a concrete genre:

![image](figures/flask6.png)

To search elements in a list in python lambda-functions are also used.
Is a compact way to find information in a list.\
For example finding an artist by its id and return None if is not in the
list could be written by:

![image](figures/flask7.png)

Or find artists by gender, that return an empty list if any:

![image](figures/flask8.png)

![image](figures/flask9.png)

You can also add o remove elements from a list by append or pop:

![image](figures/flask10.png)

![image](figures/flask11.png)

It is important to understand that we don't use memory persistence at
the moment and if you close Python Console when you open it again all
modifications you have done to our data will be gone.\
It is also important to know that you could append whatever to artists
or events lists, is up to you to add new information with the correct
format.

Obtain an artist by its id (GET Example)
----------------------------------------

Now that you understand our data structure and how to make a simple
task, it is time to write the `Artist` method `def get(self,id)`.\
As you can imagine we only have to search an artist by his/her id an
return it. Just fill this method with previous examples:

```python
def get(self, id):

    artist = next(filter(lambda x: x['id'] == id, artists), None)
    return {'artist': artist}, 200 if artist else 404
```

You can also use a for clause. We return artist with input id and return
200 if we find it. Otherwise we will return None and 404 code ("Not
Found"). Remember that `if artist else 404` is a short form of returning
200.

### Exercise 3: 

Try to check that get method is returning expected information by using
requests or another kind of API tester.

Add an artist to artists list (POST example)
--------------------------------------------

Let's try a post method which we are going to use to add a new artist to
our data (to our artists list). It is important to understand that new
artist information will be enclosed into POST request body and we have
to get this data using `reqparse`.

```python
from flask_restful import Resource, Api, reqparse #add reqparse to imports
```

```python
parser = reqparse.RequestParser() #create parameters parser from request

#define al input parameters need and its type
parser.add_argument('name', type=str, required = True, help = "This field cannot be left blanck")
parser.add_argument('country', type = str)
parser.add_argument('genre', type = str, action="append") #action = "append" is needed to determine that is a list of strings

data = parser.parse_args()
```

This data variable is going to have all variables added and defined in
the parser, and we can get their values by doing: data\['name'\],
data\['country'\], etc.\
With this information create a new `new_ artist` variable and add it to
artists list. But first of all check if it already exist an artist with
this id. If there is another artist with the same id return a message
with information with this issue and an error code (

    {'message': "Artist with id [{}] Not found".format(id)} 

). Otherwise, add the artist, return the artist added and the OK code.

### Exercise 4: 

Check post methods using requests or other API testing tool by adding
this information to request body:

``` html
{
    "name": "Doctor Prats",
    "country": "Spain",
    "genre": ["REGGAE", "SKA"]
}
```

Remember that the id parameter comes from the url itself.

HOMEWORK
--------

1.  Add into POST method the option to add a new artist without
    specifying an id. We will give him/her the maximum id in the list
    +1. (Hint: modify your code with:

    ```python
    def post(self, id=None):
    ```

    ```python
    api.add_resource(Artist, '/artist/<int:id>', '/artist')
    ```

    Also check if there is an id variable or not before to do anything
    else.

2.  Write a DELETE method that given an id deletes an artist from
    artists list. Return a message indicating if this user has been
    deleted or not and corresponding code.

3.  Write a PUT method that, if this id doesn't exist, create a new
    artist and add it to artists list. Otherwise, modify values of the
    artist with this id with the information in put body request. Return
    the artist created or modified.

4.  Create two new Resource: ArtistList and EventList with a method able
    to show all information in artits and events in '/artists' and
    '/events' urls.

5.  Write GET, POST, DELETE and PUT methods for a Resource called Event
    with

        '/event/<int:id>'

    url. Create new events with empty artists list.

6.  Test and check all developed methods with `requests` or other API
    testing tool.

Session 2
=========

In the last session we learned about some new concepts related to APIs
and how to create different endpoints to create and modify data from our
data store. But as you already noticed, we used dictionary as storage
and this kind of structure doesn't allow us to have data persistence of
our modifications. //

In this session we are going to add data persistence using a database
system defined and managed by Flask modules, modify our GET, POST, PUT
and DELETE methods to manage Events and Artists with data persistance
and create new methods to manage `Artists` contained in
`Events (Events.artists)`. Finally, we also are going to add new
endopoints related to these new methods.

SQLAlchemy: defining data structure using Models
------------------------------------------------

Data models are used in diferent applications that manage data storage
and determine how data can be organized and manipulated. The most
popoular are relational models, which uses table-based format. //

### Configure SQLAlchemy

In order to create models on top of our database, we will use a Python
package named SQLAlchemy. SQLAlchemy allow us to store and retrieve data
using object-oriented objects with its Model class. This class converts
data into Python objects obtaining a code compatible with diferent
relational databases.\
In our project, we will use Flask SQLAlchemy that is an specialized
package related to SQLAlchemy that provides us function specifics for
Flask applications.\
To use this new package we will have to install it:

    pip install Flask-SQLAlchemy

And add some lines of code to our project:

-   Create a `db.py` file in the main project (same folder as `app.py`)
    with this content:

    ```python
    from flask_sqlalchemy import SQLAlchemy

            db = SQLAlchemy()
    ```

-   Import `db` to our `app.py`:

    ```python
    from db import db
    ```

-   Add some configuration parameters to `app.py` with 'SQLALCHEMY\_
    DATABASE\_ URI' to define where data is stored (in our case a SQLite
    file called `data.db` on main project folder) and
    `’SQLALCHEMY_ TRACK_ MODIFICATIONS’` set to False to avoid
    significant overhead that consumes memory. Probably in new Flask
    versions is already set to False by default, but is better to ensure
    it.

Add this configuration after `app = Flask(_ _ name_ _ ):`

```python
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///data.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
```

SQLAlchemy will read our app configuration and automatically connect to
our database.

### Defining ArtistModel

First of all, we are going to create a `models` folder into main project
to get all models files in it.\
In `models` folder create an `artist.py` file where we are going to
define `ArtistModel` class model to interact with `artist` stable in our
API. To import our SQLAlchemy:

```python
from db import db
```

Taking previous sessions data structure as reference, we are going to
create an `artist` table with 4 class variables
(`id, name, country, genre`) corresponding to the different columns from
the table.\
We are going to define `ArtistModel` structure defining table name,
name, type and number of columns, like:

```python
class ArtistModel(db.Model):
    __tablename__ = 'artists' #This is table name

    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(30))
    country = db.Column(db.String(30))
    genre = db.Column(db.Enum(*genres))
```

Take a look to this structure:

-   `id` is defined as `Integer` and `primary_ key= True`. That
    indicates that this value is going to be used to link with other
    tables (in our case a link to `artists` with `events`). This value
    is `UNIQUE` for definition. Otherwise we will not to be sure which
    event is linked to which artist.

-   `name` and `country` are defined as `String` with maximum size
    of 30. It is not mandatory to define a maximum length but it is
    recommended.

-   `genre` is defined as `Enum`, that indicates that it can only have
    the values defined in an enumeration. In our case we are going to
    define this enumeration as a Python tuple:

    ```python
    genres = ('REGGAE', 'POP', 'TRAP', 'HIP HOP', 'ROCK', 'INDIE', 'HEAVY', 'ELECTRONIC', 'OTHER')
    ```

Add `genres` in `artist.py` before ArtistModel definition.\
In this structure version we will only have one genre per artist to
simplify our model (otherwise we should add another table just to manage
this variable).\
Finally we are going to add some more configuration parameters to our
columns:

```python
name = db.Column(db.String(30), unique = True, nullable=False)
```

-   `unique = True`: avoids having two artists with the same name in our
    database

-   `nullable = False`: doesn't allow to have empyt values in this
    column

We also can define some methods related to this table depending on our
needs, but is mandatory to define an `_ _ init_ _` method to define how
objects are created. Add this method to ArtistModel:

```python
def __init__(self, name, country, genre):
        self.name = name
        self.country = country
        self.genre = genre
```

Notice that `id` doesn't need to be initialized, SQLAlchemy is going to
do it for us when we save this kind of data to the database.

### Exercise 1: 

Define also `country` and `genre` as not `nullable`.

### Define EventModel

### Exercise 2: 

1.  Define `EventModel` class in `event.py` located in `models` folder,
    with table named `events` with these parameters:

    -   `id` (Integer and primary\_ key)

    -   `name` (String)

    -   `place` (String)

    -   `city` (String)

    -   `date` (String)

    -   `price` (Integer)

    -   `total_ available_ tickets` (Integer)

2.  Define all these parameters as not `nullable`.

3.  Define its \_ \_ init \_ \_ method.

4.  Define a `UniqueConstraint` to avoid the existance of an event with
    same (`’name’,’date’,’city’`) adding into EventModel:

    ```python
    __table_args__ = (db.UniqueConstraint('name', 'date', 'city'),)
    ```

### Defining relationships between models

Relationships between models in SQLAlchemy are links between two or more
models that allow models to reference each other automatically.\
We are going to define a type of relationship named as "Many-to-many"
relationship. This kind of relation is going to allow us to get the
artists in a concrete event or all events from a concrete artist.

### Exercise 3: 

Take a look at this many-to-many relationship definition and define a
many-to-many relationship between ArtistModeland EventModel. In our case
name of the relationship in EventModel is 'artists' and the backref is
'events'.

### Many-to-many example:
```python
#post.py

tags = db.Table('post_tags',db.Column('post_id', db.Integer, db.ForeignKey('post.id')),
        db.Column('tag_id', db.Integer, db.ForeignKey('tag.id')))
        
class PostModel(db.Model):
    id = db.Column(db.Integer(), primary_key=True)
    title = db.Column(db.String(255))
    text = db.Column(db.Text())
    user_id = db.Column(db.Integer(),db.ForeignKey('user.id'))
    tags = db.relationship('TagModel',secondary=tags, backref=db.backref('posts', lazy='dynamic'))
    
    def __init__(self, title):
        self.title = title
        ...
```

```python
#tag.py

class TagModel(db.Model):
    id = db.Column(db.Integer(), primary_key=True)
    title = db.Column(db.String(255))

    def __init__(self, title):
        self.title = title
```

Migrations: creating /updating our data storage structure
---------------------------------------------------------

In previous sessions we have defined our data store using Models, but to
be able to use it we have to create it. We can make it "by hand" in our
code, creating current tables or modifying them without using SQL
commands. But we can go a step further using Flask-Migrate.
Flask-Migrate provides a way to deal with database schema changes in
SQLAlchemy projects, using only few commands by command-line interface.

### Configure Flask-Migrate

As always, before to use this flask package we have to install it:

    pip install Flask-Migrate

And add some lines in `app.py`:

```python
from flask_migrate import Migrate
```

Make sure that your main file project is called `app.py` because
flask-migrate is looking for this file. Also, our models are imported
there:

```python
from models.artist import ArtistModel
 from models.event import EventModel  #also import table created with many-to-many relationship
```

Add `Migration` and ORM SqlAlchemy initialization after all `app.config`
statements:

```python
migrate = Migrate(app, db)
 db.init_app(app)
```

At this point you can create a resources folder and move all code
related to Resources into two files called `artist.py` and `event.py`.
In next sessions we are going to work on this.\
Make sure that you saved all modifications before executing our migrate
commands.

### Execute migrations

Open terminal using PyCharm or Terminal in main folder API and execute
these commands:

    flask db init

This will add a migrations folder to your application. The contents of
this folder need to be added to version control along with your other
source files.\
You can then generate an initial migration:

    flask db migrate -m "Initial migration".

Then you can apply the migration to the database:

    flask db upgrade

Then, each time the database models change, repeat the migrate and
upgrade commands.\
To see all commands that are available run this command:

    flask db --help

### Adding data

At this point we have an empty data storage ready to use. Before we are
going to use flask shell command line (PyCharm or Terminal) to learn how
to add, modify and delete information from it. Also it is important to
know hoy to build our query to obtain the desired information.\
Open Flask Shell by typing `flask shell` on your Terminal or PyCharm
Terminal (make sure you do it from main project folder). Make sure you
are not running our API at the same time.\
Import our `db`, initialize an app and check some parameters related to
our Models after importing them:

    >>>from db import db
    >>>db
    <SQLAlchemy engine=sqlite:////home/nuria/Projectes/P2_def/data.db>
    >>>app
    <Flask 'app'>
    >>> from models.artist import ArtistModel
    >>> ArtistModel
    <class 'models.artist.ArtistModel'>
    >>> from models.event import EventModel
    >>> EventModel
    <class 'models.event.EventModel'>

Create some artists and events and save them into our data storage:

    >>>new_artist1= ArtistModel("Bad Gyal","Spain","TRAP") 
    >>> new_artist2 = ArtistModel(name="Txarango", country="Spain", genre="REGGAE")
    >>> new_artist2
    <ArtistModel (transient 140417807213288)>
    >>>db.session.add(new_artist1)
    >>>db.session.add(new_artist2)
    >>>db.session.commit()
    >>>new_event1=EventModel("Canet Rock 2020","Platja", "Canet de Mar","2020-07-05",50,300)
    >>>db.session.add(new_event1)
    >>>db.session.commit()

Create some simple queries (`filter_ by`):

    >>> ArtistModel.query.filter_by(name="Txarango").first()
    <ArtistModel 2>
    >>>artist = ArtistModel.query.filter_by(name="Txarango").first()
    >>> artist.name, artist.country, artist.genre
    ('Txarango', 'Spain', 'REGGAE')
    >>> artists = ArtistModel.query.filter_by(country='Spain').all()
    >>> artists
    [<ArtistModel 1>, <ArtistModel 2>]
    >>> artists[0].name, artists[1].name
    ('Bad Gyal', 'Txarango')

Modify an artist and save it:

    >>> artist
    <ArtistModel 2>
    >>> artist.genre = "HIP HOP"
    >>> db.session.add(artist)
    >>> db.session.commit()
    >>>artist = ArtistModel.query.filter_by(name="Txarango").first()
    >>> artist.name, artist.country, artist.genre
    ('Txarango', 'Spain', 'HIP HOP')

Add artists to events:

    new_event1.artists.append(artist)
    db.session.add(new_event1)
    db.session.commit()

Advanced filters:\

    >>> event = EventModel.query.filter_by(id=1).filter(EventModel.artists.any(name='Txarango')).first()
    >>> event.name
    'Canet Rock 2020'

Filters using multiple linked tables (`join`):

    >>> artist = ArtistModel.query.join(ArtistModel.events).filter(EventModel.id == 1).filter(ArtistModel.name == 'Txarango').first()
    >>> artist
    <ArtistModel 2>
    >>> artist.name
    'Txarango'

Filter without specifying `first()` or `all()` are just SQL queries that
SQLAlchemy creates from our filter:

    >>> ArtistModel.query.join(ArtistModel.events).filter(EventModel.id == 1).filter(ArtistModel.name == 'Txarango')
    <flask_sqlalchemy.BaseQuery object at 0x7f98ed6b5518>
    >>> print(ArtistModel.query.join(ArtistModel.events).filter(EventModel.id == 1).filter(ArtistModel.name == 'Txarango'))
    SELECT artists.id AS artists_id, artists.name AS artists_name, artists.country AS artists_country, artists.genre AS artists_genre 
    FROM artists JOIN artists_in_events AS artists_in_events_1 ON artists.id = artists_in_events_1.artist_id JOIN events ON events.id = artists_in_events_1.event_id 
    WHERE events.id = ? AND artists.name = ?

Delete an artist from an event:

    >>> event.artists.remove(artist)
    >>> db.session.add(event)
    >>> db.session.commit()
    >>> artist = ArtistModel.query.join(ArtistModel.events).filter(EventModel.id == 1).filter(ArtistModel.name == 'Txarango').first()
    >>> artist
    >>> artist == None
    True

Delete an artist:

    >>> ArtistModel.query.all()
    [<ArtistModel 1>, <ArtistModel 2>]
    >>> artist = ArtistModel.query.filter_by(id=1).first()
    >>> artist
    <ArtistModel 1>
    >>>db.session.delete(artist)
    >>>db.session.commit()
    >>> ArtistModel.query.all()
    [<ArtistModel 2>]

Close the session and exit from Terminal:

    >>> db.session.close()
    >>> exit()

If you try to do a non allowed action into our data storage, the shell
is going to return an error that usually avoids to apply more actions to
the database. To solve this problem the best way is to roll-back through
this command:

    db.session.rollback()

And always make sure to close session with database before exiting the
Terminal.

### Exercise 4:

Create a python script called `add_ data.py` in our main project folder
adding some artists, events and artists to events to our database
automatically. Make sure that it is correct using flask shell before
executing it.

    python3 add_data.py

To emulate flask shell add these lines at the beginning of the
`add_ data.py` file:

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
#import models here

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///data.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)
db.init_app(app)
```

If some fatal error occurs and you are not able to solve the problem,
try to delete `data.db` and migrations folder and create it again.

Modify our previous Resources: HOMEWORK
---------------------------------------

1.  Create `def json(self):` methods in ArtistModel and EventModel,
    returning its information in json format like:

                {  
                "artist":{
                    "id": 1,
                    "name": "Bad Gyal",
                    "country": "Spain",
                    "genre": "TRAPs"
                    }
                }

                {
                "event": {
                    "id": 1,
                    "name": "Canet Rock 2020",
                    "place": "Platja",
                    "city": "Canet de Mar",
                    "date": "2020-07-05",
                    "artists": [
                        {
                            "id": 2,
                            "name": "Txarango",
                            "country": "Spain",
                            "genre": "REGGAE"
                        }
                    ],
                    "price": 50,
                    "total_available_tickets": 300
                    }
                }

2.  Create some methods to ArtistModel and EventModel to save or delete
    from database functions, in the corresponding Resources:

    ```python
    def save_to_db(self):
                #code_here

            def delete_from_db(self):
                #code_here
    ```

    We can use it like:

    ```python
    artist = ArtistModel('Txarango','Spain','REGGAE')
            artist.save_to_db()
    ```

3.  Create some class methods to ArtistModel and EventModel with more
    queries, for example:

    ```python
    @classmethod
            def find_by_id(cls,id):
                #code here
    ```

    We can use it like:

    ```python
    artist = ArtistModel.find_by_id(3)
    ```

4.  Modify endpoints from session 1 to use new data structure and
    methods created:

    ```python
    api.add_resource(Artist, '/artist/<int:id>', '/artist')
            api.add_resource(ArtistList, '/artists')

            api.add_resource(Event, '/event/<int:id>', '/event')
            api.add_resource(EventList, '/events')
    ```

    Remember to import models into resources and delete all imports
    related to `data.py` (we are not going to use it anymore). This time
    POST and PUT requests will always have all parameters (here some
    examples):

                {
                "name": "Bad Gyal",
                "country": "Spain",
                "genre": "TRAP"
                }

                {
                "name": "Canet Rock 2020",
                "place": "Platja",
                "city": "Canet de Mar",
                "date": "2020-07-05",
                "price": 50,
                "total_available_tickets": 300
                }

    In the case of PUT we modify an element defined by its id, only if
    it exists.\
    Modify `reqparse` to tell that all parameters are required and add
    information in help parameters. Also remember to modify `genre`
    type, that now is a single string. Take into account our Models
    definitions (`unique = True` and `UniqueConstraint`) to avoid errors
    before we try to save it in database.\
    Add `try-catch` when you are trying to save to the database to avoid
    internal errors. But first of all check that all your code is
    correct and you are not trying to commit wrong information to it.

    ```python
    try:
                #saving data
            except:
                return {"message": "Error Description"}, 500
    ```

5.  Create these new endpoints and Resources:

    ```python
    api.add_resource(EventArtistsList, '/event/<int:id>/artists')
    api.add_resource(EventArtist, '/event/<int:id_event>/artist/<id_artist>',
                                '/event/<int:id_event>/artist')

    api.add_resource(ArtistEventsList, '/artist/<int:id>/events')
    ```

    EventArtistsList :

    -   get: return all artist in an event, given its id

    EventArtist:

    -   get: return a concrete artist from and event, given event id and
        artist id.

    -   post: add an artist to a concrete event given all artist
        information structure in a json

    -   delete: remove a concrete artist from a concrete event defined
        by given ids

    ArtistEventsLists:

    -   get: return all events from an artist, given his/her id

    As in point 4), remember to return all the lists of elements in a
    json format with related tag (`events` or `artists`), depending on
    the kind of elements:

                {
                "events":[
                    {
                        "id": 1,
                        "place": "Platja",
                        "city": "Canet de Mar",
                        "date": "2020-07-05",
                        "artists": [
                            {
                                "id": 2,
                                "name": "Txarango",
                                "country": "Spain",
                                "genre": "REGGAE"
                            }
                        ]
                        "price": 50,
                        "total_available_tickets": 300
                    }
                ]
                }

    Remember to take into account all errors and return a message with
    information related to the error ocurred and the corresponding error
    code.

    ![image](figures/errors.png)

6.  Test and check all your endpoints using requests or Postman, and
    ensure that your API works properly.

Session 3
=========

Web design
----------

In this part, we are going to develop the frontend of our application.
At the end of this part, we should see the following design:\
**LOG IN VIEW**\

![image](figures/signin.png)

**EVENTS VIEW WITHOUT USER LOGGED IN**

![image](figures/image002.png)

**EVENTS VIEW WITH USER LOGGED IN**

![image](figures/image003.png)

**CART VIEW**

![image](figures/image004.png)

Introduction to Vue
-------------------

Vue is a progressive framework for building user interfaces, that is,
client side. Although Vue is simpler than React or Angular, it is
extremely powerful and can be used to build advanced applications and
offers to create projects in a structured way. It consists of a set of
optional libraries and third-party libraries, and has a vibrant and
growing community. Also, Vue is becoming really popular and has a bright
future.

Installing Vue
--------------

The current steps have been developed in vue/cli version 4.2.3, node
version 12.16.1 and npm version 6.13.4.\
After installing Vue, let's check your node version:

    node --version

If you don't have node installed yet, or you have a last version, go to
<https://nodejs.org/en/download/> and install the package. To verify
your installation, use the following command lines:

    node --version
    npm --version

In case you are on Linux, you should install Node Version Manager (NVM).
It will allow you to choose a specific node version. To install it, you
can follow this guide:
<https://phoenixnap.com/kb/install-latest-node-js-and-nmp-on-ubuntu>.\
There are some installation methods to install Vue, in this case, we are
going to install it via Vue CLI.

    npm install -g @vue/cli
    npm install -g @vue/cli-init

For more support check the following installation guide:
<https://cli.vuejs.org/guide/installation.html>.\
After installing, check with the command line:

    vue --version

Create and set up an environment project
----------------------------------------

First of all, go to the desired folder where you want to locate the
project. After this, run in the command line

    vue init webpack <name-project>

e.g. vue init webpack frontend. Select the configuration as in the
following image:

![image](figures/image005.png)

You can interact using key arrows, enter and space.\
Once the project has been created, let's pay attention to the main
files. `/src` folder has the following structure:

![image](figures/src.png){width="4cm"}

-   **App.vue**: in charge of rendering components

-   **assets**: where you save any assets like images

-   **components**: all components of the projects. Every component has
    its own html template and JavaScript code

-   **main.js**: initializes and configures Vue app

-   **index.js**: routes components of your project

Hello world in Vue
------------------

To run our first Vue app:

    cd <name-project>
    npm run dev

After running these command lines, go to <http://localhost:8080/> in
your browser.

![image](figures/image006.png)

![image](figures/image007.png)

Create a component
------------------

We have seen how to run a project. Now, we are going to see how to
create our own component. First of all, let's delete the folder `views`
to clean up the project. Then, change the html template in `App.vue`
file as:

```html
<template>
    <router-view/>
</template>
```

Also, create a new component called `Events.vue` in the components
folder. In this file, copy and paste the following code:

```html
<template>
<div id="app">
<h1> {{ message }} </h1>
</div>
</template>

<script>

export default {
  data () {
    return {
      message: 'My first component'
    }
  }
}

</script>
```

As you can see here, we have two blocks called "template" and "script".
As we mentioned before, the first block corresponds to the view in html
and the second one belongs to JavaScript code. In "return" we can
declare the variables we will use in the code. You can also interact
including code references in html template.\
Parallel to that, go to `index.js` to rout our new component:

```html
import Vue from 'vue'
import Router from 'vue-router'
import Events from '@/components/Events.vue'

Vue.use(Router)

export default new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    {
      path: '/',
      name: 'Events',
      component: Events
    }
  ]

})
```

Let's check our new component <http://localhost:8080/>

![image](figures/image008.png){width="9cm"}

Bootstrap
---------

Bootstrap (<https://www.w3schools.com/whatis/whatis_bootstrap.asp>) is
the most popular CSS Framework for developing responsive and
mobile-first websites. It contains CSS- and JavaScript-based design
templates for typography, forms, buttons, navigation, and other
interface components.\
To install it in our project, run the command line:

    npm install --save bootstrap-vue

Beside this, download the compiled files [here](here). Extract folders
in a new folder called bootstrap inside your project:

![image](figures/image009.png){width="4cm"}

After the installation, configure the file `main.js` importing the
Bootstrap:

```html
import BootstrapVue from 'bootstrap-vue'
import '@/../bootstrap/css/bootstrap.css'
import Vue from 'vue'
import App from './App.vue'
import router from './router'

Vue.use(BootstrapVue)
Vue.config.productionTip = false

new Vue({
  router,
  render: (h) => h(App)
}).$mount('#app')
```

Now, we are able to consume the predefined Bootstrap templates.

Methods
-------

Apart from variables, we can also define methods and use them in an html
block. Let's define a variable called `“total_ tickets_ bought”` and a
method to increment this value using a Bootstrap button in `Events.vue`.

```html
<template>
  <div id="app">
    <h1> {{ message }} </h1>
    <button class="btn btn-success btn-lg" @click="buyTickets"> Buy ticket </button>
    <h4> Total tickets bought: {{ tickets_bought }} </h4>
  </div>
</template>

<script>
export default {
  data () {
    return {
      message: 'My first component',
      tickets_bought: 0
    }
  },
  methods: {
    buyTickets () {
      this.tickets_bought += 1
    }
  }
}
</script>
```

As you can see, you can also call methods in a html block by using
\@click="incrementTickets", like in this case. Also, we use \<variable\>
to refer to variables and update them.

![image](figures/image010.png)

### Exercise 1 

Declare a method to subtract the total of tickets bought (including its
button to interact). Also, add a money\_ available and price\_ event
variable and add and subtract the price of each event to money\_
available variable. Use the price and money available you want. Show it
on website.

![image](figures/image011.png)

### Exercise 2 

Deactivate \"buy\" button if money available is not enough to buy one.
On the other hand, deactivate return ticket if the total tickets are 0.
To do this, you can use the property `:disabled="condition"` in the
button.

Connect Vue and Flask
---------------------

### Enable Cross-Origin Resource Sharing (CORS)

CORS is the mechanism to manage cross-origin requests. A request for a
resource from outside of the origin is known as cross-origin request. In
this practice, we will create a frontend application that will request
information from Flask. To enable the access, we have to install the
library flask-cors.

    pip install flask-cors

After this, import the CORS and configure the application:

```python
app = Flask(__name__)
app.config.from_object(__name__)

api = Api(app)


CORS(app, resources={r'/*': {'origins': '*'}})
 
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///data.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False 
migrate = Migrate(app, db)
db.init_app(app)
```

Here, you are granting access to Flask APP from the outside.

### Configure static and template folders

First of all, go to your Vue application and compile the solution with
the following line:

    npm run build

It will create a new folder called **dist** where we can find our
frontend solution (HTML, CSS, JS). Now, our Vue application has the
following structure:

![image](figures/image012.png){width="4cm"}

Now it is time to indicate to Flask the paths to consume:

```python
app = Flask(__name__,
         static_folder="../P2_VUE_WEBPACK/frontend/dist/static",
         template_folder="../P2_VUE_WEBPACK/frontend/dist")
```

And declare a route to render template:

```python
from flask import render_template

@app.route('/')
def render_vue():
    return render_template("index.html")
```

If you run the Flask application and open <http://127.0.0.1:5000/>, you
should see the last Vue configuration but hosted by your Flask APP:

![image](figures/image013.png)

Session 4
=========

Loops
-----

Vue allows to use loops and it is very useful for lists. Imagine we have
a list of events we want to iterate and show the information of every
event separately.\
To do that, create a list of events with some properties in data () -\>
return:

```html
events: [
        {
          name: 'Festival Cruilla 2020',
          artists: [
            {
              'name': 'Bad Gyal'
            },
            {
              'name': 'Txarango'
            },
            {
              'name': 'Estopa'
            }
          ],
          city: 'Barcelona',
          place: 'Parc del Forum',
          date: '2020-07-03',
          price: 100
        },
        {
          name: 'Canet Rock 2020',
          artists: [
            {
              'name': 'Txarango'
            },
            {
              'name': 'Dvicio'
            },
            {
              'name': 'Lola Indigo'
            }
          ],
          city: 'Barcelona',
          place: 'Parc del Forum',
          date: '2020-07-05',
          price: 24
        },
        {
          name: 'Iron Maiden Tour',
          artists: [
            {
              'name': 'Iron Maiden'
            }
          ],
          city: 'Barcelona',
          place: 'Sant Jordi',
          date: '2020-08-22',
          price: 70
        }
      ],
```

Create a v-for loop to visualize every event separately:

```html
<div class="container">
    <div class="row">
      <div class="col-lg-4 col-md-6 mb-4" v-for="(event) in events" :key="event.id">
        <br>
        <h6>{{ event.name }}</h6>
        <div v-for="(artist) in event.artists" :key="artist.id">
           <h5>{{ artist.name }}</h5>
        </div>
        <h6>{{ event.city }}</h6>
        <h6>{{ event.place }}</h6>
        <h6>{{ event.date }}</h6>
        <h6>{{ event.price }} \euro</h6>
      </div>
    </div>
  </div>
```

![image](figures/image004.jpg)

### Exercise 1:

Modify the current code to show events in a structured way. One way
could be using cards
(<https://getbootstrap.com/docs/4.0/components/card/>).\
Moreover, implement the button "Add to cart" and add an image of a
festival you like in every card. This button also has to add the event
bought with its information in a list of events\_ added: \[\]
considering the option this.events\_ added.push(event)\
Don't worry about the number of available tickets. Later on, we will
manage the number of tickets available where we will have to decrease
the number pf bought tickets.

GET Route
---------

Instead of declaring an event list, let's use our API developed in Flask
to GET, POST, PUT and DELETE using Vue. To consume routes from Vue, we
use the axios library:

    npm install axios@0.18.0 -saves

Import axios in the script just after \<script\>:

```html
<script>
import axios from 'axios'

export default {
```

Run your Flask app, and add the following piece of code in methods:

```html
getEvents () {
      const path = 'http://localhost:5000/events'
      axios.get(path)
        .then((res) => {
          this.events = res.data.events
        })
        .catch((error) => {
          console.error(error)
        })
    }
```

Where /events will return the list of all events you already have
registered. E.g.:

![image](figures/image006.jpg)

Also, add created() outside of methods. It allows code to run every time
the web initializes.

```html
created() {
    this.getEvents();
  },
```

This way, you can get the data from your API.

### Exercise 2: 

Configure your application to show the events consumed from `/events`
and delete the list of events created previously.

Cart view
---------

In this view, we will see all the user's orders. Also, the user will be
able to finalize the purchase. This view can be defined as part of
Events.vue, but not in a new component, just in a conditional to show
Event part or Cart part.

![image](figures/image008.jpg)

Before going on with our events view, let's implement an accounts model
to identify a user and save his/her purchased orders.\
First, as you did before with Event and Artist, create a new model
called AccountsModel in model folder. In this case, we are going to use
a One-to-Many relationship. The table "accounts" has the following
columns:

```python
class AccountsModel(db.Model):  
     __tablename__ = 'accounts'

    username = db.Column(db.String(30), primary_key=True, unique=True, nullable=False)
    password = db.Column(db.String(), nullable=False)
    # 0 not admin/ 1 is admin
    is_admin = db.Column(db.Integer, nullable=False)
    available_money = db.Column(db.Integer)
    orders = db.relationship('OrdersModel', backref='orders', lazy=True) 

    def __init__(self, username, available_money=200, is_admin=0):
        self.username = username
        self.available_money = available_money
        self.is_admin = is_admin
```

We are not going to initialize AccountModel with a password. We will see
in next sessions why, but for the next exercise you just need to have a
fake password: initialize the field password in the init function of the
account model by doing `self.password = ’test’` .

### Exercise 3:  

1.  Create a json method returning 'username', 'is\_ admin' and
    'available\_ money'.

2.  Create method to save data in db

3.  Create method to delete data from db

4.  Create a new user via Flask shell, and add it to db:

        >>> from db import db
        >>> from models.accounts import AccountsModel
        >>> test_user=AccountsModel(username='test')
        >>> db.session.add(test_user)
        >>> db.session.commit() 

Once AccountsModel is created, let's create a new model and resource to
save all the purchased events. We will call this OrdersModel. Orders
table columns:

```python
class OrdersModel(db.Model): 
     __tablename__ = 'orders' 

    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(30),
                    db.ForeignKey('accounts.username'), nullable=False                                 
    id_event = db.Column(db.Integer, nullable=False)
    tickets_bought = db.Column(db.Integer, nullable=False)
```

```python
def __init__(self, id_event, tickets_bought):
```

```python
self.id_event = id_event
    self.tickets_bought = tickets_bought
```

And create these endpoints in `app.py`:

```python
api.add_resource(Orders, '/orders/<string:username>')
api.add_resource(OrdersList, '/orders')
```

### Exercise 4: 
1.  Create method to return data in json structure filtered by event id
    in EventsModel:

    -   Id

    -   Username

    -   Event\_ name (event.name)

    -   Event\_ date (event.date)

    -   Event\_ city (event.city)

    -   Tickets\_ bought

2.  Create method to save data in db

3.  Create method to delete data from db

4.  Create a new resource called orders.py, where arguments required
    are:

    -   event\_ id, type=int, required=True

    -   tickets\_ bought, type=int, required=True

5.  Create class Orders (Resource) with methods:

    -   GET: returns all orders by username (self, username)

    -   POST: adds new order by username (self, username)

        1.  Query current user: Filter by username

        2.  Query current event: Filter by event\_ id

        3.  Check if user has enough money to buy the ticket

        4.  Check if there are available tickets

        5.  Update the current tickets available ( - tickets bought)

        6.  Update user money after buying tickets (-price \* tickets
            bought)

        7.  Initialize OrdersModel(id\_ event, tickets\_ bought)

        8.  Add order to user relationship user.orders.append(new\_
            order)

        9.  Save order, event and user

        10. Return json new order

6.  Create a class OrdersList(Resource) to return all orders available

Cart table
----------

Table where the user can visualize his/her order and its information. In
this table, we can see:

1.  **Event name**

2.  **Quantity**: amount of tickets bought for this event. User can
    interact adding or substracting a ticket with two buttons. If the
    quantity is 0, the event disappears from the list.

3.  **Price**: price per ticket

4.  **Total**: sum of all tickets price

5.  **Delete ticket**: button to delete event, the event disappears from
    the list

To create this table, you can use the classic structure
(<https://www.w3schools.com/html/html_tables.asp>).\
\<thread\> contains the headers:

```html
<thead>
<tr>
  <th>Event Name</th>
  <th>Quantity</th>
  <th>Price(\euro)</th>
  <th>Total</th>
  <th></th>
</tr>
</thead>
```

\<body\> in our case, containts the information and the buttons to
interact (increase, decrease)

### Exercise 5: 

1.  Create a table with events and their information

2.  Create increase and decrease methods and buttons

Back button
-----------

Let's create a button to return to main view:

### Exercise 6: 

1.  Create a variable called, for example, show. It will be our
    conditional to show Events or Cart view.

2.  Create a method to update the state of this conditional and show the
    Events part

3.  Link the method created before to a new button \"View Cart\" to show
    cart (show=True)

4.  Linkt he method created before to a new button \"Back\" (show=False)

Finalize purchase button
------------------------

This button runs a POST method which saves every single order row in the
database

```html
addPurchase (parameters) {
      const path = `http://localhost:5000/order/test
      axios.post(path, parameters)
        .then(() => {
          console.log('Order done')
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.log(error)
          this.getEvents()
        })
    },
```

with parameters:

```html
const parameters = {
          event_id: this.events_added.items[i].event.id,
          tickets_bought: this.events_added.items[i].quantity
        }
```

Once the POST method is done, we should reset the list where we store
the events to buy and deactivate the finalize purchase button. The user
should see:

![image](figures/image010.jpg)

Now, we have the information to update the available money of the user
after purchasing the order.

### Exercise 7: 

1.  Create a button called Finalize purchase

2.  Create POST method to send ordered purchase (POST per row)

    ```html
    finalizePurchase ()  {
        for (let i = 0; i < this.events_added.items.length; i += 1) {
            const parameters = {
                event_id: this.events_added.items[i].event.id,
                tickets_bought: this.events_added.items[i].quantity
            }
            this.addPurchase(parameters)
        }
    ```

3.  Initialize cart and deactivate button

Session 5
=========

Adding security to our backend
------------------------------

In this section we are going to modify and add some models and functions
to our AccountsModel, in order to add basic security and authentication.

### Save user password encrypted

It is not a good idea to store passwords or sensible information (credit
card number, etc.) without encrypting this information.\
In this case we are going to use `passlib` python library for password
hashing
(`Defaults to SHA256-Crypt under 32 bit systems, SHA512-Crypt under 64 bit systems`).\
First install `passlib` by doing :

    pip3 install passlib

And add these two methods to `models/accounts.py`:

```python
from passlib.apps import custom_app_context as pwd_context

class AccountsModel(db.Model):

    #PREVIOUS CODE HERE

    def hash_password(self, password):
        self.password = pwd_context.encrypt(password)
    def verify_password(self, password):
        return pwd_context.verify(password, self.password)
```

### Exercise 1: 

1.  Check or modify AccountsModel \_ \_ init\_ \_ (delete faux password
    initialization) and only initialize with username, available\_
    money, and is\_ admin values.

2.  Create resources in /resources/accounts.py methods related to this
    endpoints:

    ```python
    api.add_resource(Accounts, '/account/<string:username>', '/account')
                api.add_resource(AccountsList, '/accounts')
    ```

    where Accounts contains:

    -   **get**(self,username): get account info given a username

    -   **post**(self): create a new account passing `username` and
        `password`. Use `hash_ password` when creating an account, and
        use `save_ to _ db()` when saving a new user (first you are
        going to create a new user and then add a hashed password using
        `.hash_ password(password)` method).

    -   **delete**(self,username): delete an account related to a
        username (remember to also delete all related orders).

    and AccountsList contains:

    -   **get**(self): get information about all accounts

    Remember to return descriptive messages and a code if some petitions
    can't be served.

### Token authorization

A simple way to have a secure login system is using tokens instead of
sending usernames o passwords on body queries. This token usually has a
fixed lifetime and some information codified on it (username, etc.).\
To generate this token we are going to use a simple python library
called 'itsdangerous' that we have to install:

     pip3 install itsdangerous

Then we are going to add some methods to /models/account.py:

```python
from itsdangerous import (TimedJSONWebSignatureSerializer as Serializer, BadSignature, SignatureExpired)

class AccountsModel(db.Model): 

    #PREVIOUS CODE HERE 

    def generate_auth_token(self, expiration=600):
        s = Serializer(secret_key, expires_in=expiration)
        return s.dumps({'username': self.username}) 

    @classmethod
    def verify_auth_token(cls, token):
        s = Serializer(secret_key)
        try:
            data = s.loads(token)
        except SignatureExpired:
            return None  # valid token, but expired
        except BadSignature:
            return None  # invalid token

        user = cls.query.filter_by(username=data['username']).first() 

        return user
```

where:

-   **generate\_ auth\_ token**: returns a valid token with username
    information incrusted and expiration defined by an input value.

-   **verify\_ auth\_ token**: if input token is valid, it is going to
    extract username information and return this user

As you can see in previous code, there is a `secret key` parameter that
the method **generate\_ auth\_ token** uses as a generation seed, and
the method **verify\_ auth\_ token** uses to recover data incrusted in
our token. We are going to define a secret key in `db.py`:

```python
secret_key = "1q2s3f5g7jggujbffrhnbcdgh78jbhd"
```

This `secret_ key` can be a number, a sentence or a random set of
numbers and letters that only your app should know. We have to import
this key in `app.py`:

```python
from db import db, secret_key

app.config['SECRET_KEY'] = secret_key
```

And also import in /models/accounts:

```python
from db import db, secret_key
```

### Exercise 2: 

1.  Create a new login.py in resources folder with post method
    (`api.add_ resource(Login, ’/login’)`):

    -   `username` and `password` as required requested parameters

    -   Check if this `username` and `password` corresponds to one of
        our users (use **verify\_ password**) and return a valid token
        in that case (use **generate\_ auth\_ token**):

        ```python
        return {'token': token.decode('ascii')}, 200
        ```

    -   return a descriptive message and 404 if user not found

    -   return a descriptive message and 400 if password is invalid

2.  Validate that creating two users (one with admin privileges) and
    login works properly. You can use the following terminal examples:

    -   Get all current accounts:

            >>>import requests
            >>>r = requests.get('http://127.0.0.1:5000/accounts')
            >>>r.json()
            {'accounts': [{'username': 'npujol', 'is_admin': 0, 'available_money': 20},
                            {'username': 'test', 'is_admin': 0, 'available_money': 200},
                            {'username': 'user1', 'is_admin': 0, 'available_money': 20}]} 

    -   Add new account (with no admin privileges):

            >>>r = requests.post('http://127.0.0.1:5000/account', data = {'username':'user', 'password':'1234'})
            >>>r.json()
             {'username': 'user', 'is_admin': 0, 'available_money': 200}

            >>>r
            <Response [201]>

            >>>r = requests.get('http://127.0.0.1:5000/accounts')
            >>>r.json()
            {'accounts': [{'username': 'npujol', 'is_admin': 0, 'available_money': 20},
                     {'username': 'test', 'is_admin': 0, 'available_money': 200},
                     {'username': 'user1', 'is_admin': 0, 'available_money': 20},
                     {'username': 'user', 'is_admin': 0, 'available_money': 200}]}

    -   Add new accout (with admin privileges, only able by Terminal):

            >>> from models.accounts import AccountsModel
            >>>from models.orders import OrdersModel
            >>>from models.event import EventModel
            >>>from models.artist import ArtistModel
            >>>from db import db
            >>>new_account = AccountsModel(username='admin', is_admin=1)
            >>>new_account.hash_password('admin') #'username:'admin', password:'admin'
            >>>new_account.json()
            {'username': 'admin', 'is_admin': 1, 'available_money': 200}

    -   At this point we can take a look at how a hashed password looks
        like:

            >>>new_account.password
            '$6$rounds=656000$GelL4xX9ZikfYQ7r$C81e1B9ic.1kXDn3DO2HlsRDazIIWWxI36pIj5cWEkLPDNtPsul/JU8Id1nyWRieZfqK90rv5Dy7zP4OcQG4s1' 

            >>>from sqlalchemy import create_engine
            >>>from sqlalchemy.orm import sessionmaker
            >>>engine = create_engine('sqlite:///data.db')
            >>>Session = sessionmaker(bind=engine)
            >>>session = Session()
            >>>session.add(new_account)
            >>>session.commit()
            >>>session.close()

    -   Check if the new user is in accounts table:

            >>>r = requests.get('http://127.0.0.1:5000/accounts')
            >>>r.json()
            {'accounts': [{'username': 'npujol', 'is_admin': 0, 'available_money': 20},
              {'username': 'test', 'is_admin': 0, 'available_money': 200},
              {'username': 'user1', 'is_admin': 0, 'available_money': 20},
               {'username': 'user', 'is_admin': 0, 'available_money': 200}],
              {'username': 'admin', 'is_admin': 1, 'available_money': 200}]}

    -   Obtain token from /login:

            >>>r = requests.post('http://127.0.0.1:5000/login', data = {'username':'user', 'password':'1234'})
            >>>r
            <Response [200]>

            >>>r.json()
            {'token': 'eyJhbGciOiJIUzUxMiIsImlhdCI6MTU4ODQ0MzI2MywiZXhwIjoxNTg4NDQzODYzfQ.eyJ1c2VybmFtZSI6InVzZXIxIn0.Ljh3fTLiFlkVNatfdByiosdOUWesjDHMvxr_5SQeml0leGSdByVGFhl4_i7ZNQD0duu_TBdygcmqDYTLqf-XAQ'}

            >>>data = r.json()
            >>>data['token']
            'eyJhbGciOiJIUzUxMiIsImlhdCI6MTU4ODQ0MzI2MywiZXhwIjoxNTg4NDQzODYzfQ.eyJ1c2VybmFtZSI6InVzZXIxIn0.Ljh3fTLiFlkVNatfdByiosdOUWesjDHMvxr_5SQeml0leGSdByVGFhl4_i7ZNQD0duu_TbdygcmqDYTLqf-XAQ'     

    -   Check if token is from our user (we are going to use it in
        further exercises):

            >>>from itsdangerous import (TimedJSONWebSignatureSerializer as Serializer, BadSignature, SignatureExpired)
            >>>my_token = data['token']
            >>>secret_key = "eioc98u93injniejenufnciijucnuw82"
            >>>s = Serializer(secret_key)
            >>>data = s.loads(my_token)
            >>>data
            {'username': 'user'}                

### Endpoints to allowed accounts

Until now, all data was avaialble to every user that could make a
request. In localhost that is not a problem, but in deployment we allow
every user that can make a request to add, modify or delete elements
from our database.\
Using a simple HTTPauth and tokens we are going to avoid these kind of
undesired requests. Install HTTPAuth:

    pip3 install Flask-HTTPAuth

Define two functions on models/accounts.py outside the AccountsModel
class, like:

```python
from flask_httpauth import HTTPBasicAuth
from flask import g

auth = HTTPBasicAuth()
```

```python
#AccountsModel CODE

 
@auth.verify_password
def verify_password(token, password):
    #CODE HERE

@auth.get_user_roles
def get_user_roles(user):
    #CODE HERE
```

### Exercise 3: 

1.  Add code to **verify\_ password(token,password**:

    -   In our case we are not going to use password but the default
        structure that uses HTTPBasicAuth library.

    -   Given a token we have to return the account (entire object)
        related to it (use verify\_ auth\_ token). If the user exists,
        return the user and make a copy at a global variable from flask
        called g (g.user = user). This variable is going to help us
        share information between methods, and will last for the
        lifetime of the request. If the user doesn't exist there's no
        need to return anything.

2.  Add code to **get\_ user\_ roles(user)**:

    -   given an AccountsModel object (here user) return \['admin'\] if
        this user is\_ admin=1 or \['user'\] otherwise.

3.  Add the decorator related to previous functions to post method from
    resources/orders.py (import all needed modules)

        @auth.login_required(role='user')
        def post(self, username): 

    This decorator is going to call **verify\_ password** and, because a
    role is defined, it is also going to call **get\_ user\_ role**. It
    is only going to be able to use POST method if it is a 'logged' user
    with 'user' role. We are going to add a valid token to post method
    request to be able to validate that user.

    -   Add security to ensure that the username used in the endpoint
        and the username that has generated the token are the same user.
        Use flask g variable generated by **verify\_ password**
        (g.user.username).

    -   If it is not the same use return a descriptive 'message' with
        400 code.

    -   If you haven't before, make sure that the user has enough money
        to buy and there are enough available tickets and return
        descriptive 'message' and code if this happens. Check these
        values even if you take care of this on frontend (you never know
        which applications can use your API)

    -   If we only use a decorator as `@auth.login_ required()`, it is
        only going to check if a user is logged (has a valid token).

You can check that this part works properly by following these terminal
examples, but first get a valid token from /login and then:

    >>>from requests.auth import HTTPBasicAuth
    >>>r = requests.post('http://127.0.0.1:5000/orders/user', data = {'id_event':2, 'tickets_bought':2},auth=HTTPBasicAuth(my_token, ''))
    >>>r
    <Response [201]>

    >>>r.json()
    {'event_city': 'Barcelona',
     'event_date': '2020-07-03',
     'event_name': 'Festival Cruilla 2020',
     'id': 6,
     'tickets_bought': 2,
     'username': 'user'}

Try the same but with different username endpoints:

    Try the same but with different username endpoint:
    >>>r = requests.post('http://127.0.0.1:5000/orders/npujol', data = {'id_event':2, 'tickets_bought':2},auth=HTTPBasicAuth(my_token, ''))
    >>>r
    <Response [400]>

    >>>r.json()
    {"message": "Bad authorization user"}

Add `login_ required(role=’admin’)` decorator to all methods that you
think could be restricted only to admin (usually related to add, delete
or modify data).\
Make extra manual test to esnure that your API works properly with
requests or Postman.

Login Frontend
--------------

Let's start with the design of the component. First, create a new
component called Login.vue. Beside this, go to index.js file and add the
route.

```html
import Vue from 'vue'
import Router from 'vue-router'
import Events from '@/components/Events.vue'
import Login from '@/components/Login.vue'

Vue.use(Router)

export default new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    {
      path: '/',
      name: 'Events',
      component: Events
    },
    {
      path: '/userlogin',
      name: 'Login',
      component: Login
    }
  ]
})
```

Once the structure is ready, we can design the Login view as:

![image](figures/image015.png)

As you can see, we have two input texts and three buttons.\
If we pay attention to each button:

Sign In
-------

It checks if our username and password are already registered. To check
this, we first have to save the username and password written by user.
To do this we can use `vmodel=”username”` and `vmodel=”password”`:

    <div class="form-label-group">
      <label for="inputEmail">Username</label>
      <input type="username" id="inputUsername" class="form-control"
      placeholder="Username" required autofocus v-model="username">
    </div>
    <div class="form-label-group">
      <br>
      <label for="inputPassword">Password</label>
      <input type="password" id="inputPassword" class="form-control"
      placeholder="Password" required v-model="password">
    </div>

Where username and password are variables created in data() -\> return.

       data () {
        return {
          username: '',
          password: '',

To check the user, we have to POST in /login to obtain the token that we
will use later:

       checkLogin () {
          const parameters = {
            username: this.username,
            password: this.password
          }
          const path = `http://localhost:5000/login`
          axios.post(path, parameters)
            .then((res) => {
              this.logged = true
              this.token = res.data.token
              this.find_match = true
              this.getAccount()
            })
            .catch((error) => {
              // eslint-disable-next-line
              console.error(error)
              this.user = ''
              alert('Username or Password incorrect')
            })
        },

On the other hand, there is a tool to change your current route to
another one:

    this.$router.replace({ path: '/', query: { username: this.username, logged: this.logged } })

You will need some information about the user that we pass using a
query. `Username` contains the current username, `logged` is a boolean
that shows if the user has logged in correctly, and other
characteristics that we will implement in the next exercise.\
To consume the query information from events view, we can use the
following line in created ():

      created () {
        this.logged = this.$route.query.logged
        this.username = this.$route.query.username

### Exercise 4: 

1.  Create a user using flask shell if you don't have any user in the
    database created yet.

2.  Create a method getAccount() to GET if the user, who is going to log
    in, is admin or not and save it in a variable called `is_ admin`

3.  Create an alert to show if the user is logged in.

4.  Change the current route to '/' saving the current information using
    query and consume the information from events component '/' in
    created():

    -   Username

    -   Logged

    -   is\_ admin

    -   token

Create Account
--------------

![image](figures/image016.png)

This button is a form where the user will introduce his/her data and
submit it. Submit button calls a POST method where the data is saved in
our accounts table.\
Before sending POST we should consider how to obtain the data to send.
To obtain this data, we can use Forms and store it in an object.\
In our form, we have to collect the necessary information to send it to
Flask either for POST or PUT.\
First of all, we have to create an object to store the data:

```html
addUserForm: {
        username: '',
        password: ''
      }
```

Then, create an initialize object method:

```html
initForm () {
      this.addUserForm.username = ''
      this.addUserForm.password = ''
    },
```

After defining the object, it has to be completed using a Form
(<https://bootstrap-vue.org/docs/components/form>).\
Afterwards, using onSubmit method we should call the method to POST.
Finally, call initForm method to restart the parameters.

### Exercise 5: 

1.  Create a form to save the data

2.  Create a POST method to send the new user data using path and
    parameters:

    ```html
    const path = 'http://localhost:5000/account'
    ```

    ```html
    const parameters = {
          username: this.addUserForm.username,
          password: this.addUserForm.password
          } 
    ```

3.  Alert to user if the account has been created or it already exists

Back To Events
--------------

It returns to event page but sending different information with query,
as SIGN IN does. In this case, logged = False and username is not
necessary.

```html
this.logged = false
this.$router.replace({ path: '/', query: { logged: this.logged } })
```

### Exercise 6: 

1.  Create a method to replace route and link to back to events button.

Buying with security
--------------------

To provide security for each user's purchases, we should use the token
obtained where user presses SIGN IN. To do that, in the addPurchase
(parameters) you already created in the last session, swap it for:

```html
addPurchase (parameters) {
      const path = `http://localhost:5000/order/${this.username}`
      axios.post(path, parameters, {
        auth: {username: this.token}
      })
        .then(() => {
          console.log('Order done')
        })
        .catch((error) => {
          // eslint-disable-next-line
          console.log(error)
          this.getEvents()
        })
    },
```

See that we are using auth parameters with token as a username.

User and Events Information
---------------------------

To make clear the user's interaction, we should show to the user the
following information:

### Exercise 7: 

1.  In events view, show the available money and the tickets added to
    the cart. Remember that the available money is always controlled by
    the database, and it doesn't have to change with the interaction
    from VUE, just change after purchasing tickets or refreshing the
    page.

    ![image](figures/image017.png)

2.  In events view, show the tickets available for each event. As
    available money, it is always controlled by the database and it
    doesn't have to change with the interaction from VUE, just change
    after purchasing tickets or refreshing the page.

Session 6
=========

Create a new event and update it
--------------------------------

We are going to create a button to add new events and another to update
them.

![image](figures/image018.png){width="8cm"}

Starting with "Add New Event", we should call a form to collect the data
as we did with "Create Account" button. The form should look like:

![image](figures/image019.png)

### Exercise 1: 

1.  Create an \"Add New Event\" button

2.  Create a form with `ref="addEventModal"` to collect the data using
    Forms

3.  Create a variable to store the data collected

    ```html
    addEventForm: {
            place: '',
            name: '',
            city: '',
            date: '',
            price: '',
            total_available_tickets: ''
          },    
    ```

4.  Create **onSubmit** method called from Submit button as:

    ```html
    onSubmit (evt) {
          evt.preventDefault()
            this.$refs.addEventModal.hide()
          const parameters = {
            place: this.addEventForm.place,
            name: this.addEventForm.name,
            city: this.addEventForm.city,
            date: this.addEventForm.date,
            price: this.addEventForm.price,
            total_available_tickets: this.addEventForm.total_available_tickets
          }
          this.addEvent(parameters)
          this.initForm()
        },      
    ```

5.  Create **addEvent(parameters)** method: it should call a POST
    request to <http://localhost:5000/event>. Remember to add auth
    parameters to POST request, like:

    ```html
    axios.post(path, parameters, {
            auth: {username: this.token}
          })        
    ```

    Also remember to add admin role to the new event (POST):

    ```html
    @auth.login\_ required(role=admin)
    ```

    After this, refresh the current events available.

6.  Create `initForm()` method to initialize the parameters inside
    `addEventForm`.

7.  Call `initForm()` after adding an event.

8.  Create **onReset** method called from Reset button as:

    ```html
    onReset(evt) {
            evt.preventDefault()
            this.initForm()
            this.show = false
            this.$nextTick(() => {
            this.show = true
            })
          },
    ```

The next button we should create is "Update Event" button. In this case,
the form to collect data should look like:

![image](figures/image020.png)

### Exercise 2: 

1.  Create an \"Update Event\" button

2.  Create a form with `ref = “editEventModal”` to collect the data
    using Forms

3.  Create a variable to store the data collected:

    ```html
    editEventForm: {
            id: '',
            name: '',
            place: '',
            city: '',
            date: '',
            price: '',
            total_available_tickets: ''
          },
    ```

4.  Create **onSubmitUpdate** method called from Submit button

5.  Create **updateEvent(parameters)** method. It should call a PUT
    request to <http://localhost:5000/event/event_id>, where
    `event_ id = this.editEventForm.id` .\
    Remember to add auth parameters to PUT request:

    ```html
    axios.put(path, parameters, {
                auth: {username: this.token}
            })
    ```

    Also remember to add admin role to update event (PUT), like:

    ```html
    @auth.login\_ required(role=admin)
    ```

    After this, refresh the current events available.

6.  Add to **initForm()** method the `editEventForm` parameters.

7.  Call **initForm()** after updating an event.

8.  Create **onResetUpdate** method called from Reset button.

Add and delete artists to events
--------------------------------

In this section, we are going to create two more buttons to add and
delete artists in an event. To have an idea of the final result:

![image](figures/image021.png)

Starting with "Add Artist To Event", we should also create a form to
introduce the parameters of the new artist:

![image](figures/image022.png)

As well as adding the artist to the event, we have to create the artist
if he/she doesn't already exist.

### Exercise 3: 

1.  Create an \"Add Artist to Event\" button

2.  Create a method **EventWhereModifyArtist(event)** and call it every
    time you press the button "Add Artist to Event". This way, we have
    saved the event and we also have access to the information
    everywhere.

    ```html
    eventWhereModifyArtist (event) {
                this.event_to_modify = event
                },
    ```

3.  Create a form with `ref = “addArtistModal”` to collect the data
    using Forms

4.  Create a variable to store the data collected

    ```html
    addArtistForm: {
              id: '',
              name: '',
              country: '',
              genre: ''
            },
    ```

5.  Create **onSubmitAddArtistInEvent** method called from Submit button
    as:

    ```html
    onSubmitAddArtistInEvent (evt) {
                evt.preventDefault()
                this.$refs.addArtistModal.hide()
                const parameters = {
                   name: this.addArtistForm.name,
                   country: this.addArtistForm.country,
                   genre: this.addArtistForm.genre
                }
                this.addNewArtist(parameters)
                this.addArtistInEvent(parameters)
                },
    ```

6.  Create **addNewArtist(parameters)** method to add the new artist to
    the database if he/she doesn't already exist, by doing a POST
    request to <http://localhost:5000/artist>.\
    Remember to add auth parameters to POST request:

    ```html
    axios.post(path, parameters, {
              auth: {username: this.token}
            })
    ```

    Also, remember to add admin role to add artist in event (POST):

    ```html
    @auth.login_required(role=admin)
    ```

7.  Add to **initForm()** method the addArtistForm parameters

8.  Call **initForm()** after adding artist to event

9.  Create **onResetAddArtistInEvent** method called from Reset button

Now, we are going to do the same with \"Delete Artist in Event\".

### Exercise 4: 

1.  Create a \"Delete Artist to Event\" button

2.  Call **eventWhereModifyArtist()** method you created before, every
    time you press the button \"Delete Artist in Event\".

3.  Create a form with `ref = “deleteArtistModal”` to collect the data
    using Forms

4.  Create a variable to store the data collected

    ```html
    deleteArtistForm: {
              id: '',
              name: ''
            },
    ```

5.  Create **onSubmitDeleteArtistInEvent** method called from Submit
    button as:

    ```html
    onSubmitDeleteArtistInEvent (evt) {
                evt.preventDefault()
                this.$refs.deleteArtistModal.hide()
                this.deleteArtistInEvent()
                },
    ```

6.  Create **deleteNewArtist(parameters)** method. It should call a
    DELETE request to
    <http://localhost:5000/event/event_id/artist/artist_id>. In here:

    -   `event_ id = this.event_ to_ modify.id`

    -   `artist_ id = this.artist_ id`

    Remember to add auth parameters to DELETE request:

    ```html
    axios.delete(path, {
              auth: {username: this.token}
            })
    ```

    Also, remember to add admin role to delete artist in event (DELETE):

    ```html
    @auth.login_required(role=admin)
    ```

7.  Add to **initForm()** method the deleteArtistForm parameters

8.  Call **initForm()** after deleting artist in event

Delete event
------------

After adding and updating events, let's implement the delete event
option. Unlike add new event and update event buttons, "Delete event"
button has to be in every single card. The final result should look
like:

![image](figures/image023.png)

### Exercise 5: 

1.  Create a \"Delete event\" button

2.  Call **removeEvent(event.id)** method. This method has to call a
    DELETE request to <http://localhost:5000/event/event.id>.\
    Remember to add auth parameters to POST request:

    ```html
    axios.delete(path, {
              auth: {username: this.token}
            })
    ```

    Also, remember to add admin role to delete event (DELETE):

    ```html
    @auth.login_required(role=admin)
    ```

After this, refresh the current events available.

Admin and user view
-------------------

We sholud consider that a regular user is not going to have the same
functionalities as an admin. Therefore, we must disable some
functionalities for the user. Let's see the two different views:\
**VIEW ADMIN**:\
Admin has functionalities such as:

-   Add New Event

-   Update Event

-   Add Artist to Event

-   Delete Artist in Event

-   Delete Event

-   Log Out

![image](figures/image024.png){width="9cm"}

**VIEW USER WITHOUT ADMINISTRATION RIGHTS**:\
On the other hand, normal user has less functionalities:

-   Add to Cart

-   View Cart

-   Log Out

![image](figures/image025.png){width="9cm"}

Activate and deactivate these functions adding a condition inside the
button. Remember that we already have a boolean to know if the user is
admin or not (`is_ admin`, information from login component query) e.g:

```html
<button>
         v-if="logged && is_admin==1"
         style="margin: 15px"
         class="btn btn-dark btn-lg"
         @click="eventWhereModifyArtist(event)"
         v-b-modal.add-artist-modal>
         Add Artist to Event
      </button>
```

### Exercise 6: 

1.  Disable "Add New Event", "Update Event", "Add Artist to Event",
    "Delete Artist in Event" and "Delete Event" functionalities for a
    user without administrator rights

2.  Disable "View Cart" and "Add to Cart" functionalities for an admin.

### Exercise 7: 

1.  Create all the admin views and forms needed to manage Artists,
    similar to Events

Session 7
=========
