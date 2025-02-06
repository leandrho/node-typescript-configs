# Aplicación de consola de texto en Node.js


## Yargs

Yargs es una librería de Node.js que simplifica la creación de herramientas de línea de comandos interactivas. Facilita el análisis de argumentos y la generación de interfaces de usuario intuitivas.

### ¿Para qué sirve Yargs?

Yargs ofrece las siguientes funcionalidades:

* **Análisis de argumentos**: Define opciones y comandos para tu programa de línea de comandos, y luego analiza los argumentos proporcionados por el usuario para extraer los valores.
* **Generación de menú de ayuda**: Crea automáticamente un menú de ayuda basado en las opciones y comandos definidos, lo que facilita a los usuarios entender cómo utilizar tu herramienta.
* **Validación de argumentos**: Define reglas para los argumentos, como tipos de datos requeridos o valores permitidos, y valida los argumentos proporcionados por el usuario para asegurar que sean correctos.
* **Interfaz de usuario interactiva**: Facilita la creación de interfaces de usuario interactivas en la línea de comandos, permitiendo a los usuarios navegar por diferentes opciones y comandos.

### ¿Cómo se usa Yargs con TypeScript?

Para utilizar Yargs con TypeScript, primero debes instalar las dependencias necesarias:

```bash
npm install yargs @types/yargs
```

Luego, puedes utilizarlo en tu código de la siguiente manera:

```typescript
import yargs from 'yargs';
import { hideBin } from 'yargs/helpers';

interface Args {
  nombre: string;
  apellido?: string;
}

const argv = yargs(hideBin(process.argv))
  .options({
    nombre: {
      alias: 'n',
      describe: 'Tu nombre',
      type: 'string',
      demandOption: true,
    },
    apellido: {
      alias: 'a',
      describe: 'Tu apellido',
      type: 'string',
    },
  })
  .command(
    'saludar',
    'Saluda al usuario',
    (yargs) => {
      return yargs.positional('nombre', {
        describe: 'Tu nombre',
        type: 'string',
      });
    },
    (argv) => {
      const args = argv as Args;
      console.log(`Hola, ${args.nombre} ${args.apellido || ''}!`);
    }
  )
  .help()
  .alias('help', 'h')
  .parse();

console.log(argv);
```

En este ejemplo, se define una interfaz `Args` para tipar los argumentos. Se define una opción `nombre` (obligatoria) y una opción opcional `apellido`. Luego, se define un comando `saludar` que recibe el nombre como argumento posicional. Al ejecutar el programa con la opción `--nombre` o el comando `saludar`, Yargs analizará los argumentos y ejecutará la función correspondiente.

```bash
    node mi-programa.js --nombre "John"
```
```
    Hola, John!
    { _: [], nombre: 'John' }
```

```bash
    node mi-programa.js saludar --nombre "John" --apellido "Doe"
```
```
    Hola, John Doe!
    { _: [ 'saludar' ], nombre: 'John', apellido: 'Doe' }
```

### Ejemplos de uso con TypeScript

* **Herramientas de desarrollo**: Yargs se utiliza comúnmente para crear herramientas de línea de comandos para desarrolladores, como herramientas de compilación, linters o generadores de código.
* **Scripts de automatización**: Puedes utilizar Yargs para crear scripts que realicen tareas automatizadas, como procesamiento de archivos o despliegue de aplicaciones.
* **Interfaces de usuario interactivas**: Yargs facilita la creación de interfaces de usuario interactivas en la línea de comandos, lo que puede ser útil para programas complejos con muchas opciones y comandos.

### En resumen

Yargs es una herramienta poderosa para crear herramientas de línea de comandos en Node.js. Facilita el análisis de argumentos, la generación de menús de ayuda y la creación de interfaces de usuario interactivas. Al utilizar TypeScript, puedes agregar tipado estático a tus argumentos y comandos, lo que mejora la calidad y mantenibilidad de tu código. Si necesitas crear una herramienta de línea de comandos, Yargs con TypeScript es una excelente opción.
