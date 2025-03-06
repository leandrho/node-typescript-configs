## 🔄 **¿Qué es el Event Loop en Node.js?**
El **Event Loop** es el mecanismo que permite a Node.js manejar operaciones asíncronas de manera eficiente en un solo hilo, sin bloquear la ejecución del código.

📌 **Características principales:**

✔ **No bloqueante**: Node.js no espera a que una tarea finalice para seguir ejecutando el código.

✔ **Asíncrono**: Usa callbacks, Promises y `async/await` para manejar tareas en segundo plano.

✔ **Basado en eventos**: Node.js escucha eventos y los procesa cuando están listos.

---

## ⚙ **¿Cómo funciona?**
El **Event Loop** se ejecuta en un solo hilo y sigue estos pasos en cada iteración:

1️⃣ **Ejecuta código síncrono** (inicialización del script).

2️⃣ **Procesa timers** (`setTimeout`, `setInterval`).

3️⃣ **Ejecuta callbacks de I/O** (como lectura/escritura de archivos).

4️⃣ **Procesa Promises** en la cola de microtareas.

5️⃣ **Maneja eventos adicionales** y vuelve a empezar.

### 🔥 **Ejemplo básico**
```ts
console.log('Inicio');

setTimeout(() => {
  console.log('Timeout ejecutado');
}, 0);

Promise.resolve().then(() => console.log('Promise ejecutada'));

console.log('Fin');
```
📌 **Salida en consola:**
```
Inicio
Fin
Promise ejecutada
Timeout ejecutado
```
**¿Por qué esta secuencia?**

1️⃣ **Ejecuta** `console.log('Inicio')` y `console.log('Fin')` (código síncrono).

2️⃣ **Coloca** `setTimeout` en la cola de **Timers**.

3️⃣ **Coloca** la Promise en la cola de **Microtareas** y la ejecuta **antes** del timeout.

4️⃣ **Ejecuta** el callback de `setTimeout`.

---

## 🌀 **Fases del Event Loop en Node.js**
El **Event Loop** tiene **6 fases** principales:

| Fase | Descripción | Ejemplo |
|------|------------|---------|
| **Timers** | Ejecuta `setTimeout` y `setInterval` si el tiempo ha expirado. | `setTimeout(() => {}, 1000)` |
| **I/O Callbacks** | Ejecuta callbacks de operaciones de I/O (lectura de archivos, sockets). | `fs.readFile('archivo.txt', callback)` |
| **Idle/Prepare** | Interno de Node.js (no se usa normalmente). | - |
| **Poll** | Obtiene nuevas operaciones de I/O y las ejecuta si están listas. | Servidor HTTP con `http.createServer()` |
| **Check** | Ejecuta callbacks de `setImmediate()`. | `setImmediate(() => {})` |
| **Close Callbacks** | Ejecuta callbacks de cierre como `socket.on('close')`. | `server.close()` |

📌 **Microtareas (Promesas y `process.nextTick`)** se ejecutan entre cada fase.

---

## 🆚 **Diferencias entre `setTimeout`, `setImmediate` y `process.nextTick`**
| Método | ¿Cuándo se ejecuta? | Prioridad |
|--------|---------------------|-----------|
| `setTimeout(cb, 0)` | En la fase de Timers, después de un pequeño retraso. | ✅ Baja |
| `setImmediate(cb)` | En la fase Check, después de la fase Poll. | ✅ Media |
| `process.nextTick(cb)` | Antes de cambiar de fase en el Event Loop. | 🔥 Más alta |

### 📌 **Ejemplo práctico**
```ts
setTimeout(() => console.log('setTimeout'), 0);
setImmediate(() => console.log('setImmediate'));
process.nextTick(() => console.log('nextTick'));

console.log('Fin del script');
```
📌 **Salida esperada:**
```
Fin del script
nextTick
setTimeout / setImmediate (depende de la carga)
```

---

## 🔥 **¿Por qué es importante el Event Loop?**
✔ **Evita bloqueos** en la ejecución.
✔ **Maneja miles de peticiones sin usar múltiples hilos.**
✔ **Optimiza el rendimiento** en tareas de I/O.
