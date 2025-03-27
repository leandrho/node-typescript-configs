# Módulo Http de Node.js

**¿Qué es el módulo HTTP de Node.js?**

El módulo HTTP de Node.js es un módulo central que proporciona funcionalidades para crear servidores HTTP y clientes HTTP. Esto significa que puedes usarlo para:

* **Crear servidores web:** Que pueden recibir solicitudes HTTP y enviar respuestas.
* **Realizar solicitudes HTTP:** Para interactuar con otros servidores web.

**¿Para qué sirve?**

El módulo HTTP es fundamental para construir aplicaciones web y servicios en línea con Node.js. Algunas de sus aplicaciones comunes incluyen:

* Crear APIs RESTful.
* Construir aplicaciones web de una sola página (SPAs).
* Desarrollar servidores para aplicaciones móviles.
* Implementar microservicios.

**¿Cómo funciona?**

El módulo HTTP se basa en el protocolo HTTP, que define cómo se comunican los clientes y servidores web. En términos generales, el proceso funciona así:

1.  **El cliente envía una solicitud HTTP:** Que incluye información como la URL, el método (GET, POST, etc.) y los encabezados.
2.  **El servidor recibe la solicitud:** Y la procesa.
3.  **El servidor envía una respuesta HTTP:** Que incluye un código de estado (200 OK, 404 Not Found, etc.), encabezados y el cuerpo de la respuesta (datos HTML, JSON, etc.).

**Uso básico del módulo HTTP:**

Para crear un servidor HTTP básico, puedes usar el método `http.createServer()`:

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hola mundo!');
});

server.listen(3000, () => {
  console.log('Servidor escuchando en el puerto 3000');
});
```

En este ejemplo:

* `http.createServer()` crea un nuevo servidor.
* La función de devolución de llamada recibe los objetos `req` (solicitud) y `res` (respuesta).
* `res.writeHead()` establece los encabezados de la respuesta.
* `res.end()` envía el cuerpo de la respuesta y finaliza la conexión.
* `server.listen()` inicia el servidor y lo pone a escuchar en el puerto 3000.

**Usando TypeScript con el módulo HTTP:**

Para usar TypeScript con el módulo HTTP, puedes instalar los tipos de Node.js:

```bash
npm install --save-dev @types/node
```

Luego, puedes escribir código TypeScript como este:

```typescript
import * as http from 'http';

const server = http.createServer((req: http.IncomingMessage, res: http.ServerResponse) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hola mundo desde Typescript!');
});

server.listen(3000, () => {
  console.log('Servidor escuchando en el puerto 3000');
});
```

Las principales diferencias son:

* Importamos el módulo HTTP usando `import * as http from 'http';`.
* Especificamos los tipos de los objetos `req` y `res` usando `http.IncomingMessage` y `http.ServerResponse`.

**Puntos importantes:**

* El módulo HTTP es de bajo nivel, por lo que para aplicaciones más complejas, es común usar frameworks como Express.js, que simplifican el desarrollo.
* Es importante comprender los conceptos básicos del protocolo HTTP para usar el módulo de manera efectiva.

