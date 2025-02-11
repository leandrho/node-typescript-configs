## Dotenv en Node.js y TypeScript: Gestión de Variables de Entorno de Forma Segura

En el desarrollo de aplicaciones, especialmente aquellas que se ejecutan en diferentes entornos (desarrollo, pruebas, producción), es crucial manejar de forma segura la información sensible, como contraseñas, claves de API y otros datos de configuración. **Dotenv** surge como una herramienta fundamental en Node.js y TypeScript para abordar este desafío.

### ¿Qué es Dotenv?

Dotenv es un módulo de Node.js que permite cargar variables de entorno desde un archivo `.env` a `process.env`. Esto facilita la gestión de la configuración de la aplicación, separando los datos sensibles del código fuente y adaptándolos a cada entorno.

### ¿Para qué sirve Dotenv?

* **Seguridad**: Almacenar información sensible en archivos `.env` separados del código fuente, se reduce el riesgo de exponerla en repositorios o entornos públicos.
* **Flexibilidad**: Permite configurar la aplicación para diferentes entornos sin modificar el código. Cada entorno puede tener su propio archivo `.env` con valores específicos.
* **Organización**: Facilita la gestión de variables de entorno al centralizarlas en un archivo `.env`.
* **Simplicidad**: Su uso es sencillo y se integra fácilmente en proyectos Node.js y TypeScript.

### ¿Cómo funciona Dotenv?

1. **Instalación:** Se instala el paquete `dotenv` mediante npm o yarn:
   ```bash
   npm install dotenv
   ```

2. **Creación del archivo `.env`:** Se crea un archivo llamado `.env` en la raíz del proyecto. Este archivo contendrá las variables de entorno con el formato `CLAVE=VALOR`:
   ```
   DATABASE_URL=postgres://usuario:contraseña@host:puerto/basededatos
   API_KEY=abcdef123456
   PORT=3000
   ```

3. **Carga de las variables:** En el archivo principal de la aplicación (generalmente `index.js` o `server.ts`), se requiere y configura `dotenv` al principio del código:
   ```javascript
   require('dotenv').config();
   ```

4. **Acceso a las variables:** Las variables de entorno se pueden acceder a través de `process.env`:
   ```javascript
   console.log(process.env.DATABASE_URL); // postgres://usuario:contraseña@host:puerto/basededatos
   console.log(process.env.API_KEY); // abcdef123456
   console.log(process.env.PORT); // 3000
   ```

### Complemento con `env-var`

Si bien `dotenv` carga las variables de entorno, no proporciona validación ni tipado. Para esto, se puede utilizar la librería `env-var`:

1. **Instalación:**
   ```bash
   npm install env-var
   ```

2. **Validación y tipado:**
   ```javascript
   const env = require('env-var');

   const DATABASE_URL = env.get('DATABASE_URL').required().asString();
   const PORT = env.get('PORT').default(3000).asInt();

   console.log(DATABASE_URL); // postgres://usuario:contraseña@host:puerto/basededatos
   console.log(PORT); // 3000
   ```

`env-var` permite definir reglas para las variables de entorno, como si son obligatorias, valores por defecto y el tipo de dato esperado. Esto ayuda a prevenir errores y asegura que la aplicación se ejecute con la configuración correcta.

### Consideraciones adicionales

* El archivo `.env` no debe ser incluido en el repositorio Git. Se recomienda agregarlo al archivo `.gitignore`.
* Para entornos de producción, es común utilizar herramientas de gestión de secretos como HashiCorp Vault, AWS Secrets Manager o Google Cloud Secret Manager.

En resumen, `dotenv` es una herramienta esencial para gestionar variables de entorno en Node.js y TypeScript de forma segura y eficiente. Su combinación con `env-var` permite validar y tipar las variables, mejorando la calidad y robustez del código.
