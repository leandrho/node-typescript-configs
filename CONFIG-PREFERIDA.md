
# **Node.js con TypeScript usando TS-Node-Dev** (Opción recomendada)

## **1️⃣ Instalación de TypeScript y dependencias necesarias**
Ejecuta el siguiente comando para instalar TypeScript junto con las herramientas necesarias para el desarrollo:

```sh
npm i -D typescript @types/node ts-node-dev rimraf
```

- `typescript` → Compilador de TypeScript.
- `@types/node` → Definiciones de tipos para Node.js.
- `ts-node-dev` → Reinicia automáticamente el servidor al detectar cambios en los archivos `.ts`.
- `rimraf` → Elimina la carpeta `dist/` antes de compilar (equivalente a `rm -rf` en Unix).

---

## **2️⃣ Configuración de TypeScript**
Genera un archivo de configuración `tsconfig.json` con la siguiente instrucción:

```sh
npx tsc --init --outDir dist --rootDir src
```

> 🎯 **Nota**: Puedes personalizar `tsconfig.json` según tus necesidades.

---

## **3️⃣ Scripts en `package.json`**
Añade los siguientes scripts para facilitar el desarrollo, la compilación y la ejecución:

```json
"scripts": {
  "dev": "tsnd --respawn --clear src/app.ts",
  "build": "rimraf dist && tsc",
  "start": "npm run build && node dist/app.js"
}
```

### **Explicación de cada script:**
- **`dev`** → Ejecuta el proyecto en modo desarrollo con `ts-node-dev`, reiniciando automáticamente cuando hay cambios.
- **`build`** → Borra `dist/` y compila el código TypeScript en `src/` hacia `dist/`.
- **`start`** → Compila el código y luego ejecuta la versión compilada con Node.js.

> 🔗 **Más sobre TS-Node-Dev**: [npmjs.com/package/ts-node-dev](https://www.npmjs.com/package/ts-node-dev)

---

Con esta configuración, puedes desarrollar en **Node.js con TypeScript** de forma rápida y eficiente. 🚀
