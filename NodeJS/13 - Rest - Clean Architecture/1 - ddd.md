# Domain-Driven Design(DDD)

El **patrón de diseño guiado por el dominio (Domain-Driven Design, DDD)** en un entorno **Node.js con TypeScript**, combinado con **arquitectura limpia (Clean Architecture)**, es una forma estructurada de diseñar aplicaciones de software enfocándose en el **dominio del problema** y manteniendo el código modular, desacoplado y fácil de mantener.

---

## 🔹 Conceptos clave

1. **Domain-Driven Design (DDD)**
   Se basa en la idea de que la lógica de negocio debe ser el núcleo de la aplicación, separándola de detalles como la infraestructura y las interfaces de usuario.

2. **Clean Architecture**
   Separa la aplicación en **capas bien definidas**, donde cada capa tiene una responsabilidad específica, evitando dependencias innecesarias.

---

## 🔹 Arquitectura basada en capas con DDD y Clean Architecture

La arquitectura suele dividirse en cuatro capas principales:

1. **Capa de dominio (Domain Layer)**
   - Representa el **corazón de la aplicación**.
   - Define **entidades, agregados, objetos de valor e interfaces de repositorio**.
   - **NO tiene dependencias externas.**

2. **Capa de aplicación (Application Layer)**
   - Contiene los **casos de uso (use cases)** que orquestan la lógica de negocio.
   - Se encarga de interactuar con la capa de dominio sin preocuparse por detalles técnicos.
   - Usa **interfaces** para comunicarse con la infraestructura.

3. **Capa de infraestructura (Infrastructure Layer)**
   - Implementa los **repositorios**, **ORMs**, **APIs externas**, **logs**, etc.
   - Satisface las interfaces definidas en la capa de dominio.

4. **Capa de interfaz de usuario (Presentation Layer)**
   - Se encarga de manejar la interacción con el usuario (REST API, GraphQL, CLI, etc.).
   - Consume los casos de uso definidos en la capa de aplicación.

---

## 🔹 Implementación en Node.js con TypeScript

A continuación, se presenta una estructura de carpetas basada en Clean Architecture con DDD:

```
/src
 ├── /domain
 │   ├── /entities
 │   │   ├── User.ts
 │   ├── /value-objects
 │   │   ├── Email.ts
 │   ├── /repositories
 │   │   ├── IUserRepository.ts
 │   ├── /services
 │   │   ├── UserService.ts
 │
 ├── /application
 │   ├── /use-cases
 │   │   ├── CreateUserUseCase.ts
 │   │   ├── GetUserByIdUseCase.ts
 │
 ├── /infrastructure
 │   ├── /repositories
 │   │   ├── UserRepository.ts
 │   ├── /database
 │   │   ├── prisma.ts
 │
 ├── /presentation
 │   ├── /controllers
 │   │   ├── UserController.ts
 │   ├── /routes
 │   │   ├── userRoutes.ts
 │
 ├── /config
 │   ├── env.ts
 │
 ├── /shared
 │   ├── AppError.ts
 │   ├── Result.ts
 │
 ├── server.ts
```

---

## 🔹 Explicación con código

### **1️⃣ Capa de dominio (Domain Layer)**
Define las reglas de negocio sin preocuparse por cómo se implementarán.

#### 📌 **Entidad: `User.ts`**
```typescript
export class User {
  constructor(
    public readonly id: string,
    public name: string,
    public email: string
  ) {}

  changeEmail(newEmail: string) {
    this.email = newEmail;
  }
}
```

#### 📌 **Interfaz del repositorio: `IUserRepository.ts`**
```typescript
export interface IUserRepository {
  findById(id: string): Promise<User | null>;
  save(user: User): Promise<void>;
}
```

---

### **2️⃣ Capa de aplicación (Application Layer)**
Define los casos de uso que utilizan las entidades.

#### 📌 **Caso de uso: `CreateUserUseCase.ts`**
```typescript
import { User } from "../../domain/entities/User";
import { IUserRepository } from "../../domain/repositories/IUserRepository";

export class CreateUserUseCase {
  constructor(private userRepository: IUserRepository) {}

  async execute(name: string, email: string): Promise<User> {
    const user = new User(Date.now().toString(), name, email);
    await this.userRepository.save(user);
    return user;
  }
}
```

---

### **3️⃣ Capa de infraestructura (Infrastructure Layer)**
Implementa los repositorios con una base de datos.

#### 📌 **Repositorio con Prisma: `UserRepository.ts`**
```typescript
import { PrismaClient } from "@prisma/client";
import { IUserRepository } from "../../domain/repositories/IUserRepository";
import { User } from "../../domain/entities/User";

const prisma = new PrismaClient();

export class UserRepository implements IUserRepository {
  async findById(id: string): Promise<User | null> {
    const user = await prisma.user.findUnique({ where: { id } });
    return user ? new User(user.id, user.name, user.email) : null;
  }

  async save(user: User): Promise<void> {
    await prisma.user.create({
      data: {
        id: user.id,
        name: user.name,
        email: user.email,
      },
    });
  }
}
```

---

### **4️⃣ Capa de presentación (Presentation Layer)**
Define los controladores y rutas.

#### 📌 **Controlador: `UserController.ts`**
```typescript
import { Request, Response } from "express";
import { CreateUserUseCase } from "../../application/use-cases/CreateUserUseCase";
import { UserRepository } from "../../infrastructure/repositories/UserRepository";

export class UserController {
  async createUser(req: Request, res: Response) {
    const { name, email } = req.body;
    const userRepository = new UserRepository();
    const createUserUseCase = new CreateUserUseCase(userRepository);

    const user = await createUserUseCase.execute(name, email);
    return res.status(201).json(user);
  }
}
```

#### 📌 **Rutas: `userRoutes.ts`**
```typescript
import { Router } from "express";
import { UserController } from "../controllers/UserController";

const router = Router();
const userController = new UserController();

router.post("/users", userController.createUser);

export default router;
```

---

### **5️⃣ Servidor con Express**
```typescript
import express from "express";
import userRoutes from "./presentation/routes/userRoutes";

const app = express();
app.use(express.json());
app.use("/api", userRoutes);

app.listen(3000, () => console.log("Server running on port 3000"));
```

---

## 🔹 Beneficios de esta arquitectura
✅ **Código modular y reutilizable**
✅ **Separación de preocupaciones**
✅ **Fácil mantenimiento y escalabilidad**
✅ **Permite cambiar la infraestructura sin afectar la lógica de negocio**

---

## 🔹 Conclusión
El **Domain-Driven Design (DDD)** en **Node.js con TypeScript**, combinado con **Clean Architecture**, permite diseñar sistemas robustos y bien estructurados. Separa claramente la lógica de negocio, los casos de uso y la infraestructura, haciendo que el código sea mantenible y flexible ante cambios.
