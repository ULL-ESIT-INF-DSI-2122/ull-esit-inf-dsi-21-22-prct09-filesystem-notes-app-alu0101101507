[![Coverage Status](https://coveralls.io/repos/github/ULL-ESIT-INF-DSI-2122/ull-esit-inf-dsi-21-22-prct09-filesystem-notes-app-alu0101101507/badge.svg?branch=main)](https://coveralls.io/github/ULL-ESIT-INF-DSI-2122/ull-esit-inf-dsi-21-22-prct09-filesystem-notes-app-alu0101101507?branch=main) [![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=ULL-ESIT-INF-DSI-2122_ull-esit-inf-dsi-21-22-prct09-filesystem-notes-app-alu0101101507&metric=alert_status)](https://sonarcloud.io/summary/new_code?id=ULL-ESIT-INF-DSI-2122_ull-esit-inf-dsi-21-22-prct09-filesystem-notes-app-alu0101101507)

# Practica 9 Aplicación de procesamiento de notas de texto
[github-pages](https://ull-esit-inf-dsi-2122.github.io/ull-esit-inf-dsi-21-22-prct09-filesystem-notes-app-alu0101101507/)

Hemos de relizar una aplicacion de procesamiento de notas de textos que ha de cumplir con los siguientes requisitos:

  -La aplicación de notas deberá permitir que múltiples usuarios interactúen con ella, pero no simultáneamente

  -Una nota estará formada, como mínimo, por un título, un cuerpo y un color (rojo, verde, azul o amarillo)

  -Cada usuario tendrá su propia lista de notas, con la que podrá llevar a cabo diferentes operaciones


Para realizar esto se ha creado una clase notas que definira el esqueleto de las notas que se crearan durante toda la practica y luce asi:

## Clase Notas
```typescript
export class Nota {
  constructor(protected titulo: string, protected cuerpo: string , protected color: string){

  }

  getTitulo(): string {
    return this.titulo;
  }

  getCuerpo(): string{ 
    return this.cuerpo;
  }

  getColor(): string {
    return this.color;
  }

  setTitulo(aux: string) {
    this.titulo = aux;
  }

  setCuerpo(aux: string){
    this.cuerpo = aux;
  }

  setColor(aux: string){
    this.color = aux;
  }

  printNotaTitulo(){ 
    if(this.color == "Rojo") {
      console.log(chalk.red(`${this.titulo}`));
    } else if(this.color == "Verde") {
      console.log(chalk.green(`${this.titulo}`));
    } else if(this.color == "Azul") {
      console.log(chalk.blue(`${this.titulo}`));
    } else if(this.color == "Amarillo") {
      console.log(chalk.yellow(`${this.titulo}`));
    }
  }

  printNotaCuerpo(){
    if(this.color == "Rojo") {
      console.log(chalk.red(`${this.cuerpo}`));
    } else if(this.color == "Verde") {
      console.log(chalk.green(`${this.cuerpo}`));
    } else if(this.color == "Azul") {
      console.log(chalk.blue(`${this.cuerpo}`));
    } else if(this.color == "Amarillo") {
      console.log(chalk.yellow(`${this.cuerpo}`));
    }
  }
}

```

En esta clase definimos las notas las cuales tendran un titulo, un cuerpo o texto de la nota y el color asociado a la nota, para poder acceder a estos atributos se han creado las funciones como **getTitulo()** y **setTitulo()** y los demas de los atributos restantes y las funciones que nos permitirian poner el color del titulo y el cuerpo como son **printNotaTitulo()** y **printNotaCuerpo()** las cuales dependiendo del color nos mostraran por pantalla el titulo o el cuerpo de un color

## Clase Gestor

En esta clase es donde hemos definido las diferentes acciones que se han de llevar a cabo con las notas creadas y esas acciones son las siguientes:

  -Añadir una nota a la lista, en caso de ya existir una nota con el mismo nombre mostrar un error por pantalla, si no existiese se crearia con los parametros dados

  -Modificar una nota de la lista, si no existe la nota que se intenta modificar se mostrara un erros por pantalla, si existe la nota se modificara

  -Borrar una nota de la lista, si no existe la nota a eliminar se ha de mostrar un error por pantalla, si existe se eliminara la nota

  -Listar los titulos de las notas de la lista

  -Leer una nota concreta de la lista, si la nota existe en las listas se mostrara su contenido si no existe se mostrara un error por pantalla


### Añadir Notas

La funcion para añadir notas luce de la siguiente manera:

```typescript
addNota(user: string, new_nota: Nota){
    if (fs.existsSync(`./database/${user}`)){
      console.log(chalk.green('Existe el directorio'));
      fs.access(new_nota.getTitulo(), fs.constants.F_OK, (err) => {
        console.log(chalk.red((`${new_nota.getTitulo()} ${err ? 'Existe la nota' : 'No existe la nota'}`)));
      });
    } else {
      fs.mkdirSync(`./database/${user}`);
      const nota = {"Titulo": new_nota.getTitulo(), "Cuerpo": new_nota.getCuerpo(), "Color": new_nota.getColor()};
      const aux = JSON.stringify(nota);
      fs.writeFileSync(`./database/${user}/${new_nota.getTitulo()}`+ ".json", aux);
        console.log(chalk.green('Se ha añadido la nota'));
    }
  }
```

En esta funcion haremos uso del usuario para ponerlo como creador de la nota y mediante el cual se le creara un directorio con su nombre en el cual se guardaran sus notas, lo que haremos sera crear un fichero JSON con la informacion de la manera requerida: titulo, cuerpo y color y mediante el uso de `writeFileSync()` añadiremos la nota a la lista con la informacion antes especificada

### Modificar notas

La funcion para modificar notas luce de la siguiente manera:

```typescript
modNota(user: string, new_nota: Nota, aux2: string, titulo: string, cuerpo: string, color: string){
    if(fs.existsSync(`./database/${user}/${aux2}`)) {
      new_nota.setTitulo(titulo);
      new_nota.setCuerpo(cuerpo);
      new_nota.setColor(color)
      const nota = {"Titulo": new_nota.getTitulo(), "Cuerpo": new_nota.getCuerpo(), "Color": new_nota.getColor()};
      const aux = JSON.stringify(nota);
      fs.writeFileSync(`./database/${user}/${aux2}`, aux);
        console.log(chalk.green('Se ha modificado la nota'));
    } else {
      console.log(chalk.red("No existe la nota en la lista"));
    }
  }
```

En esta funcion haremos uso de los **set** de la clase nota lo que nos permitira modificar la informacion existente en la nota por otra informacion que el usuario proponga, y se crearia con los nuevos elementos formateados el fichero JSON, el cual añadiremos  a la lista con el metodo ``writeFileSync()`.

### Borrar notas

La funcion para borrar las notas luce de la siguiente manera:

```typescript
  delNota(user: string, aux: string){
    if(fs.existsSync(`./database/${user}/${aux}`)) {
      fs.rmSync(`./database/${user}/${aux}`);
        console.log(chalk.green('Se ha eliminado la nota'));
    } else {
      console.log(chalk.red("No existe la nota en la lista"));
    }
  }
```

En esta funcion buscaremos la nota que coincida con el nombre que el usuario pase por terminal, y mediante el metodo `rmSync()` borraremos el fichero que coincida con el nombre introducido por el usuario, asi como si no existiece ese fichero mostraremos un mensaje de error.

### Listar los titulos de las notas

La funcion para listar los titulos de las notas luce de la siguiente manerea: 

```typescript
  mostrarTitulos(user: string){
    let filename = fs.readdirSync(`./database/${user}`);

    console.log("\nTitulos de las notas de la lista: ");
    filename.forEach((file => {
      console.log(chalk.blue(file));
    }));
  }
```

En esta funcion lo que haremos sera listar todos los ficheros, es decir las notas, que se encuentre en el directorio del usuario indicado por el cliente mediante terminal, mostrando uno por uno de manera escalar los nombres de los ficheros que coinciden con los titulos de las notas que cada usuario crea.

### Leer una nota concreta de la lista

La funcion que nos permitira leer una nota conreta de la lista luce de la siguiente manera:

```typescript
  readNota(user: string, aux: string){
    if(fs.existsSync(`./database/${user}/${aux}`)){
      console.log(fs.readFileSync(`./database/${user}/${aux}`, "utf8"));
    } else {
      console.log(chalk.red('Error la nota no se encuentra en la lista'))
    }
  }
```

En esta funcion lo que haremos sera leer el contenido de una nota en concreto de la lista, para ello usaremos el metodo `readFileSync()` para poder leer la nota que el usuario haya especificado por terminal, tienes que ser mandado un usuario y la nota que quiera leer.

## Note-App

La funcion que se encargara de poder manejar la aplicacion mediante linea de comandos luce de la siguiente manera:

```typescript
/**
 * Comando que nos permite añadir una nota
 */
yargs.command({
  command: 'add',
  describe: 'Añadir una nueva nota',
  builder: {
    user: {
      describe: 'Usuario',
      demandOption: true,
      type: 'string',
    },
    title: {
      describe: 'Titulo',
      demandOption: true,
      type: 'string',
    },
    cuerpo: {
      describe: 'Cuerpo de la nota',
      demandOption: true,
      type: 'string',
    },
    color: {
      describe: 'Color de la nota',
      demandOption: true,
      type: 'string',
    },
  },
  handler(argv) {
    if (typeof argv.user === 'string' &&
        typeof argv.title === 'string' &&
        typeof argv.cuerpo === 'string' &&
        typeof argv.color === 'string') {
      // Required logic to add a new note
      console.log(new Gestor().addNota(argv.user, new Nota(argv.title, argv.cuerpo, argv.color)))
    }
  },
});

/**
 * Comando que nos permite modificar una nota
 */
yargs.command({
  command: 'mod',
  describe: 'Añadir una nueva nota',
  builder: {
    user: {
      describe: 'Usuario',
      demandOption: true,
      type: 'string',
    },
    title: {
      describe: 'Titulo',
      demandOption: true,
      type: 'string',
    },
    cuerpo: {
      describe: 'Cuerpo de la nota',
      demandOption: true,
      type: 'string',
    },
    color: {
      describe: 'Color de la nota',
      demandOption: true,
      type: 'string',
    },
    title2: {
      describe: 'Titulo a cambiar',
      demandOption: true,
      type: 'string',
    },
    cuerpo2: {
      describe: 'Cuerpo a cambiar',
      demandOption: true,
      type: 'string',
    },
    color2: {
      describe: 'Color a cambiar',
      demandOption: true,
      type: 'string',
    },
    fichero: {
      describe: 'Nombre fichero',
      demandOption: true,
      type: 'string',
    },
  },
  handler(argv) {
    if (typeof argv.user === 'string' &&
        typeof argv.title === 'string' &&
        typeof argv.cuerpo === 'string' &&
        typeof argv.color === 'string' && 
        typeof argv.title2 === 'string' &&
        typeof argv.cuerpo2 === 'string' &&
        typeof argv.color2 === 'string' &&
        typeof argv.fichero === 'string') {
      // Required logic to add a new note
      console.log(new Gestor().modNota(argv.user, new Nota(argv.title, argv.cuerpo, argv.color), argv.fichero, argv.title2, argv.cuerpo2, argv.color2))
    }
  },
});

/**
 * Comando que nos permite borrar una nota
 */
yargs.command({
  command: 'del',
  describe: 'Eliminar nota',
  builder: {
    user: {
      describe: 'Usuario',
      demandOption: true,
      type: 'string',
    },
    fichero: {
      describe: 'Nombre fichero',
      demandOption: true,
      type: 'string',
    },
  },
  handler(argv) {
    if (typeof argv.user === 'string' &&
        typeof argv.fichero === 'string') {
      // Required logic to add a new note
      console.log(new Gestor().delNota(argv.user, argv.fichero));
    }
  },
});

/**
 * Comando que nos permite mostrar una lista de titulos
 */
yargs.command({
  command: 'mostrar',
  describe: 'Mostrar titulos de las notas',
  builder: {
    user: {
      describe: 'Usuario',
      demandOption: true,
      type: 'string',
    },
  },
  handler(argv) {
    if (typeof argv.user === 'string') {
      // Required logic to add a new note
      console.log(new Gestor().mostrarTitulos(argv.user))
    }
  },
});

/**
 * Comando que nos permite leer una nota en concreto
 */
yargs.command({
  command: 'leer',
  describe: 'Leer una nota en cocreto',
  builder: {
    user: {
      describe: 'Usuario',
      demandOption: true,
      type: 'string',
    },
    fichero: {
      describe: 'Nombre fichero',
      demandOption: true,
      type: 'string',
    },
  },
  handler(argv) {
    if (typeof argv.user === 'string' &&
        typeof argv.fichero === 'string') {
      // Required logic to add a new note
      console.log(new Gestor().readNota(argv.user, argv.fichero))
    }
  },
});

/**
 * Comando que ejecutara el comando dado por el usuario
 */
yargs.parse()
```

En esta funcionalidad se define mediante el paquete **yargs** la interaccion del usuario con el programa mediante la linea de comandos de tal manera que al lado de la ejecucion en linea de comandos el usuario podra poner unas opciones las cuales les permitiran hacer las funciones definidas en la clase **gestor()**, se veria asi por ejemplo la funcion añadir una nota desde la linea de comandos:

```
node ./dist/note-app/note-app.js add --user="pepito" --title="prueba" --cuerpo="prueba" --color="Rojo"
```

siendo asi como se ejecutaria, pues tendrias la opcion de `add` seguido de los atributos que habria que pasarle a la funcion de añadir para poder lleevar a cabo la creacion de la nueva nota.


