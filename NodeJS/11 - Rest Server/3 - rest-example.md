## Ejemplo

**REST Server** con **Node.js, TypeScript y Express** siguiendo la arquitectura limpia (**Clean Architecture**).

---

## 📌 **Arquitectura del Proyecto**
Para mantener un código modular y escalable, estructuraremos el proyecto de esta forma:

```
/rest-server
│── /src
│   ├── /config         # Configuración de la app
│   ├── /controllers    # Controladores de la API
│   ├── /routes         # Definición de rutas de la API
│   ├── /services       # Lógica de negocio
│   ├── /repositories   # Acceso a datos
│   ├── /models         # Definición de entidades
│   ├── /middlewares    # Middlewares de Express
│   ├── /utils          # Utilidades generales
│   ├── app.ts          # Configuración principal de Express
│   ├── server.ts       # Punto de entrada del servidor
│── package.json
│── tsconfig.json
│── .env
│── .gitignore
```

---

## 🔹 **Paso 1: Inicializar el Proyecto**
Ejecuta los siguientes comandos para crear el proyecto y configurar TypeScript:

```bash
mkdir rest-server && cd rest-server
npm init -y
npm install express dotenv cors helmet morgan
npm install --save-dev typescript ts-node @types/node @types/express
npx tsc --init
```

---

## 🔹 **Paso 2: Configurar TypeScript (`tsconfig.json`)**
Editamos `tsconfig.json` para habilitar los módulos de ES6:

```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "CommonJS",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true
  }
}
```

---

## 🔹 **Paso 3: Configurar Express (`src/app.ts`)**
Creamos el archivo `src/app.ts` para configurar Express:

```ts
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import morgan from 'morgan';
import dotenv from 'dotenv';
import userRoutes from './routes/user.routes';

dotenv.config();

const app = express();

// Middlewares
app.use(cors());
app.use(helmet());
app.use(morgan('dev'));
app.use(express.json());

// Rutas
app.use('/api/users', userRoutes);

export default app;
```

---

## 🔹 **Paso 4: Configurar el Servidor (`src/server.ts`)**
Este archivo inicia el servidor en el puerto configurado:

```ts
import app from './app';

const PORT = process.env.PORT || 5000;

app.listen(PORT, () => {
  console.log(`🚀 Server running on http://localhost:${PORT}`);
});
```

---

## 🔹 **Paso 5: Crear las Rutas (`src/routes/user.routes.ts`)**
Creamos las rutas para la gestión de usuarios:

```ts
import { Router } from 'express';
import UserController from '../controllers/user.controller';

const router = Router();

router.get('/', UserController.getAllUsers);
router.get('/:id', UserController.getUserById);
router.post('/', UserController.createUser);
router.put('/:id', UserController.updateUser);
router.delete('/:id', UserController.deleteUser);

export default router;
```

---

## 🔹 **Paso 6: Crear el Controlador (`src/controllers/user.controller.ts`)**
Los controladores manejan la lógica de cada endpoint:

```ts
import { Request, Response } from 'express';
import UserService from '../services/user.service';

class UserController {
  static async getAllUsers(req: Request, res: Response) {
    const users = await UserService.getAllUsers();
    res.json(users);
  }

  static async getUserById(req: Request, res: Response) {
    const user = await UserService.getUserById(Number(req.params.id));
    user ? res.json(user) : res.status(404).json({ message: 'User not found' });
  }

  static async createUser(req: Request, res: Response) {
    const newUser = await UserService.createUser(req.body);
    res.status(201).json(newUser);
  }

  static async updateUser(req: Request, res: Response) {
    const updatedUser = await UserService.updateUser(Number(req.params.id), req.body);
    updatedUser ? res.json(updatedUser) : res.status(404).json({ message: 'User not found' });
  }

  static async deleteUser(req: Request, res: Response) {
    const deleted = await UserService.deleteUser(Number(req.params.id));
    deleted ? res.json({ message: 'User deleted' }) : res.status(404).json({ message: 'User not found' });
  }
}

export default UserController;
```

---

## 🔹 **Paso 7: Crear el Servicio (`src/services/user.service.ts`)**
Aquí definimos la lógica de negocio:

```ts
import UserRepository from '../repositories/user.repository';

class UserService {
  static async getAllUsers() {
    return UserRepository.getAll();
  }

  static async getUserById(id: number) {
    return UserRepository.getById(id);
  }

  static async createUser(userData: any) {
    return UserRepository.create(userData);
  }

  static async updateUser(id: number, userData: any) {
    return UserRepository.update(id, userData);
  }

  static async deleteUser(id: number) {
    return UserRepository.delete(id);
  }
}

export default UserService;
```

---

## 🔹 **Paso 8: Crear el Repositorio (`src/repositories/user.repository.ts`)**
Este módulo se encargará de la persistencia de datos.

(Simulación en memoria; en una aplicación real usaríamos una base de datos como PostgreSQL o MongoDB).

```ts
interface User {
  id: number;
  name: string;
  email: string;
}

class UserRepository {
  private static users: User[] = [
    { id: 1, name: 'John Doe', email: 'john@example.com' }
  ];

  static getAll() {
    return this.users;
  }

  static getById(id: number) {
    return this.users.find(user => user.id === id);
  }

  static create(userData: any) {
    const newUser: User = { id: this.users.length + 1, ...userData };
    this.users.push(newUser);
    return newUser;
  }

  static update(id: number, userData: any) {
    const userIndex = this.users.findIndex(user => user.id === id);
    if (userIndex === -1) return null;
    this.users[userIndex] = { ...this.users[userIndex], ...userData };
    return this.users[userIndex];
  }

  static delete(id: number) {
    const userIndex = this.users.findIndex(user => user.id === id);
    if (userIndex === -1) return false;
    this.users.splice(userIndex, 1);
    return true;
  }
}

export default UserRepository;
```

---

## 🔹 **Paso 9: Agregar Variables de Entorno (`.env`)**
Creamos un archivo `.env` para configurar el puerto:

```
PORT=5000
```

---

## 🔹 **Paso 10: Agregar un Script de Inicio en `package.json`**
Modificamos el archivo `package.json` para agregar un script de ejecución:

```json
"scripts": {
  "start": "ts-node src/server.ts"
}
```

---

## 🔹 **Paso 11: Iniciar el Servidor**
Ejecutamos el servidor con:

```bash
npm start
```

✅ Ahora la API RESTful está corriendo en `http://localhost:5000/api/users`.

---

## 🎯 **Resumen**
- **Express** para manejar las rutas y peticiones HTTP.
- **TypeScript** para mejor tipado y seguridad.
- **Arquitectura limpia** con capas bien definidas (`Controllers`, `Services`, `Repositories`).
- **Middleware** como `helmet`, `cors`, y `morgan` para seguridad y logs.
