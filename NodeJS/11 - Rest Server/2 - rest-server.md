Un **REST Server** es un servidor que sigue el paradigma **REST (Representational State Transfer)** para proporcionar servicios a través de HTTP. Se basa en principios como:

1. **Uso de recursos**: Cada entidad (usuarios, productos, etc.) se representa como una URL única.
2. **Métodos HTTP estándar**:
   - `GET`: Obtener datos.
   - `POST`: Crear recursos.
   - `PUT`: Actualizar recursos.
   - `DELETE`: Eliminar recursos.
3. **Uso de respuestas en formato estándar** como JSON.
4. **Stateless**: Cada petición es independiente, y el servidor no guarda el estado de la sesión.

---

## 🏗 **Cómo funciona un REST Server en Node.js con Express y TypeScript**

Para mantener una **arquitectura limpia**, organizamos el código siguiendo el **patrón de capas**:

1. **Capa de Rutas (`routes`)**: Define las rutas y redirige las peticiones.
2. **Capa de Controladores (`controllers`)**: Gestiona la lógica de negocio de cada petición.
3. **Capa de Casos de Uso / Servicios (`services`)**: Contiene la lógica de la aplicación sin preocuparse por HTTP.
4. **Capa de Modelos (`models`)**: Define las estructuras de datos y su validación.

---

### 📌 **Ejemplo de un REST Server en Node.js con TypeScript y Express**

### 1️⃣ **Instalar dependencias**
```sh
npm init -y
npm install express cors dotenv
npm install --save-dev typescript @types/express @types/node ts-node nodemon
```

### 2️⃣ **Configurar TypeScript**
Crea un archivo `tsconfig.json`:
```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "CommonJS",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true
  }
}
```

### 3️⃣ **Estructura del Proyecto**
```
/src
 ├── /controllers
 │    ├── user.controller.ts
 ├── /routes
 │    ├── user.routes.ts
 ├── /services
 │    ├── user.service.ts
 ├── /models
 │    ├── user.model.ts
 ├── /config
 │    ├── server.ts
 ├── index.ts
```

---

### 🏗 **1. Configurar el Servidor Express (`config/server.ts`)**
```ts
import express from "express";
import cors from "cors";
import userRoutes from "../routes/user.routes";

export class Server {
  private app = express();
  private port = process.env.PORT || "3000";

  constructor() {
    this.middlewares();
    this.routes();
  }

  private middlewares() {
    this.app.use(cors());
    this.app.use(express.json()); // Para manejar JSON
  }

  private routes() {
    this.app.use("/api/users", userRoutes);
  }

  public start() {
    this.app.listen(this.port, () => {
      console.log(`Server running on port ${this.port}`);
    });
  }
}
```

---

### 📌 **2. Crear un Modelo de Usuario (`models/user.model.ts`)**
```ts
export interface User {
  id: number;
  name: string;
  email: string;
}
```

---

### 📌 **3. Crear el Servicio de Usuarios (`services/user.service.ts`)**
```ts
import { User } from "../models/user.model";

class UserService {
  private users: User[] = [
    { id: 1, name: "John Doe", email: "john@example.com" }
  ];

  getAllUsers(): User[] {
    return this.users;
  }

  getUserById(id: number): User | undefined {
    return this.users.find(user => user.id === id);
  }

  createUser(user: User): User {
    user.id = this.users.length + 1;
    this.users.push(user);
    return user;
  }
}

export default new UserService();
```

---

### 📌 **4. Crear un Controlador (`controllers/user.controller.ts`)**
```ts
import { Request, Response } from "express";
import userService from "../services/user.service";

class UserController {
  getUsers(req: Request, res: Response) {
    const users = userService.getAllUsers();
    res.json(users);
  }

  getUser(req: Request, res: Response) {
    const id = Number(req.params.id);
    const user = userService.getUserById(id);
    user ? res.json(user) : res.status(404).json({ msg: "User not found" });
  }

  createUser(req: Request, res: Response) {
    const newUser = userService.createUser(req.body);
    res.status(201).json(newUser);
  }
}

export default new UserController();
```

---

### 📌 **5. Crear las Rutas (`routes/user.routes.ts`)**
```ts
import { Router } from "express";
import userController from "../controllers/user.controller";

const router = Router();

router.get("/", userController.getUsers);
router.get("/:id", userController.getUser);
router.post("/", userController.createUser);

export default router;
```

---

### 📌 **6. Iniciar el Servidor (`index.ts`)**
```ts
import { config } from "dotenv";
import { Server } from "./config/server";

config(); // Cargar variables de entorno

const server = new Server();
server.start();
```

---

## 🚀 **Ejemplo de Peticiones al Servidor**

1. **Obtener todos los usuarios**
   ```http
   GET http://localhost:3000/api/users
   ```
   **Respuesta:**
   ```json
   [
     { "id": 1, "name": "John Doe", "email": "john@example.com" }
   ]
   ```

2. **Obtener un usuario por ID**
   ```http
   GET http://localhost:3000/api/users/1
   ```
   **Respuesta:**
   ```json
   { "id": 1, "name": "John Doe", "email": "john@example.com" }
   ```

3. **Crear un usuario**
   ```http
   POST http://localhost:3000/api/users
   Content-Type: application/json

   {
     "name": "Alice",
     "email": "alice@example.com"
   }
   ```
   **Respuesta:**
   ```json
   { "id": 2, "name": "Alice", "email": "alice@example.com" }
   ```

---

## 🔥 **Conclusión**
Este REST Server en **Node.js, TypeScript y Express** usa una **arquitectura limpia**, separando responsabilidades en **rutas, controladores, servicios y modelos**. Esto facilita la escalabilidad y el mantenimiento.

### ✅ Ventajas de esta arquitectura:
✔ Código modular y reutilizable.
✔ Separación de responsabilidades.
✔ Fácil de probar y escalar.
