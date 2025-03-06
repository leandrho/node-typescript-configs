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

**Recomendación:** En el tsconfig.json se puede incluir y excluir archivos y/o directorios, para el caso de node se recomienda excluir __node_modules__
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

---

## Definiciones de tipos
Los paquetes de **definiciones de tipos** (como los que empiezan con @types/ en npm) son archivos que describen la estructura de una biblioteca o módulo JavaScript para que TypeScript pueda entenderlo.

Los **`@types/node`** son paquetes de **definiciones de tipos TypeScript** para el entorno de ejecución **Node.js**. Proporcionan información sobre los tipos de datos (interfaces, funciones, clases, etc.) de los módulos nativos de Node.js (como `fs`, `http`, `path`, etc.), permitiendo que TypeScript entienda y valide el código que utiliza estas APIs.

---

### **¿Para qué sirven?**
1. **Habilitar el soporte de TypeScript para bibliotecas JS**:
   - TypeScript necesita conocer los **tipos de datos** (funciones, clases, parámetros, objetos, etc.) de una biblioteca JavaScript para validar tu código.
   - Estas definiciones actúan como un "manual de instrucciones" para TypeScript.

2. **Mejorar la experiencia de desarrollo**:
   - Autocompletado inteligente en editores (VS Code, WebStorm, etc.).
   - Verificación de errores en tiempo real (ej: parámetros incorrectos).
   - Documentación integrada (hover sobre funciones para ver descripciones).

---

### **¿Qué hacen exactamente?**
- **Definen tipos**: Estructuras como `interfaces`, `types`, `clases`, etc., para funciones y objetos de una biblioteca.
- **Declaran módulos**: Indican cómo se exportan/importan los elementos (ej: `export function sum(a: number, b: number): number`).
- **Agregan metadata**: Descripciones de parámetros, valores de retorno, errores, etc.

---

### **Contexto**
- **TypeScript** necesita conocer la estructura de las bibliotecas JavaScript para trabajar con ellas.
- **Node.js** está escrito en JavaScript, por lo que TypeScript no tiene información de tipos por defecto.
- **DefinitelyTyped**: Es un repositorio comunitario que aloja definiciones de tipos. Los paquetes `@types/*` (como `@types/node`) se publican desde aquí.

---

### **¿Cómo se usan?**
1. **Instalación**:
   ```bash
   npm install --save-dev @types/node
   ```
   o
   ```bash
   yarn add -D @types/node
   ```

2. **En tu código TypeScript**:
   ```typescript
   import fs from 'fs'; // TypeScript ahora conoce los tipos de 'fs'
   ```

---

### **Ejemplo práctico**
Sin `@types/node`, TypeScript no sabe qué parámetros acepta `fs.readFile`:
```typescript
// Sin @types/node
fs.readFile('file.txt', (err, data) => {
  // TypeScript no verifica tipos de 'err' o 'data'
});
```

Con `@types/node`:
```typescript
// Con @types/node
fs.readFile('file.txt', (err: NodeJS.ErrnoException | null, data: Buffer) => {
  // TypeScript valida que 'err' sea null o un error, y 'data' un Buffer
});
```

---

### **¿Cuándo son necesarios?**
- Siempre que uses **TypeScript** con **Node.js**.
- Si usas una versión moderna de Node.js (v16+), algunas definiciones pueden estar incluidas, pero `@types/node` asegura cobertura completa y actualizada.

---

### **Notas clave**
- **Dependencia de desarrollo**: No se incluyen en el código de producción.
- **Versiones**: Deben coincidir con la versión de Node.js que uses (ej: `@types/node@18` para Node.js 18).
- **Alternativa**: Si una biblioteca ya incluye tipos (como `"types"` en su `package.json`), no necesitas `@types/nombre-biblioteca`.
