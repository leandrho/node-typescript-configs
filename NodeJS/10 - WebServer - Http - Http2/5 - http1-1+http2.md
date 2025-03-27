Análisis detallado de HTTP/1.1 y HTTP/2 en **Node.js con TypeScript, Arquitectura Limpia y Express**, incluyendo sus diferencias, ventajas y una implementación práctica.

---

## **📌 HTTP/1.1 vs HTTP/2: ¿Qué debes saber?**
### 🔹 **HTTP/1.1 (1997)**
- Usa **conexiones persistentes** (mantiene la conexión abierta para múltiples solicitudes).
- Cada solicitud debe esperar la respuesta antes de enviar otra (**Head-of-line blocking**).
- No soporta **multiplexing** (envío simultáneo de varias respuestas en una sola conexión).
- Soporta **compresión de encabezados** con `gzip` pero no de manera eficiente.
- Usa **pipelining**, pero rara vez se implementa correctamente por problemas de enrutamiento.

### 🔹 **HTTP/2 (2015)**
- **Multiplexing**: Varias respuestas pueden viajar en paralelo por la misma conexión.
- **Compresión de encabezados** con **HPACK** (reduce la sobrecarga en cada petición).
- **Server Push**: Permite al servidor enviar recursos antes de que el cliente los solicite.
- **Binary protocol** en lugar de texto plano, lo que mejora el rendimiento.
- **Menos latencia** y **uso más eficiente del ancho de banda**.

---

## **📌 Configuración en Node.js con Express y TypeScript**
Implementaremos **HTTP/1.1 y HTTP/2**, utilizando una arquitectura limpia.

---

## **1️⃣ Instalación de dependencias**
Ejecuta:
```sh
npm install express http2 fs dotenv
npm install --save-dev @types/express @types/node
```

---

## **2️⃣ Estructura del Proyecto**
```plaintext
📂 src
 ├── 📂 config
 │    ├── env.ts
 │    ├── serverConfig.ts
 ├── 📂 controllers
 │    ├── homeController.ts
 ├── 📂 server
 │    ├── httpServer.ts
 │    ├── http2Server.ts
 ├── .env
 ├── tsconfig.json
```

---

## **3️⃣ Variables de Entorno (`.env`)**
```plaintext
PORT=3000
HTTPS_PORT=3443
CERT_PATH=./certs/cert.pem
KEY_PATH=./certs/key.pem
```

---

## **4️⃣ Carga de Variables (`config/env.ts`)**
```typescript
import dotenv from "dotenv";
dotenv.config();

export const env = {
  PORT: process.env.PORT || "3000",
  HTTPS_PORT: process.env.HTTPS_PORT || "3443",
  CERT_PATH: process.env.CERT_PATH || "./certs/cert.pem",
  KEY_PATH: process.env.KEY_PATH || "./certs/key.pem",
};
```

---

## **5️⃣ Configuración del Servidor (`config/serverConfig.ts`)**
```typescript
import express from "express";

export const createApp = () => {
  const app = express();
  app.use(express.json());
  return app;
};
```

---

## **6️⃣ Controlador (`controllers/homeController.ts`)**
```typescript
import { Request, Response } from "express";

export class HomeController {
  static async getHome(req: Request, res: Response) {
    res.json({ message: "Hello from HTTP Server!" });
  }
}
```

---

## **7️⃣ Servidor HTTP/1.1 (`server/httpServer.ts`)**
```typescript
import http from "http";
import { createApp } from "../config/serverConfig";
import { HomeController } from "../controllers/homeController";
import { env } from "../config/env";

const app = createApp();

// Rutas
app.get("/", HomeController.getHome);

const server = http.createServer(app);

server.listen(env.PORT, () => {
  console.log(`HTTP Server running on http://localhost:${env.PORT}`);
});
```

---

## **8️⃣ Servidor HTTP/2 con TLS (`server/http2Server.ts`)**
```typescript
import fs from "fs";
import http2 from "http2";
import { createApp } from "../config/serverConfig";
import { HomeController } from "../controllers/homeController";
import { env } from "../config/env";

const app = createApp();

// Cargar certificados SSL
const options = {
  key: fs.readFileSync(env.KEY_PATH),
  cert: fs.readFileSync(env.CERT_PATH),
};

// Rutas
app.get("/", HomeController.getHome);

const server = http2.createSecureServer(options, app);

server.listen(env.HTTPS_PORT, () => {
  console.log(`HTTP/2 Server running on https://localhost:${env.HTTPS_PORT}`);
});
```

---

## **📌 Pruebas y Uso**
1. **Ejecuta el servidor HTTP/1.1**:
   ```sh
   npx ts-node src/server/httpServer.ts
   ```
   Visita: http://localhost:3000

2. **Ejecuta el servidor HTTP/2**:
   ```sh
   npx ts-node src/server/http2Server.ts
   ```
   Visita: https://localhost:3443
   *(Necesitarás un certificado válido o aceptar la advertencia en el navegador).*

---

## **📌 Comparación de Rendimiento**
Puedes probar la diferencia con **`curl`**:

🔹 **HTTP/1.1**
```sh
curl -v http://localhost:3000
```

🔹 **HTTP/2**
```sh
curl -v --http2 https://localhost:3443 --insecure
```

---

## **🔥 Resumen Final**
✔ **HTTP/1.1** usa conexiones persistentes pero tiene bloqueos en serie.
✔ **HTTP/2** soporta **multiplexing, compresión de headers y Server Push**.
✔ Implementamos **ambos protocolos en Express con TypeScript**.
✔ **HTTP/2** requiere certificados SSL para operar en modo seguro.
✔ Probamos el rendimiento con `curl`.


## Comparativa detallada entre **HTTP/1.1 y HTTP/2**, con ventajas, desventajas y cómo afectan el rendimiento en Node.js.

---

# **🔍 HTTP/1.1 vs HTTP/2: Diferencias Claves**
| Característica         | HTTP/1.1 🏛️  | HTTP/2 🚀 |
|-----------------------|-------------|----------|
| **Formato**           | Texto plano | Binario |
| **Multiplexing**      | ❌ No soportado (1 respuesta a la vez) | ✅ Permite múltiples respuestas en paralelo |
| **Compresión de Headers** | ❌ No optimizado (envía headers repetidos) | ✅ Usa HPACK para reducir tamaño |
| **Conexiones**        | Abre varias conexiones TCP para paralelismo | Usa **una sola conexión TCP** eficiente |
| **Server Push**       | ❌ No soportado | ✅ Puede enviar recursos antes de ser solicitados |
| **Seguridad**        | Puede usar TLS (pero no obligatorio) | Generalmente requiere TLS |
| **Latencia**         | Mayor (espera cada respuesta antes de la siguiente) | Menor (varias respuestas simultáneas) |
| **Eficiencia de Red** | Bajo rendimiento en recursos pequeños | Más eficiente para múltiples recursos |

---

## **📌 Explicación de Cada Diferencia**
### 1️⃣ **Formato de Datos: Texto vs Binario**
- **HTTP/1.1** usa **texto plano**, lo que facilita la depuración pero **aumenta el tamaño** de los paquetes de datos.
- **HTTP/2** usa **binario**, reduciendo la sobrecarga y mejorando la eficiencia.

💡 **Ejemplo**:
```plaintext
HTTP/1.1 GET /index.html
Host: example.com
User-Agent: curl/7.64.1
Accept: */*
```
vs
```
HTTP/2: Datos en binario (menos bytes transmitidos)
```

---

### 2️⃣ **Multiplexing (Paralelismo)**
- **HTTP/1.1** envía **una solicitud a la vez por conexión**. Si una solicitud es lenta, **bloquea las siguientes** (*Head-of-Line Blocking*).
- **HTTP/2** permite **multiplexing**, enviando múltiples solicitudes/respuestas en una sola conexión TCP.

💡 **Ejemplo**:
Si tienes que cargar **5 imágenes en una página**, en **HTTP/1.1** harás 5 conexiones separadas, mientras que **HTTP/2** enviará las 5 imágenes en la misma conexión.

---

### 3️⃣ **Compresión de Headers (HPACK)**
- **HTTP/1.1** **repite headers en cada solicitud**, aumentando la carga de red.
- **HTTP/2** usa **HPACK**, que **comprime y reutiliza headers**, reduciendo el tamaño.

💡 **Ejemplo**:
```plaintext
HTTP/1.1: (Repite los headers en cada solicitud)
GET /image1.jpg
Host: example.com
User-Agent: Chrome

GET /image2.jpg
Host: example.com
User-Agent: Chrome
```
En **HTTP/2**, el header "Host: example.com" se envía una sola vez.

---

### 4️⃣ **Conexiones TCP y Rendimiento**
- **HTTP/1.1** usa **varias conexiones TCP** para cargar recursos en paralelo.
- **HTTP/2** usa **una sola conexión TCP** y la mantiene abierta, evitando la sobrecarga de establecer múltiples conexiones.

💡 **Ejemplo**:
Con **HTTP/1.1**, abrir muchas conexiones TCP puede **ralentizar la descarga**.
Con **HTTP/2**, se usa una sola conexión para todos los recursos.

---

### 5️⃣ **Server Push (Carga Proactiva)**
- **HTTP/1.1** solo envía archivos si el cliente los solicita.
- **HTTP/2** **anticipa** qué archivos necesita el cliente y los envía sin esperar la solicitud.

💡 **Ejemplo**:
Un servidor HTTP/2 puede enviar **index.html + styles.css + script.js** automáticamente sin esperar que el navegador los pida.

---

### 6️⃣ **Seguridad (TLS)**
- **HTTP/1.1** puede usarse sin **TLS (HTTPS)**.
- **HTTP/2** casi **siempre requiere TLS**, lo que mejora la seguridad.

💡 **Ejemplo**:
HTTP/1.1 → `http://example.com` (posible sin HTTPS)
HTTP/2 → `https://example.com` (TLS obligatorio en la mayoría de los casos)

---

## **📌 Pros y Contras**
### **✅ HTTP/1.1 - Ventajas**
✔ Simplicidad y compatibilidad con servidores antiguos.
✔ Fácil de depurar por su formato en texto.
✔ No requiere TLS obligatorio.

### **❌ HTTP/1.1 - Desventajas**
✖ **Mayor latencia** (espera cada respuesta antes de la siguiente).
✖ **Mayor consumo de ancho de banda** (headers repetidos).
✖ **Uso ineficiente de conexiones TCP** (abre muchas conexiones).

---

### **✅ HTTP/2 - Ventajas**
✔ **Menor latencia** gracias a **multiplexing**.
✔ **Mayor eficiencia de red** con **HPACK** (compresión de headers).
✔ **Carga proactiva** de recursos con **Server Push**.
✔ **Menos conexiones TCP**, mejor rendimiento.

### **❌ HTTP/2 - Desventajas**
✖ **Más complejo de depurar** (binario en lugar de texto).
✖ **TLS (HTTPS) casi obligatorio**.
✖ **No es compatible con servidores antiguos**.

---

## **📌 ¿Cuándo usar HTTP/1.1 o HTTP/2?**
| Caso de Uso          | HTTP/1.1 🏛 | HTTP/2 🚀 |
|----------------------|------------|----------|
| API con JSON liviano | ✅ Bueno | ✅ Mejor si hay muchas peticiones |
| Carga de páginas web con muchas imágenes y scripts | ❌ Lento | ✅ Excelente |
| Sitios con conexiones inseguras | ✅ Posible | ❌ No recomendado |
| Streaming en tiempo real | ❌ Bloqueo de carga | ✅ Multiplexing ayuda |

---

## **📌 Conclusión Final**
✔ **Si puedes usar HTTP/2, úsalo**: es más rápido, eficiente y reduce la latencia.
✔ **HTTP/1.1 sigue siendo útil en entornos antiguos o sin HTTPS**.
✔ **Ambos pueden convivir**: los navegadores usan **HTTP/2 si está disponible, y HTTP/1.1 como fallback**.
