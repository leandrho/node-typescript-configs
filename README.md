## Pasos para usar Node con TypeScript y Nodemon

Aquí tienes los pasos para usar Node con TypeScript y Nodemon, junto con explicaciones y algunas mejoras:

**1. Instala las dependencias:**

```bash
npm i -D typescript @types/node ts-node nodemon rimraf
```

*   `typescript`: El compilador de TypeScript.
*   `@types/node`: Definiciones de tipos para Node.js.
*   `ts-node`: Permite ejecutar archivos TypeScript directamente en Node.js.
*   `nodemon`: Reinicia el servidor automáticamente cuando hay cambios en el código.
*   `rimraf`: Elimina el directorio de distribución antes de cada compilación (opcional, pero recomendado).

**2. Inicializa el archivo de configuración de TypeScript:**

```bash
npx tsc --init --outDir dist/ --rootDir src --esModuleInterop true --resolveJsonModule true
```

*   `--outDir dist/`: Especifica el directorio de salida para los archivos JavaScript compilados.
*   `--rootDir src`: Especifica el directorio raíz de los archivos TypeScript.
*   `--esModuleInterop true`: Habilita la interoperabilidad entre módulos CommonJS y ES modules.
*   `--resolveJsonModule true`: Permite importar archivos JSON como módulos.

Recomentación: En el tsconfig.json se puede incluir y excluir archivos y/o directorios, para el caso de node se recomienda excluir __node_modules__
e incluir __`src/**/*`__. Para esto agregar el __exclude__ y el __include__ en el archivo tsconfig.json

```json
{
  "exclude": ["node_modules"],
  "include": ["src/**/*"],
  "compilerOptions" : {
    /* all options */
  }
}
```


**3. Crea el archivo de configuración de Nodemon:**

**.nodemon.json**

```json
{
  "watch": ["src"],
  "ext": ".ts,.js",
  "ignore": [],
  "exec": "ts-node ./src/index.ts"
}
```

*   `watch`: Directorios a observar para cambios.
*   `ext`: Extensiones de archivo a observar.
*   `ignore`: Archivos o directorios a ignorar.
*   `exec`: Comando a ejecutar cuando hay cambios.

**4. Configura los scripts en `package.json`:**

```json
"scripts": {
    "dev": "nodemon",
    "build": "rimraf ./dist && tsc",
    "start": "npm run build && node dist/index.js"
  }
```

*   `dev`: Inicia el servidor en modo de desarrollo con Nodemon.
*   `build`: Compila el código TypeScript en JavaScript.
*   `start`: Inicia el servidor en modo de producción.

**5. Crea el archivo principal de la aplicación:**

**src/index.ts**

```typescript
console.log("¡Hola desde Node.js con TypeScript y Nodemon!");
```

**6. Ejecuta la aplicación:**

*   **Desarrollo:** `npm run dev`
*   **Producción:** `npm run start`

**Mejoras:**

*   **`--esModuleInterop true` y `--resolveJsonModule true`:** Facilitan la importación de módulos CommonJS y archivos JSON en proyectos TypeScript.
*   **`rimraf`:** Asegura una compilación limpia al eliminar el directorio de salida antes de cada compilación.
*   **`index.ts`:** Nombre más común para el archivo principal de la aplicación.

**Información adicional:**

*   Puedes personalizar el archivo de configuración de TypeScript (`tsconfig.json`) para ajustar las opciones del compilador.
*   Puedes agregar más scripts en `package.json` para tareas como pruebas, linting, etc.
