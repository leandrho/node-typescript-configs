## Nodemon

### ¿Qué es Nodemon?

Nodemon es una herramienta de línea de comandos que monitorea automáticamente los cambios en tus archivos de código fuente de Node.js y reinicia tu aplicación cuando detecta un cambio. Es muy útil durante el desarrollo, ya que te permite ver los efectos de tus cambios de código inmediatamente sin tener que reiniciar manualmente tu servidor cada vez.

### ¿Para qué sirve Nodemon?

* **Desarrollo más rápido:**
    * Ahorra tiempo al eliminar la necesidad de reiniciar manualmente el servidor después de cada cambio de código.
    * Permite un flujo de trabajo de desarrollo más fluido y eficiente.
* **Detección de errores:**
    * Facilita la detección temprana de errores, ya que los cambios se reflejan inmediatamente.
    * Ayuda a ver el impacto de los cambios al momento.
* **Mejora la productividad:**
    * Permite a los desarrolladores centrarse en escribir código en lugar de realizar tareas repetitivas.

### ¿Cómo funciona Nodemon?

1.  **Monitoreo de archivos:**
    * Nodemon monitorea los archivos en tu directorio de proyecto en busca de cambios.
2.  **Detección de cambios:**
    * Cuando se detecta un cambio en un archivo, Nodemon reinicia automáticamente tu aplicación Node.js.
3.  **Reinicio del servidor:**
    * Nodemon ejecuta el script de inicio de tu aplicación, lo que reinicia el servidor.
4.  **Configuración:**
    * Nodemon se puede configurar para monitorear tipos de archivos específicos, ignorar ciertos directorios y ejecutar comandos personalizados antes o después de reiniciar la aplicación.

### ¿Cómo usar Nodemon?

1.  **Instalación:**
    * Puedes instalar Nodemon globalmente o como una dependencia de desarrollo en tu proyecto.
        * Instalación global: `npm install -g nodemon`
        * Instalación local: `npm install --save-dev nodemon`
2.  **Ejecución:**
    * Para ejecutar tu aplicación con Nodemon, simplemente reemplaza el comando `node` con `nodemon`. Por ejemplo:
        * `nodemon mi-archivo.js`
    * Tambien puedes usarlo en los scripts del package.json como se explico anteriormente.
3.  **Configuración:**
    * Puedes configurar Nodemon creando un archivo `nodemon.json` en tu directorio de proyecto.
    * Este archivo te permite definir opciones como los archivos que se deben monitorear, los directorios que se deben ignorar y los comandos que se deben ejecutar.

### Ejemplo de uso:

Supongamos que tienes un archivo `server.js` que inicia un servidor Node.js. En lugar de ejecutar `node server.js`, puedes ejecutar `nodemon server.js`. Nodemon monitoreará los cambios en `server.js` y reiniciará el servidor automáticamente cada vez que realices un cambio.

**Ejemplo de archivo de configuración nodemon.json:**

```json
{
  "watch": ["src"],
  "ext": ".ts,.js",
  "ignore": [],
  "exec": "ts-node ./src/index.ts"
  "env": {
    "NODE_ENV": "development"
  }
}
```

**Donde**:
- `watch`: Directorios a observar.
- `ext`: Extensiones de archivo a monitorear.
- `ignore`: Archivos/directorios a ignorar.
- `exec`: Comando para ejecutar el entrypoint.
- `env`: Variables de entorno.

### No usar en producción

No se debe usar en producción, siempre tiene que estar como dependencia de desarrollo.

### En resumen:

Nodemon es una herramienta esencial para el desarrollo de aplicaciones Node.js. Simplifica el proceso de desarrollo al automatizar el reinicio del servidor, lo que te permite centrarte en escribir código y mejorar tu productividad.
