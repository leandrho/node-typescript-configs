## process.argv

Recibir parámetros desde la consola utilizando el array `process.argv`. Este array contiene los argumentos de la línea de comandos pasados al script Node.js.

**¿Cómo funciona `process.argv`?**

* `process.argv` es un array que contiene los argumentos de la línea de comandos.
* Los primeros dos elementos del array siempre son:
    * `process.argv[0]`: La ruta absoluta al ejecutable de Node.js.
    * `process.argv[1]`: La ruta absoluta al script que se está ejecutando.
* Los argumentos adicionales que pases en la línea de comandos se almacenarán en los elementos restantes del array.

**Ejemplo básico:**

Crea un archivo llamado `mi-script.js` con el siguiente código:

```javascript
// mi-script.js
const args = process.argv.slice(2); // Obtener argumentos a partir del tercer elemento

console.log('Argumentos recibidos:');
args.forEach((arg, index) => {
  console.log(`${index + 1}: ${arg}`);
});
```

Ahora, ejecuta el script desde la consola con algunos argumentos:

```bash
node mi-script.js arg1 arg2 arg3
```

La salida será:

```
Argumentos recibidos:
1: arg1
2: arg2
3: arg3
```

**Manejo de argumentos con nombres (opciones):**

Si quieres pasar argumentos con nombres (opciones), puedes hacerlo de la siguiente manera:

```javascript
// mi-script.js
const args = process.argv.slice(2);
const options = {};

for (let i = 0; i < args.length; i++) {
  if (args[i].startsWith('--')) {
    const optionName = args[i].slice(2);
    if (i + 1 < args.length && !args[i + 1].startsWith('--')) {
      options[optionName] = args[i + 1];
      i++; // Saltar el valor del argumento
    } else {
      options[optionName] = true; // Opción booleana
    }
  }
}

console.log('Opciones recibidas:');
console.log(options);
```

Ejecuta el script con opciones:

```bash
node mi-script.js --nombre "Juan Perez" --edad 30 --activo
```

La salida será:

```
Opciones recibidas:
{ nombre: 'Juan Perez', edad: '30', activo: true }
```

**Consideraciones:**

* `process.argv` devuelve los argumentos como strings. Si necesitas convertirlos a otros tipos (números, booleanos), debes hacerlo manualmente.
* Para aplicaciones más complejas, considera usar librerías como `minimist` o `commander`, que facilitan el manejo de argumentos y opciones.

Espero que esta explicación te sea útil.
