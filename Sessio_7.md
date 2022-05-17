Sessió Opcional
=========
  	
	
Vista d'administrador i d'usuari
-------------------

Hem de considerar que un usuari normal no tindrà les mateixes funcions que un administrador. Per tant, hem de desactivar algunes funcionalitats per a l'usuari normal. Vegem les dues vistes diferents: 

**Vista ADMINISTRADOR**: 

L’administrador té funcions com:

- Veure informació dels partits

- Afegeix un partit nou

- Actualització del partit

- Afegeix equips al partit

- Suprimeix partit de la competició 

- Esborra una competició

- Tancar sessió




**Vista USUARI SENSE DRETS D'ADMINISTRACIÓ**:

D'altra banda, l'usuari normal té menys funcionalitats:

-   Veure informació dels partits

-   Afegeix a la cistella

-   Veure cistella

- 	 Comprar

-   Tancar sessió


Activeu i desactiveu aquestes funcions afegint una condició dins dels botons que facin aquestes accions. Recordeu que ja tenim un booleà per saber si l'usuari és administrador o no ("is_admin"), per exemple:

```html
<button v-if="logged && is_admin==1"
         style="margin: 15px"
         class="btn btn-dark btn-lg"
         @click="showWhereModifyArtist(event)"
         v-b-modal.add-artist-modal>
         Add Team to Match
      </button>
```

## Creant formularis per afegir o modificar dades al backend

Per crear un formulari en la vista, podem usar el següent codi bootstrap:


* Creeu un botó "Afegeix un partit nou"

* Creeu un formulari amb la ref =" addMatchModal " per recopilar les dades mitjançant Forms de bootstrap:

```html
 <b-modal ref="addMatchModal"
        id="show-modal"
        title="Add new Match"
        hide-footer>
  <b-form @submit="onSubmit" @reset="onReset" v-if="show">
  <b-form-group  .....>
  	 <b-form-input ....> ></b-form-input> .....
  </b-form-group>
  <b-button type="submit" variant="primary">Submit</b-button>
   <b-button type="reset" variant="danger">Reset</b-button>
   </b-modal>
  
```

* Creeu una variable per emmagatzemar les dades recollides que voldreu enviar al backend, per exemple:

```
  addMachForm: {
        team1: '',
        team2: '',
        ...
      },    
```
* Creeu el mètode onSubmit cridat des del botó "Enviar":
```
    onSubmit (evt) {
          evt.preventDefault()
          this.$refs.addMatchModal.hide()
          const parameters = {
            team1: this.addShowForm.team1,
            team2: this.addShowForm.team2,
            ...
          }
          this.addMatch(parameters)
          this.initForm()
        },
 ```     
* Crea el mètode addMatch(paràmetres): hauria de cridar a una sol·licitud POST al backend. Recordeu afegir paràmetres d'autenticació a la sol·licitud POST, com ara:

```
axios.post(path, parameters, {
        auth: {username: this.token}
      })        
```     

* Comproveu que en el backend s'hagi d'estar registrat com a administrador en aquest endpoint:

``@auth.login_required(role=admin)``

Després d'això, actualitzeu els partits disponibles.

* Creeu el mètode initForm() per a inicialitzar els paràmetres de addMathcForm.

* Crideu `initForm() 'després d'afegir un partit.

* Crea el mètode onReset cridat des del botó Reset com:

```
onReset(evt) {
       evt.preventDefault()
       this.initForm()
       this.show = false
       this.$nextTick(() => {
       this.show = true
       })
     },
```
### Exercicis:

* Feu els Endpoints necessaris per a que el administrador pugui actualitzar les dades del model.
* Adapteu la vista per tal que si l'usuari registrat és administrador s'activin els formularis amb el seu botó per a actualitzar les dades del model. 
