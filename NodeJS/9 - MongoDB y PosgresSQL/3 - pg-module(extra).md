## pg

El módulo `pg` en Node.js es un cliente para PostgreSQL que permite interactuar con bases de datos desde aplicaciones escritas en JavaScript o TypeScript. Cuando lo combinamos con **TypeScript, variables de entorno y arquitectura limpia**, logramos una solución escalable y mantenible.

---

## **¿Cómo funciona el módulo `pg` en Node.js con TypeScript?**

El módulo `pg` nos permite:
- Conectarnos a una base de datos PostgreSQL.
- Ejecutar consultas SQL.
- Manejar conexiones de manera eficiente usando `Pool`.

---

## **Arquitectura Limpia en el Contexto de `pg`**

En la **arquitectura limpia**, separamos las responsabilidades en capas:
1. **Infraestructura** → Módulo `pg` y configuración de la base de datos.
2. **Repositorio** → Capa intermedia que abstrae la base de datos.
3. **Casos de uso** → Lógica de negocio que usa el repositorio.
4. **Controlador** → Maneja las solicitudes HTTP y utiliza los casos de uso.

---

## **Ejemplo Completo con TypeScript, `.env` y Arquitectura Limpia**

### **1️⃣ Instalación de dependencias**
Ejecuta:
```sh
npm install pg dotenv
npm install --save-dev @types/pg
```

### **2️⃣ Estructura del Proyecto**
```plaintext
📂 src
 ├── 📂 config
 │    ├── db.ts
 │    ├── env.ts
 ├── 📂 repositories
 │    ├── userRepository.ts
 ├── 📂 useCases
 │    ├── getUser.ts
 ├── 📂 controllers
 │    ├── userController.ts
 ├── 📂 server
 │    ├── index.ts
 ├── .env
 ├── tsconfig.json
```

---

### **3️⃣ Configuración de Variables de Entorno (`.env`)**
```plaintext
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=secret
DB_NAME=mydatabase
```

---

### **4️⃣ Configuración de la Base de Datos (`config/db.ts`)**
```typescript
import { Pool } from "pg";
import { env } from "./env";

export const pool = new Pool({
  host: env.DB_HOST,
  port: Number(env.DB_PORT),
  user: env.DB_USER,
  password: env.DB_PASSWORD,
  database: env.DB_NAME,
});
```

---

### **5️⃣ Cargar Variables de Entorno (`config/env.ts`)**
```typescript
import dotenv from "dotenv";
dotenv.config();

export const env = {
  DB_HOST: process.env.DB_HOST || "localhost",
  DB_PORT: process.env.DB_PORT || "5432",
  DB_USER: process.env.DB_USER || "postgres",
  DB_PASSWORD: process.env.DB_PASSWORD || "secret",
  DB_NAME: process.env.DB_NAME || "mydatabase",
};
```

---

### **6️⃣ Repositorio de Usuarios (`repositories/userRepository.ts`)**
```typescript
import { pool } from "../config/db";

export class UserRepository {
  async getUserById(id: number) {
    const result = await pool.query("SELECT * FROM users WHERE id = $1", [id]);
    return result.rows[0] || null;
  }
}
```

---

### **7️⃣ Caso de Uso (`useCases/getUser.ts`)**
```typescript
import { UserRepository } from "../repositories/userRepository";

export class GetUserUseCase {
  private userRepository: UserRepository;

  constructor() {
    this.userRepository = new UserRepository();
  }

  async execute(id: number) {
    return this.userRepository.getUserById(id);
  }
}
```

---

### **8️⃣ Controlador (`controllers/userController.ts`)**
```typescript
import { GetUserUseCase } from "../useCases/getUser";
import { Request, Response } from "express";

export class UserController {
  private getUserUseCase: GetUserUseCase;

  constructor() {
    this.getUserUseCase = new GetUserUseCase();
  }

  async getUser(req: Request, res: Response) {
    const userId = Number(req.params.id);
    if (isNaN(userId)) {
      return res.status(400).json({ error: "Invalid user ID" });
    }

    const user = await this.getUserUseCase.execute(userId);
    if (!user) {
      return res.status(404).json({ error: "User not found" });
    }

    return res.json(user);
  }
}
```

---

### **9️⃣ Servidor Express (`server/index.ts`)**
```typescript
import express from "express";
import { UserController } from "../controllers/userController";

const app = express();
const userController = new UserController();

app.get("/users/:id", (req, res) => userController.getUser(req, res));

app.listen(3000, () => console.log("Server running on port 3000"));
```

---

## **🔥 Resumen Final**
✔ **Conexión a PostgreSQL** con `pg` y `Pool` para optimizar el rendimiento.
✔ **Uso de variables de entorno** para no exponer credenciales en el código.
✔ **Aplicación de Arquitectura Limpia** para separar responsabilidades.
✔ **Uso de TypeScript** para mejor tipado y mantenibilidad.
✔ **Servidor Express** para manejar peticiones.
