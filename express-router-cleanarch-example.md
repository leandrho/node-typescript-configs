Se debe organizar el código en capas como **controladores, servicios y repositorios**, separando responsabilidades.

---

## **📌 Arquitectura Limpia en Express con TypeScript**
📂 Estructura de carpetas recomendada:

```
/src
 ├── /controllers  -> Lógica de manejo de solicitudes HTTP
 │    ├── UserController.ts
 ├── /services      -> Lógica de negocio
 │    ├── UserService.ts
 ├── /repositories  -> Capa de acceso a datos (base de datos)
 │    ├── UserRepository.ts
 ├── /routes        -> Definición de rutas
 │    ├── user.routes.ts
 ├── /models        -> Definición de tipos o interfaces
 │    ├── User.ts
 ├── server.ts      -> Configuración del servidor
 ├── app.ts         -> Configuración de Express
 ├── tsconfig.json  -> Configuración de TypeScript
```

---

## **1️⃣ Modelo de Datos (`models/User.ts`)**
Aquí definimos la interfaz del usuario para tipado fuerte en TypeScript.

```ts
export interface User {
  id: number;
  name: string;
  email: string;
}
```

---

## **2️⃣ Repositorio (`repositories/UserRepository.ts`)**
El repositorio maneja los datos (puede estar conectado a una base de datos real).

```ts
import { User } from '../models/User';

export class UserRepository {
  private users: User[] = [];

  getAll(): User[] {
    return this.users;
  }

  getById(id: number): User | undefined {
    return this.users.find(user => user.id === id);
  }

  create(user: User): User {
    this.users.push(user);
    return user;
  }

  update(id: number, updatedUser: Partial<User>): User | null {
    const index = this.users.findIndex(user => user.id === id);
    if (index === -1) return null;
    this.users[index] = { ...this.users[index], ...updatedUser };
    return this.users[index];
  }

  delete(id: number): boolean {
    const index = this.users.findIndex(user => user.id === id);
    if (index === -1) return false;
    this.users.splice(index, 1);
    return true;
  }
}
```

---

## **3️⃣ Servicio (`services/UserService.ts`)**
El servicio gestiona la lógica de negocio y llama al repositorio.

```ts
import { UserRepository } from '../repositories/UserRepository';
import { User } from '../models/User';

export class UserService {
  private userRepository: UserRepository;

  constructor() {
    this.userRepository = new UserRepository();
  }

  getUsers(): User[] {
    return this.userRepository.getAll();
  }

  getUserById(id: number): User | undefined {
    return this.userRepository.getById(id);
  }

  createUser(name: string, email: string): User {
    const user: User = { id: Date.now(), name, email };
    return this.userRepository.create(user);
  }

  updateUser(id: number, updatedUser: Partial<User>): User | null {
    return this.userRepository.update(id, updatedUser);
  }

  deleteUser(id: number): boolean {
    return this.userRepository.delete(id);
  }
}
```

---

## **4️⃣ Controlador (`controllers/UserController.ts`)**
El controlador maneja las peticiones HTTP y usa el servicio.

```ts
import { Request, Response } from 'express';
import { UserService } from '../services/UserService';

export class UserController {
  private userService: UserService;

  constructor() {
    this.userService = new UserService();
  }

  getUsers = (req: Request, res: Response) => {
    const users = this.userService.getUsers();
    res.json(users);
  };

  getUserById = (req: Request, res: Response) => {
    const id = parseInt(req.params.id);
    const user = this.userService.getUserById(id);
    if (!user) return res.status(404).json({ message: 'Usuario no encontrado' });
    res.json(user);
  };

  createUser = (req: Request, res: Response) => {
    const { name, email } = req.body;
    const newUser = this.userService.createUser(name, email);
    res.status(201).json(newUser);
  };

  updateUser = (req: Request, res: Response) => {
    const id = parseInt(req.params.id);
    const updatedUser = this.userService.updateUser(id, req.body);
    if (!updatedUser) return res.status(404).json({ message: 'Usuario no encontrado' });
    res.json(updatedUser);
  };

  deleteUser = (req: Request, res: Response) => {
    const id = parseInt(req.params.id);
    const deleted = this.userService.deleteUser(id);
    if (!deleted) return res.status(404).json({ message: 'Usuario no encontrado' });
    res.json({ message: 'Usuario eliminado correctamente' });
  };
}
```

---

## **5️⃣ Definir las Rutas (`routes/user.routes.ts`)**
Aquí conectamos las rutas con el controlador.

```ts
import { Router } from 'express';
import { UserController } from '../controllers/UserController';

const router = Router();
const userController = new UserController();

router.get('/', userController.getUsers);
router.get('/:id', userController.getUserById);
router.post('/', userController.createUser);
router.put('/:id', userController.updateUser);
router.delete('/:id', userController.deleteUser);

export default router;
```

---

## **6️⃣ Configurar Express (`app.ts`)**
Aquí montamos las rutas y middlewares.

```ts
import express from 'express';
import userRoutes from './routes/user.routes';

const app = express();
app.use(express.json());
app.use('/users', userRoutes);

export default app;
```

---

## **7️⃣ Iniciar el Servidor (`server.ts`)**
El archivo principal para ejecutar el servidor.

```ts
import app from './app';

const PORT = 3000;
app.listen(PORT, () => {
  console.log(`Servidor en ejecución en http://localhost:${PORT}`);
});
```

---

## **🚀 Beneficios de esta Arquitectura**
✅ **Modularidad:** Código organizado en capas reutilizables.
✅ **Escalabilidad:** Fácil de ampliar y mantener.
✅ **Tipado fuerte:** TypeScript ayuda a evitar errores.
✅ **Separación de responsabilidades:** Cada capa tiene una función específica.

---
