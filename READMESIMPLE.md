## Node.js con TypeScript - Simplificado

### 1. Instala dependencias

```bash
npm i -D typescript @types/node ts-node nodemon rimraf
```

### 2. Inicializa TypeScript

```bash
npx tsc --init --outDir dist/ --rootDir src
```

### 3. Configura Nodemon

**.nodemon.json**

```json
{
  "watch": ["src"],
  "ext": ".ts",
  "exec": "ts-node ./src/index.ts"
}
```

### 4. Scripts en `package.json`

```json
"scripts": {
    "dev": "nodemon",
    "build": "rimraf ./dist && tsc",
    "start": "npm run build && node dist/index.js"
  }
```

### 5. Archivo principal

**src/index.ts**

```typescript
console.log("¡Hola mundo!");
```

### 6. Ejecuta

*   **Desarrollo:** `npm run dev`
*   **Producción:** `npm run start`

### Recomendaciones

*   A medida que tu proyecto avance, considera agregar las opciones de configuración adicionales (`--esModuleInterop`, `--resolveJsonModule`, etc.) para mejorar la interoperabilidad y el manejo de módulos.
*   Explora las opciones de configuración de TypeScript en `tsconfig.json` para personalizar el comportamiento del compilador.
