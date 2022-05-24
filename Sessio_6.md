---
layout: page
title: Heroku Deployment
---
---

# Del desenvolupament a la producció.

## Desplegant a Heroku

Heroku va ser una de les primeres plataformes com a proveïdors de serveis. Va començar com una opció d'allotjament per a aplicacions basades en Ruby, però després va créixer per admetre molts altres idiomes com Java, Node.js i el nostre favorit, Python.

En essència, desplegar una aplicació web a Heroku només requereix carregar l'aplicació amb git. Heroku cerca un fitxer anomenat Procfile al directori arrel de l'aplicació per obtenir instruccions sobre com executar l'aplicació. Per als projectes Python, Heroku també espera un fitxer requirements.txt que enumera totes les dependències del mòdul que cal instal·lar.


1. Creant un compte Heroku
Abans de poder desplegar-nos a Heroku, hem de tenir un compte amb ells. Aneu a [heroku.com](https://www.heroku.com/) i creeu un compte.
Un cop hàgiu iniciat la sessió, teniu accés a un tauler, on es poden gestionar totes les vostres aplicacions.

2. Instal·lació del client Heroku

Heroku ofereix una eina anomenada "client Heroku" que utilitzarem per crear i gestionar la nostra aplicació. Aquesta eina està disponible per a Windows, Mac OS X i Linux. Si hi ha una descàrrega [Heroku toolbelt](https://toolbelt.heroku.com/) per a la vostra plataforma, aquesta és la manera més senzilla d'instal·lar l'eina de client Heroku.

El primer que hem de fer amb l'eina de client és iniciar sessió al nostre compte:

    $ heroku login
    

### Creació d'una aplicació Heroku

Per crear una nova aplicació Heroku, només cal que utilitzeu l'ordre create del directori arrel de l'aplicació

      $ heroku apps:create <<nomgrup>>-sportsmaster
      
Enllaça el teu repositori local a Heroku.

	$ heroku git:remote -a <<nomgrup>>-sportsmaster


Al final del desplegament, hauríeu de tenir aquests fitxers al directori arrel de la vostra aplicació:

* requirements.txt
* Procfile
* config.py
* /templates/index.html
* /static
* /models
* /resources
* app.py
* db.py

### El fitxer requirements.txt
    
Heroku no proporciona un servidor web. En lloc d'això, espera que l'aplicació iniciï el seu propi servidor al número de port indicat a la variable d'entorn $PORT.

Sabem que el servidor web Flask no és bo per a l'ús de producció perquè és un sol procés i un sol fil, de manera que necessitem un servidor millor. El tutorial Heroku per a Python suggereix `gunicorn`, un servidor web d'estil pre-fork escrit en Python, de manera que aquest és el que farem servir.

El servidor web `gunicorn` s'ha d'afegir al `requirements.txt` dins del directori de l'aplicació. Afegiu també totes les dependències dels paquets python per al vostre projecte. El fitxer `requirements.txt` hauria d'estar a l'arrel del projecte i conté el nom del paquet '==' número de versió del paquet. Per conèixer les versions dels vostres paquets específics relacionats amb Flask, utilitzeu `pip freeze`. Podeu volcar el contingut d'aquesta instrucció a un fitxer amb `pip freeze > requirements.txt`.

Example:

```   
flask == num_versio
flask-restful == num_versio
Flask-SQLAlchemy == num_versio
Flask-Migrate == num_versio
flask-cors == num_versio
passlib == num_versio
pyjwt == num_versio
Flask-HTTPAuth == num_versio
gunicorn	    # Engine WSGI per a Python
psycopg2            # Driver de Postgress
```

### El fitxer Procfile
L'últim requisit és dir-li a Heroku com executar l'aplicació. Per a això, Heroku requereix un fitxer anomenat només "Procfile" a la carpeta arrel del Github.

Aquest fitxer és extremadament senzill, només defineix els noms del procés i les ordres associades a ells (fitxer Procfile):
```
   web: gunicorn -w 1 -k gthread --threads 4 app:app
```

Utilitzarem un treballador del tipus gthread i 4 fils per a la nostra aplicació. Podeu ajustar el valor del fil. Si no feu servir bloquejos (vegeu més avall), establiu --threads a 1. 

L'etiqueta web està associada al servidor web. Heroku espera aquesta tasca i l'utilitzarà per iniciar la nostra aplicació.

### El fitxer config.py

Necessitarem un fitxer `config.py` per controlar les variables de desenvolupament i entorn de producció. L'entorn de producció conté les instruccions per a que la nostra app funcioni a Heroku. Quan estiguem treballant en local, l'entorn que farem servir és el de development, que té les _settings_ per treballar en local.
 
```python 

from decouple import config
class Config:
    pass

class ProductionConfig(Config):
    DEBUG = False
    SQLALCHEMY_DATABASE_URI = config('DATABASE_URL', default='localhost')
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    STATIC_FOLDER = "/static"
    TEMPLATE_FOLDER = "/templates"
    SECRET_KEY = config('SECRET_KEY', default='localhost')

class DevelopmentConfig(Config):
    DEBUG = True
    SQLALCHEMY_DATABASE_URI = 'sqlite:///data.db'
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    STATIC_FOLDER = "/P2_VUE_WEBPACK/frontend/dist/static"
    TEMPLATE_FOLDER = "/P2_VUE_WEBPACK/frontend/dist"
    SECRET_KEY = "kdsfklsmfakfmafmadslvsdfasdf"

config = {
    'development': DevelopmentConfig,
    'production': ProductionConfig
}

```

Ara hem de modificar el `app.py`: esborrar totes les variables de configuració i carregar-les des de config.py en funció de l'entorn:

```python 
from decouple import config as config_decouple
from config import config
	
app = Flask(__name__)
environment = config['development']
if config_decouple('PRODUCTION', cast=bool ,default=False):
    environment = config['production']
   
app.config.from_object(environment) 
	
```
Traieu també la clau secreta de `db.py`.
A model/comptes, elimineu la "secret_key import from db.py" i afegiu aquest codi:

```python
from flask import g, current_app
```
i substituïu l'ocurrència de `secret_key` per `current_app.secret_key`

Afegiu les variables de configuració PRODUCTION establertes a True i la vostra pròpia SECRET_KEY amb:

```
 heroku config:set PRODUCTION=True  -a <<nomgrup>-sportsmaster>

 heroku config:set SECRET_KEY=Your_own_key -a <<nomgrup>-sportsmaster>
```

Comproveu-los amb

`heroku config -a <<nomgrup>-sportsmaster>`


### Database
Hauríem d'utilitzar una base de dades de producció. Heroku inclou una instància gratuïta de postgress que podem utilitzar. Aneu al vostre escriptori Heroku i [instal·leu el complement de postgress] (https://elements.heroku.com/addons/heroku-postgresql).

	
`heroku  addons:create heroku-postgresql:hobby-dev -a <<nomgrup>-sportsmaster>`

Afegiu una variable d'entorn amb les credencials de la base de dades creada:


`heroku config:set DATABASE_URL= <<URI>>  -a <<nomgrup>-sportsmaster>`

per trobar la URI aneu a la base de dades creada a heroku i aneu a Database Credentials i alla teniu la URI definida, ara bé per a que funcioni des de SQLALchemy canvieu el protocol `postgres:`de la URI per `postgresql:`. Si teniu problemes per canviar la URL, podeu executar `heroku addons:detach DATABASE -a <app_name>`.



### Preparant el nostre codi anterior per a la producció.

Hem de preparar el nostre codi anterior per a la producció.

* Substituïu tots els enllaços `localhost:5000` als components de vue per a `https://<<nomgrup>-sportsmaster>.herokuapp.com/`.
* Regenera la carpeta vue `/dist`:
`npm run build`
* Afegiu els resultats a STATIC_FOLDER i TEMPLATE_FOLDER (Anar a la carpeta /Dist dins de frontend, copiar `index.html` dins d'una nova carpeta a l'arrel de la app anomenada `templates`, copiar també `/Dist/static` i enganxar-la directament també a l'arrel de la app).
* Hem de modificar totes les columnes Enum dels models per tal de posar-li un nom 
per exemple, la columna ``category = db.Column(db.Enum(*categories_list,),nullable=False)`` hauria de quedar així: ``category = db.Column(db.Enum(*categories_list,name='categories_types'), nullable=False)``




#### Creació d'un Singleton Lock SafeThread per utilitzar-lo en l'operació de sol·licituds d'escriptura simultània

La nostra configuració de gunicorn és monoprocés (un treballador) i multiThread, de manera que ens hem d'assegurar que els nostres mètodes **post**, **delete** i **put** siguin safeThread mitjançant bloquejos. Aquests bloquejos s'han de compartir amb la resta de fils que accedeixen al mateix endpoint (per tant, han de ser Singleton) i la implementació d'aquest Sigleton ha de ser SafeThread. Aquí teniu el codi d'aquesta classe de bloqueig:

`lock.py` 

```python
import threading

class my_Lock(object):
   __singleton_lock = threading.Lock()
   __singleton_instance = None

   @classmethod
   def getInstance(cls):
      if not cls.__singleton_instance:
         with cls.__singleton_lock:
            if not cls.__singleton_instance:
               cls._singleton_instance = cls()
      return cls.__singleton_instance

   def __init__(self):
      """ Virtually private constructor. """
      if my_Lock.__singleton_instance != None:
         raise Exception("This class is a singleton!")
      else:
         my_Lock.__singleton_instance = self
         self.lock = threading.Lock()

lock = my_Lock.getInstance()
```

per exemple a `resources/orders.py`

```python
from lock import lock
....
    def post(self, username): 
        data = self.parser.parse_args()
        with lock.lock:
        	....
		 return ....
```

### Desplegant el codi

Ara podeu implementar el vostre codi. Recordeu que si no teniu el fitxer de la base de dades de sqlite i la carpeta de migrations al .gitignore haureu d'**moure a un altre lloc el fitxer de base de dades i la carpeta migrations** abans de desplegar el codi, fer un commit, push sense aquests fitxers, modificar el .gitignore per afegir aquest fitxer i carpeta i tornar-los a copiar en local. Així tindreu el sqlite en local per a desenvolupament i no en remot per a produccio.

Afegiu, fer commit i envieu tots els vostres fitxers locals al vostre repositori

```
git add *
git commit -a -m "ready to deploy"
```

Si la vostra aplicació no es troba al directori arrel del vostre repositori github, enllaceu el subdirectori on es troba:

`git subtree push --prefix subdirectori_practica_github heroku main  `

Si la vostra aplicació es troba a l'arrel:

`git push heroku main`



Si no teniu cap error en el procés, ara podeu crear les taules de la base de dades:


`heroku  run bash -a <<nomgrup>-sportsmaster>`

```bash 
flask db init
flask db migrate -m "Initial migration."
flask db upgrade
```

Busqueu el vostre sqlite per al vostre compte d'administrador i anoteu els valors.

```
sqlite3 data.db 
select * from accounts;
```

Ara, instal·leu a la vostra màquina local el client postgress `psql` i executeu l'ordre heroku pg:psql i inseriu el vostre compte d'administrador des de la base de dades sqlite.

```
heroku pg:psql -a <<nomgrup>-sportsmaster>
INSERT INTO accounts VALUES(1,'admin','THE_HASH....',200);
```

Per a comprovar que ho teniu tot bé, podeu executar també 

```
heroku pg:psql -a <<nomgrup>-sportsmaster>
SELECT * FROM match;
```
On haurieu de veure tots els partits, o, `SELECT * FROM accounts;` per veure els usuaris creats amb el hash de contrasenya.

**Recordeu que dins del navegador, amb la pàgina oberta, podeu veure la consola pitjant F12, on podreu visualitzar qualsevol possible error**. Recordeu també que **qualsevol canvi en qualsevol fitxer en la carpeta frontend requerirà executar `npm run build`, copiar els fitxers generats en les respectives carpetes i fer push per a veure els canvis a Heroku.

* També us podeu conectar a la base de dades de Postgres de Heroku fent servir DBeaver, per exemple, on per configurar una nova connexió haureu d'omplir els camps de usuari, contrasenya, nom de la db, host i port, informació que trobareu a Database credentials de Heroku. Un cop establerta la connexió podreu veure tota la informació de les db, eliminar o afegir files, etc.

Ara l'aplicació està en línia:

`heroku open -a <<nomgrup>-sportsmaster>`

Per consultar els registres podeu accedir-hi:

`heroku logs -a <<nomgrup>-sportsmaster>`

### Tingueu en compte algunes limitacions del pla gratuït d'heroku:

- L'aplicació "s'adorm" després de 30 minuts d'inactivitat. El primer accés després pot ser lent.
- La base de dades PostgreSQL gratuïta té una limitació de 10K files.
- Heroku es desplega en el protocol `https`, en lloc de `http`. Per tant, tots els enllaços s'han de canviar al protocol "https".

(Nota: amb https://education.github.com/pack teniu un pla Hobby Dino de franc!)

