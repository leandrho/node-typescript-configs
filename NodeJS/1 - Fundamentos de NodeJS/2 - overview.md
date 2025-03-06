# Node.js

---

# 🚀 **Características más importantes de Node.js**

Node.js es un entorno de ejecución de JavaScript diseñado para construir aplicaciones escalables y eficientes en el lado del servidor. A continuación, exploramos sus principales características:

---

## 🔹 **1. Asincronía y No Bloqueante**
Node.js utiliza un modelo de **I/O no bloqueante**, lo que significa que puede manejar múltiples tareas simultáneamente sin esperar a que cada operación termine antes de ejecutar la siguiente.

Ejemplo de operación **sincrónica** (bloqueante):
```ts
import fs from 'fs';

const data = fs.readFileSync('archivo.txt', 'utf-8'); // Bloquea el hilo hasta que termine
console.log(data);
console.log("Este mensaje se muestra después de leer el archivo.");
```
**Salida esperada:**
```
(contenido de archivo.txt)
Este mensaje se muestra después de leer el archivo.
```
🔹 **Problema**: Mientras se lee el archivo, Node.js **no puede ejecutar otra tarea**.

---

Ejemplo de operación **asíncrona** (no bloqueante):
```ts
import fs from 'fs';

fs.readFile('archivo.txt', 'utf-8', (err, data) => {
  if (err) throw err;
  console.log(data);
});

console.log("Este mensaje se muestra antes de leer el archivo.");
```
**Salida esperada:**
```
Este mensaje se muestra antes de leer el archivo.
(contenido de archivo.txt)
```
🔹 **Explicación**: `readFile()` se ejecuta en segundo plano y no bloquea el flujo principal.

---

## 🔹 **2. Event Loop**
El **Event Loop** es el corazón de Node.js. Permite manejar múltiples tareas de manera eficiente en un solo hilo.

📌 **Ciclo del Event Loop:**
1. **Timers** → Ejecuta `setTimeout()` y `setInterval()`.
2. **I/O Callbacks** → Maneja respuestas de archivos, redes, etc.
3. **Idle & Prepare** → Uso interno de Node.js.
4. **Poll** → Procesa eventos de I/O (HTTP, archivos, etc.).
5. **Check** → Ejecuta `setImmediate()`.
6. **Close Callbacks** → Maneja eventos de cierre (`socket.on('close')`).

Ejemplo:
```ts
setTimeout(() => console.log('setTimeout'), 0);
setImmediate(() => console.log('setImmediate'));
process.nextTick(() => console.log('nextTick'));
```
**Salida esperada:**
```
nextTick
setTimeout (o setImmediate, depende del sistema)
setImmediate (o setTimeout)
```
🔹 **Explicación**:
- `process.nextTick()` tiene mayor prioridad que `setTimeout()` y `setImmediate()`.

---

## 🔹 **3. libuv**
Node.js usa **libuv**, una biblioteca en C++ que proporciona:
✅ **Manejo del Event Loop**
✅ **I/O asíncrono** (red, archivos, etc.)
✅ **Soporte para múltiples hilos (thread pool)**

🔹 **Importante**: Aunque Node.js es **single-threaded**, algunas operaciones (como `fs` o `crypto`) usan un **pool de hilos** en **libuv** para tareas intensivas.

Ejemplo con `crypto` (usa thread pool):
```ts
import crypto from 'crypto';

console.time('hash');
crypto.pbkdf2Sync('password', 'salt', 100000, 64, 'sha512');
console.timeEnd('hash'); // Ejecutado en thread pool
```

---

## 🔹 **4. npm (Node Package Manager)**
📌 **npm** es el gestor de paquetes de Node.js.
✅ Permite instalar, actualizar y gestionar dependencias.
✅ Se encuentra en `package.json`.

Ejemplo de instalación:
```sh
npm install express
```
Ejemplo de `package.json`:
```json
{
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

---

## 🔹 **5. npx (Node Package eXecute)**
📌 **npx** ejecuta paquetes sin necesidad de instalarlos globalmente.
Ejemplo con `create-react-app`:
```sh
npx create-react-app mi-proyecto
```
Diferencia clave entre **npm** y **npx**:
- `npm install -g paquete` → Lo instala globalmente.
- `npx paquete` → Lo ejecuta sin instalar.

---

## 🔹 **6. Node.js es Single-threaded pero usa Thread Pool**
Node.js ejecuta JavaScript en **un solo hilo** pero usa un **pool de hilos** para operaciones costosas.

Ejemplo de uso del thread pool:
```ts
import { Worker } from 'worker_threads';

const worker = new Worker('./worker.js');
worker.on('message', msg => console.log(msg));
worker.postMessage('Hola');
```

---

## 🔹 **7. Módulos en Node.js**
### **🔹 a) Módulos Nativos**
Node.js incluye módulos integrados como:
✅ `fs` → Manejo de archivos
✅ `http` → Servidores web
✅ `crypto` → Cifrado
✅ `os` → Información del sistema
✅ `path` → Manejo de rutas

Ejemplo con `fs`:
```ts
import fs from 'fs';

fs.writeFileSync('archivo.txt', 'Hola, Node.js!');
```

---

### **🔹 b) Módulos de Terceros**
Se instalan con **npm** y amplían las capacidades de Node.js.

Ejemplo con **Express.js**:
```ts
import express from 'express';

const app = express();

app.get('/', (req, res) => {
  res.send('Hola desde Express!');
});

app.listen(3000, () => console.log('Servidor corriendo en http://localhost:3000'));
```

---

### **🔹 c) CommonJS vs ESM**
Node.js soporta **CommonJS (require)** y **ESM (import/export)**.

**CommonJS:**
```ts
const fs = require('fs');
```
**ESM:**
```ts
import fs from 'fs';
```
Para usar **ESM**, agrega `"type": "module"` en `package.json`.

---

## **🚀 Conclusión**
Node.js es poderoso por:
✅ **I/O No bloqueante y Event Loop**
✅ **libuv y Thread Pool para tareas pesadas**
✅ **npm y npx para gestionar paquetes**
✅ **Módulos nativos y de terceros**
✅ **Soporte para CommonJS y ESM**

