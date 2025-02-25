# Express

**¿Qué es Express.js?**

Express.js es un framework web minimalista y flexible para Node.js. Proporciona un conjunto de características robustas para el desarrollo de aplicaciones web y móviles. En esencia, simplifica la creación de servidores web y APIs en Node.js.

**¿Para qué sirve Express.js?**

Express.js se utiliza para una variedad de propósitos, incluyendo:

* **Desarrollo de APIs web:** Express.js facilita la creación de APIs RESTful que pueden ser consumidas por aplicaciones frontend o móviles.
* **Creación de aplicaciones web:** Puedes construir aplicaciones web completas con Express.js, manejando rutas, plantillas y contenido estático.
* **Desarrollo de aplicaciones de una sola página (SPA):** Express.js es ideal para servir el backend de SPAs construidas con frameworks como React, Angular o Vue.js.
* **Creación de microservicios:** Su naturaleza ligera lo hace adecuado para la construcción de microservicios.

**¿Cómo funciona Express.js?**

Express.js funciona como una capa sobre el módulo HTTP de Node.js, proporcionando abstracciones y herramientas que simplifican la creación de servidores web. Sus componentes clave incluyen:

* **Rutas:** Definen cómo la aplicación responde a las solicitudes de los clientes.
* **Middleware:** Funciones que se ejecutan durante el ciclo de vida de una solicitud, permitiendo realizar tareas como el análisis de solicitudes, la autenticación y el manejo de errores.
* **Plantillas:** Permiten generar contenido HTML dinámico.

**Usando TypeScript con Express.js**

TypeScript añade tipado estático a JavaScript, lo que mejora la mantenibilidad y la detección de errores en el desarrollo de aplicaciones Express.js. Para usar TypeScript con Express.js, necesitas:

1.  **Instalar dependencias:**
    * `npm install express @types/express typescript ts-node --save-dev`

2.  **Configurar TypeScript:**
    * Crear un archivo `tsconfig.json` para definir las opciones del compilador de TypeScript.

3.  **Escribir código TypeScript:**
    * Puedes escribir tu código Express.js en archivos `.ts`, aprovechando las ventajas del tipado estático.

4.  **Compilar y ejecutar:**
    * Puedes compilar tu código TypeScript a JavaScript con el comando `tsc`, y luego ejecutarlo con Node.js. O puedes utilizar `ts-node` para ejecutar directamente los archivos .ts.

**Ejemplo básico:**

```typescript
import express, { Request, Response } from 'express';

const app = express();
const port = 3000;

app.get('/', (req: Request, res: Response) => {
  res.send('Hola mundo desde Express.js con TypeScript!');
});

app.listen(port, () => {
  console.log(`Servidor escuchando en el puerto ${port}`);
});
```

En este ejemplo:

* Importamos express y los tipos Request y Response.
* Creamos una instancia de la aplicación Express.
* Definimos una ruta GET en la raíz ("/").
* Iniciamos el servidor en el puerto 3000.

**Ventajas de usar TypeScript con Express.js:**

* **Mejor mantenibilidad:** El tipado estático ayuda a prevenir errores y facilita la refactorización.
* **Detección temprana de errores:** Los errores se detectan durante la compilación, no en tiempo de ejecución.
* **Mejor documentación:** Los tipos sirven como documentación implícita del código.
* **Mayor productividad:** El autocompletado y la verificación de tipos mejoran la experiencia de desarrollo.

En **Express**, las *routes* (rutas) son la forma en que definimos las direcciones a las que un servidor puede responder. Se organizan con `express.Router` y pueden manejar diferentes métodos HTTP (`GET`, `POST`, `PUT`, `DELETE`, etc.).

---

## 🔹 **Ejemplo de rutas en Express con TypeScript**

### **1. Crear el servidor en `src/index.ts`**
```ts
import express from 'express';
import userRoutes from './routes/user.routes';

const app = express();
app.use(express.json()); // Middleware para JSON
app.use('/users', userRoutes); // Montar las rutas de usuarios

const PORT = 3000;
app.listen(PORT, () => console.log(`Servidor en http://localhost:${PORT}`));
```

---

### **2. Definir las rutas en `src/routes/user.routes.ts`**
```ts
import { Router } from 'express';

const router = Router();

router.get('/', (req, res) => {
  res.json({ message: 'Lista de usuarios' });
});

router.get('/:id', (req, res) => {
  const { id } = req.params;
  res.json({ message: `Usuario con ID: ${id}` });
});

router.post('/', (req, res) => {
  const { name } = req.body;
  res.status(201).json({ message: `Usuario ${name} creado` });
});

router.put('/:id', (req, res) => {
  const { id } = req.params;
  res.json({ message: `Usuario con ID: ${id} actualizado` });
});

router.delete('/:id', (req, res) => {
  const { id } = req.params;
  res.json({ message: `Usuario con ID: ${id} eliminado` });
});

export default router;
```

---

## 🔹 **Conclusión**
- `express.Router()` permite modularizar las rutas.
- Se pueden definir rutas con `GET`, `POST`, `PUT`, `DELETE`.
- Se usa `req.params` para obtener parámetros de la URL.
- Se usa `req.body` para leer datos en peticiones `POST` o `PUT`.
