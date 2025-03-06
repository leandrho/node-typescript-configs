# Módulo fs

El módulo `fs` (*file system*) en **Node.js** permite interactuar con el sistema de archivos para leer, escribir, modificar y eliminar archivos y directorios. **Admite operaciones sincrónicas, asíncronas y basadas en promesas**.

---

# 📂 **Módulo `fs` en Node.js con TypeScript**
📌 Importamos `fs` en TypeScript:
```ts
import fs from 'fs';
import { promises as fsPromises } from 'fs'; // Para usar Promises
```

---

## **1. Operaciones de Archivos**

### 📌 **1.1. Leer un archivo**
### **Asíncrono con `fs.readFile()`**
```ts
fs.readFile('archivo.txt', 'utf-8', (err, data) => {
  if (err) throw err;
  console.log('Contenido:', data);
});
```
🔹 **No bloquea el hilo principal**.
🔹 **Siempre usa el encoding `utf-8`** si quieres obtener texto.

---

### **Asíncrono con Promesas (`fsPromises.readFile()`)**
```ts
async function leerArchivo() {
  try {
    const data = await fsPromises.readFile('archivo.txt', 'utf-8');
    console.log('Contenido:', data);
  } catch (err) {
    console.error('Error leyendo el archivo:', err);
  }
}
leerArchivo();
```

---

### **Sincrónico con `fs.readFileSync()`**
```ts
const data = fs.readFileSync('archivo.txt', 'utf-8');
console.log('Contenido:', data);
```
🔹 **Bloquea el hilo hasta que se termine de leer el archivo**.

---

### 📌 **1.2. Escribir en un archivo**
### **Asíncrono con `fs.writeFile()`**
```ts
fs.writeFile('archivo.txt', 'Hola, TypeScript!', 'utf-8', (err) => {
  if (err) throw err;
  console.log('Archivo guardado');
});
```
🔹 **Sobrescribe el archivo si ya existe**.

---

### **Asíncrono con Promesas (`fsPromises.writeFile()`)**
```ts
async function escribirArchivo() {
  try {
    await fsPromises.writeFile('archivo.txt', 'Contenido actualizado');
    console.log('Archivo escrito correctamente');
  } catch (err) {
    console.error('Error al escribir:', err);
  }
}
escribirArchivo();
```

---

### **Sincrónico con `fs.writeFileSync()`**
```ts
fs.writeFileSync('archivo.txt', 'Texto sincrónico', 'utf-8');
console.log('Archivo guardado');
```
🔹 **Bloquea el hilo hasta que termine la escritura**.

---

### 📌 **1.3. Agregar contenido a un archivo (`fs.appendFile`)**
```ts
fs.appendFile('archivo.txt', '\nNueva línea agregada', (err) => {
  if (err) throw err;
  console.log('Línea añadida');
});
```
🔹 **No sobrescribe el archivo, sino que agrega al final**.

---

### 📌 **1.4. Renombrar un archivo (`fs.rename`)**
```ts
fs.rename('archivo.txt', 'nuevo-nombre.txt', (err) => {
  if (err) throw err;
  console.log('Archivo renombrado');
});
```

---

### 📌 **1.5. Eliminar un archivo (`fs.unlink`)**
```ts
fs.unlink('archivo.txt', (err) => {
  if (err) throw err;
  console.log('Archivo eliminado');
});
```

---

## **2. Manejo de Directorios**

### 📌 **2.1. Crear un directorio (`fs.mkdir`)**
```ts
fs.mkdir('nueva-carpeta', (err) => {
  if (err) throw err;
  console.log('Carpeta creada');
});
```
🔹 **Si la carpeta ya existe, lanzará un error**.

📌 **Crear un directorio y evitar error si ya existe**:
```ts
fs.mkdir('nueva-carpeta', { recursive: true }, (err) => {
  if (err) throw err;
  console.log('Carpeta creada');
});
```
🔹 **`recursive: true`** crea directorios anidados si no existen.

---

### 📌 **2.2. Leer el contenido de un directorio (`fs.readdir`)**
```ts
fs.readdir('.', (err, archivos) => {
  if (err) throw err;
  console.log('Archivos en el directorio:', archivos);
});
```
🔹 **`'.'` indica el directorio actual**.

---

### 📌 **2.3. Eliminar un directorio (`fs.rmdir`)**
```ts
fs.rmdir('nueva-carpeta', { recursive: true }, (err) => {
  if (err) throw err;
  console.log('Carpeta eliminada');
});
```
🔹 **`recursive: true`** elimina todo el contenido de la carpeta.

---

## **3. Información de Archivos (`fs.stat`)**
```ts
fs.stat('archivo.txt', (err, stats) => {
  if (err) throw err;
  console.log('Es un archivo:', stats.isFile());
  console.log('Es un directorio:', stats.isDirectory());
  console.log('Tamaño:', stats.size, 'bytes');
});
```

---

## **4. Streams en `fs` (para manejar archivos grandes)**
### 📌 **4.1. Leer archivos con `fs.createReadStream`**
```ts
const stream = fs.createReadStream('archivo-grande.txt', 'utf-8');

stream.on('data', (chunk) => {
  console.log('Chunk recibido:', chunk);
});
stream.on('end', () => console.log('Lectura completada'));
stream.on('error', (err) => console.error('Error:', err));
```
🔹 **Ideal para archivos grandes** en lugar de `fs.readFile()`.

---

### 📌 **4.2. Escribir en archivos con `fs.createWriteStream`**
```ts
const writeStream = fs.createWriteStream('output.txt');

writeStream.write('Línea 1\n');
writeStream.write('Línea 2\n');
writeStream.end();
```

---

## **5. `fs.watch` (Monitoreo de cambios en archivos)**
```ts
fs.watch('archivo.txt', (eventType, filename) => {
  console.log(`El archivo ${filename} cambió: ${eventType}`);
});
```
🔹 **Detecta cambios en archivos y directorios en tiempo real**.

---

## **6. Promisify para Convertir Callbacks en Promesas**
Podemos usar `util.promisify` para convertir funciones de callback en Promesas.

```ts
import { promisify } from 'util';

const readFilePromise = promisify(fs.readFile);

async function leerArchivoPromisify() {
  try {
    const data = await readFilePromise('archivo.txt', 'utf-8');
    console.log('Contenido:', data);
  } catch (err) {
    console.error('Error leyendo:', err);
  }
}
leerArchivoPromisify();
```

---

## **Resumen 📝**
| Operación | Asíncrono (Callback) | Asíncrono (Promise) | Sincrónico |
|-----------|----------------------|---------------------|------------|
| Leer archivo | `fs.readFile` | `fsPromises.readFile` | `fs.readFileSync` |
| Escribir archivo | `fs.writeFile` | `fsPromises.writeFile` | `fs.writeFileSync` |
| Agregar contenido | `fs.appendFile` | `fsPromises.appendFile` | `fs.appendFileSync` |
| Eliminar archivo | `fs.unlink` | `fsPromises.unlink` | `fs.unlinkSync` |
| Crear directorio | `fs.mkdir` | `fsPromises.mkdir` | `fs.mkdirSync` |
| Leer directorio | `fs.readdir` | `fsPromises.readdir` | `fs.readdirSync` |
| Eliminar directorio | `fs.rmdir` | `fsPromises.rmdir` | `fs.rmdirSync` |

---
