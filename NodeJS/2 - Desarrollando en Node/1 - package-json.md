## Node.js - package.json

**¿Qué es y para qué sirve `package.json`?**

El archivo `package.json` es el corazón de cualquier proyecto Node.js. Es un archivo en formato JSON que contiene metadatos sobre el proyecto, como:

* **Nombre y versión:** Identifican el proyecto de forma única.
* **Dependencias:** Lista de los paquetes (librerías) que el proyecto necesita para funcionar.
* **Scripts:** Comandos personalizados que se pueden ejecutar con `npm` o `yarn`.
* **Información del autor y licencia:** Detalles sobre quién creó el proyecto y bajo qué términos se puede usar.

**Funciones principales:**

* **Gestión de dependencias:** Permite instalar y actualizar fácilmente las librerías necesarias para el proyecto.
* **Automatización de tareas:** Los scripts definidos en `package.json` permiten automatizar tareas comunes como iniciar el servidor, ejecutar pruebas o compilar código.
* **Distribución de paquetes:** Facilita la publicación y distribución de paquetes Node.js en el registro de npm.

**Configuración de scripts:**

La sección `scripts` en `package.json` define comandos personalizados que se pueden ejecutar con `npm run <nombre_del_script>`. Por ejemplo:

```json
"scripts": {
  "start": "node index.js",
  "test": "jest",
  "build": "webpack --mode production"
}
```

* `npm run start`: Ejecuta el comando `node index.js`, que normalmente inicia el servidor.
* `npm run test`: Ejecuta las pruebas unitarias con Jest.
* `npm run build`: Compila el código para producción con Webpack.

**Los dos puntos (:) en los scripts:**

Los dos puntos se utilizan para crear scripts con nombres más descriptivos y para organizar tareas relacionadas. Por ejemplo:

* `"start:dev": "nodemon index.js"`: Inicia el servidor en modo de desarrollo con `nodemon`, que reinicia el servidor automáticamente al detectar cambios en el código.
* `"start:prod": "node index.js"`: Inicia el servidor en modo de producción.
* `"build:dev": "webpack --mode development"`: Compila el codigo para desarrollo.
* `"build:prod": "webpack --mode production"`: Compila el codigo para produccion.

Esto permite tener diferentes configuraciones para el mismo script, dependiendo del entorno en el que se esté trabajando.

**Ejemplos de scripts comunes:**

* `"start": "node server.js"`: Inicia un servidor Node.js.
* `"dev": "nodemon server.js"`: Inicia un servidor con reinicio automático para desarrollo.
* `"test": "mocha"`: Ejecuta pruebas con Mocha.
* `"build": "webpack"`: Compila el código con Webpack.
* `"lint": "eslint ."`: Ejecuta un linter para verificar la calidad del código.
* `"format": "prettier --write ."`: Formatea el código automáticamente con Prettier.

**Ejemplo completo de `package.json`:**

```json
{
  "name": "mi-proyecto",
  "version": "1.0.0",
  "description": "Un proyecto de ejemplo",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "start:dev": "nodemon index.js",
    "test": "jest",
    "build": "webpack --mode production"
  },
  "dependencies": {
    "express": "^4.17.1"
  },
  "devDependencies": {
    "nodemon": "^2.0.7",
    "jest": "^26.6.3",
    "webpack": "^5.38.1",
    "webpack-cli": "^4.7.2"
  },
  "author": "Tu Nombre",
  "license": "MIT"
}
```
