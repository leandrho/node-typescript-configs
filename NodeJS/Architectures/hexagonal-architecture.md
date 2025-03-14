## **Arquitectura Hexagonal (Ports & Adapters) en Node.js**
La **Arquitectura Hexagonal** (también conocida como **Ports & Adapters**) busca desacoplar la lógica de negocio de la infraestructura, permitiendo que el código sea independiente de frameworks, bases de datos o interfaces externas.

### **🎯 Objetivo principal**
Separar la lógica de negocio del resto del sistema mediante **puertos (ports) e interfaces (adapters)**, para que los cambios en la infraestructura no afecten el dominio.

---

# **📌 Conceptos Clave**
### **1️⃣ Dominio (Core o Business Logic)**
Es el **corazón** de la aplicación y no debe depender de ninguna tecnología externa.

💡 **Incluye:**
- **Entidades**: Representan los objetos de negocio (Ej: `User`).
- **Casos de Uso** (o Servicios de Aplicación): Contienen la lógica de negocio (Ej: `CreateUser`).
- **Interfaces de Puertos** (Ports): Definen contratos para la comunicación con el exterior.

---

### **2️⃣ Puertos (Ports)**
Son **interfaces** que definen cómo la lógica de negocio interactúa con el mundo exterior.

📌 **Tipos de Puertos:**
1. **Puertos de Entrada (Inbound Ports)**
   - Son las **interacciones** que el mundo exterior puede hacer con nuestra aplicación.
   - Ejemplo: Métodos en casos de uso como `createUser.execute()`.

2. **Puertos de Salida (Outbound Ports)**
   - Son las **interacciones** que nuestra aplicación hace hacia afuera (base de datos, APIs externas, etc.).
   - Ejemplo: `UserRepository` (interfaz de acceso a datos).

---

### **3️⃣ Adaptadores (Adapters)**
Son las **implementaciones concretas** de los puertos y se encargan de la conexión con el mundo exterior.

📌 **Tipos de Adaptadores:**
- **Adaptadores de Entrada (Inbound Adapters)**: Implementan los puertos de entrada y exponen la aplicación (Ej: un controlador HTTP).
- **Adaptadores de Salida (Outbound Adapters)**: Implementan los puertos de salida y se conectan con bases de datos, APIs externas, etc.

---

## **📂 Estructura del Proyecto**
📌 Un esquema típico en **Node.js con TypeScript** usando Arquitectura Hexagonal:
```
/src
 ├── /application         # (Capa de aplicación - Casos de uso)
 │   ├── /use-cases
 │   │   ├── CreateUser.ts
 │   │   ├── GetUser.ts
 │   ├── /ports           # (Interfaces de comunicación)
 │   │   ├── UserRepository.ts
 │
 ├── /domain              # (Capa de dominio - Reglas de negocio)
 │   ├── /entities
 │   │   ├── User.ts
 │
 ├── /infrastructure      # (Capa de infraestructura - Adaptadores)
 │   ├── /repositories
 │   │   ├── MongoUserRepository.ts
 │   ├── /controllers
 │   │   ├── UserController.ts
 │
 ├── /config              # (Configuraciones como env, conexión a BD, etc.)
 ├── /server.ts           # (Punto de entrada de la aplicación)
```

---

## **🔍 Implementación Paso a Paso**
### **1️⃣ Dominio - Entidad `User`**
📂 `src/domain/entities/User.ts`
```ts
export class User {
  constructor(public id: string, public name: string, public email: string) {}

  validateEmail(): boolean {
    return /\S+@\S+\.\S+/.test(this.email);
  }
}
```

---

### **2️⃣ Puerto de Salida - `UserRepository`**
📂 `src/application/ports/UserRepository.ts`
```ts
import { User } from "../../domain/entities/User";

export interface UserRepository {
  save(user: User): Promise<void>;
  findByEmail(email: string): Promise<User | null>;
}
```
📌 **Este es un contrato, no una implementación.**

---

### **3️⃣ Caso de Uso - `CreateUser`**
📂 `src/application/use-cases/CreateUser.ts`
```ts
import { User } from "../../domain/entities/User";
import { UserRepository } from "../ports/UserRepository";

export class CreateUser {
  constructor(private userRepository: UserRepository) {}

  async execute(name: string, email: string) {
    const user = new User(Date.now().toString(), name, email);
    if (!user.validateEmail()) throw new Error("Email inválido");

    await this.userRepository.save(user);
    return user;
  }
}
```
📌 **Este caso de uso solo conoce la interfaz (`UserRepository`), no una base de datos específica.**

---

### **4️⃣ Adaptador de Salida - `MongoUserRepository`**
📂 `src/infrastructure/repositories/MongoUserRepository.ts`
```ts
import { UserRepository } from "../../application/ports/UserRepository";
import { User } from "../../domain/entities/User";
import { MongoClient } from "mongodb";

export class MongoUserRepository implements UserRepository {
  private client = new MongoClient("mongodb://localhost:27017");
  private collection = this.client.db("usersDB").collection("users");

  async save(user: User): Promise<void> {
    await this.collection.insertOne(user);
  }

  async findByEmail(email: string): Promise<User | null> {
    const data = await this.collection.findOne({ email });
    return data ? new User(data.id, data.name, data.email) : null;
  }
}
```
📌 **Aquí se implementa `UserRepository` para conectarse a MongoDB.**

---

### **5️⃣ Adaptador de Entrada - `UserController`**
📂 `src/infrastructure/controllers/UserController.ts`
```ts
import { Request, Response } from "express";
import { CreateUser } from "../../application/use-cases/CreateUser";
import { MongoUserRepository } from "../repositories/MongoUserRepository";

export class UserController {
  static async createUser(req: Request, res: Response) {
    const { name, email } = req.body;

    try {
      const userRepository = new MongoUserRepository();
      const createUser = new CreateUser(userRepository);
      const user = await createUser.execute(name, email);

      res.status(201).json(user);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  }
}
```
📌 **Aquí se inyecta la implementación real (`MongoUserRepository`) en el caso de uso.**

---

### **6️⃣ Configurar Express y Definir Rutas**
📂 `src/server.ts`
```ts
import express from "express";
import { UserController } from "./infrastructure/controllers/UserController";

const app = express();
app.use(express.json());

app.post("/users", UserController.createUser);

app.listen(3000, () => console.log("Servidor corriendo en puerto 3000"));
```
📌 **Este es el punto de entrada de la app, donde se configura Express y las rutas.**

---

## **🎯 Beneficios de la Arquitectura Hexagonal**
✅ **Desacoplamiento total**: La lógica de negocio no depende de frameworks, bases de datos o interfaces externas.
✅ **Alta mantenibilidad**: Se puede cambiar la base de datos o el framework web sin afectar la lógica de negocio.
✅ **Facilidad de pruebas**: Se pueden escribir **tests unitarios** simulando (`mocks`) los puertos.
✅ **Escalabilidad**: Es fácil agregar más adaptadores (ejemplo: una API GraphQL, eventos de mensajería, CLI, etc.).

---

## **📌 Conclusión**
La **Arquitectura Hexagonal** es ideal para aplicaciones que necesitan **independencia de la infraestructura** y **flexibilidad**. En Node.js, se puede implementar con **Express, MongoDB, PostgreSQL, RabbitMQ, etc.** sin afectar la lógica de negocio.

Con este enfoque, puedes escalar y mantener tu aplicación de manera mucho más efectiva. 🚀

---

## **Arquitectura Hexagonal + Vertical Slicing en Node.js**

La **arquitectura hexagonal** ayuda a desacoplar la lógica de negocio de la infraestructura. Pero en proyectos grandes, organizar el código por **capas tradicionales (domain, application, infrastructure, etc.)** puede volverse complejo.

Aquí es donde entra **Vertical Slicing**, un enfoque que organiza el código por funcionalidades en lugar de capas.

---

# **📌 ¿Qué es Vertical Slicing?**
En lugar de estructurar el proyecto por capas horizontales (`domain/`, `application/`, `infrastructure/`), **Vertical Slicing** divide el código en **módulos funcionales** (features) que contienen **toda su lógica encapsulada**.

Cada "slice" (corte vertical) es **independiente** y agrupa todo lo necesario para una funcionalidad en un solo lugar.

## **🎯 Beneficios**
✅ **Mejor organización**: Cada funcionalidad está en su propia carpeta.
✅ **Desacoplamiento real**: Se pueden cambiar o eliminar funcionalidades sin romper el resto del sistema.
✅ **Escalabilidad**: Fácil de agregar nuevas features sin tocar las existentes.
✅ **Más intuitivo**: Es más fácil encontrar dónde está cada funcionalidad.

---

# **📂 Estructura con Vertical Slicing + Hexagonal**
📌 En lugar de organizar el código por capas tradicionales (`domain/`, `application/`, etc.), organizamos por **módulos funcionales**:

```
/src
 ├── /users                  # 🟢 Módulo "Users" (Ej: registro de usuarios)
 │   ├── /domain             # Entidad y lógica de negocio
 │   │   ├── User.ts
 │   │   ├── UserRepository.ts
 │   │   ├── UserErrors.ts
 │   ├── /application        # Casos de uso (Use Cases)
 │   │   ├── CreateUser.ts
 │   │   ├── GetUser.ts
 │   ├── /infrastructure     # Implementaciones concretas
 │   │   ├── UserController.ts
 │   │   ├── MongoUserRepository.ts
 │   ├── /tests              # Pruebas unitarias y de integración
 │   │   ├── CreateUser.test.ts
 │
 ├── /products               # 🟠 Módulo "Products"
 │   ├── /domain
 │   ├── /application
 │   ├── /infrastructure
 │   ├── /tests
 │
 ├── /orders                 # 🔵 Módulo "Orders"
 │   ├── /domain
 │   ├── /application
 │   ├── /infrastructure
 │   ├── /tests
 │
 ├── /shared                 # Código reutilizable entre módulos
 │   ├── Database.ts
 │   ├── Logger.ts
 │   ├── EventBus.ts
 │
 ├── /config                 # Configuración global (.env, conexiones, etc.)
 ├── /server.ts              # Punto de entrada de la app
```

---

# **🔍 Implementación Paso a Paso**
Vamos a implementar un **módulo de usuarios (`users`) con Vertical Slicing y Arquitectura Hexagonal**.

---

## **1️⃣ Módulo `users`**
📌 **Entidad `User`**
📂 `src/users/domain/User.ts`
```ts
export class User {
  constructor(public id: string, public name: string, public email: string) {}

  validateEmail(): boolean {
    return /\S+@\S+\.\S+/.test(this.email);
  }
}
```

📌 **Interfaz `UserRepository`**
📂 `src/users/domain/UserRepository.ts`
```ts
import { User } from "./User";

export interface UserRepository {
  save(user: User): Promise<void>;
  findByEmail(email: string): Promise<User | null>;
}
```

📌 **Errores de negocio (`UserErrors`)**
📂 `src/users/domain/UserErrors.ts`
```ts
export class UserAlreadyExistsError extends Error {
  constructor(email: string) {
    super(`User with email ${email} already exists.`);
    this.name = "UserAlreadyExistsError";
  }
}
```

---

## **2️⃣ Caso de Uso - `CreateUser`**
📂 `src/users/application/CreateUser.ts`
```ts
import { User } from "../domain/User";
import { UserRepository } from "../domain/UserRepository";
import { UserAlreadyExistsError } from "../domain/UserErrors";

export class CreateUser {
  constructor(private userRepository: UserRepository) {}

  async execute(name: string, email: string) {
    const existingUser = await this.userRepository.findByEmail(email);
    if (existingUser) throw new UserAlreadyExistsError(email);

    const user = new User(Date.now().toString(), name, email);
    if (!user.validateEmail()) throw new Error("Invalid email format");

    await this.userRepository.save(user);
    return user;
  }
}
```
📌 **Este caso de uso solo conoce `UserRepository`, no sabe nada sobre MongoDB o Express.**

---

## **3️⃣ Adaptador de Salida - `MongoUserRepository`**
📂 `src/users/infrastructure/MongoUserRepository.ts`
```ts
import { UserRepository } from "../domain/UserRepository";
import { User } from "../domain/User";
import { MongoClient } from "mongodb";

export class MongoUserRepository implements UserRepository {
  private client = new MongoClient("mongodb://localhost:27017");
  private collection = this.client.db("usersDB").collection("users");

  async save(user: User): Promise<void> {
    await this.collection.insertOne(user);
  }

  async findByEmail(email: string): Promise<User | null> {
    const data = await this.collection.findOne({ email });
    return data ? new User(data.id, data.name, data.email) : null;
  }
}
```
📌 **Implementa `UserRepository`, pero no afecta a la lógica de negocio.**

---

## **4️⃣ Adaptador de Entrada - `UserController`**
📂 `src/users/infrastructure/UserController.ts`
```ts
import { Request, Response } from "express";
import { CreateUser } from "../application/CreateUser";
import { MongoUserRepository } from "./MongoUserRepository";

export class UserController {
  static async createUser(req: Request, res: Response) {
    const { name, email } = req.body;

    try {
      const userRepository = new MongoUserRepository();
      const createUser = new CreateUser(userRepository);
      const user = await createUser.execute(name, email);

      res.status(201).json(user);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  }
}
```
📌 **El controlador solo se encarga de recibir la petición y ejecutar el caso de uso.**

---

## **5️⃣ Configuración de Rutas y Servidor**
📂 `src/server.ts`
```ts
import express from "express";
import { UserController } from "./users/infrastructure/UserController";

const app = express();
app.use(express.json());

app.post("/users", UserController.createUser);

app.listen(3000, () => console.log("Server running on port 3000"));
```
📌 **Cada módulo es independiente y puede conectarse fácilmente al servidor.**

---

# **🎯 Beneficios de la Combinación**
✅ **Desacoplamiento total**: Cada módulo (`users`, `products`, `orders`) es autónomo y puede escalar por separado.
✅ **Modularidad real**: Se pueden agregar/eliminar funcionalidades sin afectar otras partes del código.
✅ **Más fácil de navegar**: En lugar de buscar archivos en distintas capas (`application/`, `domain/`, etc.), todo lo de cada feature está junto.
✅ **Escalabilidad**: Se pueden dividir módulos en microservicios sin esfuerzo.

---

# **📌 Conclusión**
Usar **Arquitectura Hexagonal con Vertical Slicing** en Node.js permite un diseño **modular, desacoplado y escalable**. Cada funcionalidad tiene su propio espacio y sigue los principios de **Single Responsibility y Dependency Inversion**.

Este enfoque es ideal para proyectos grandes donde la estructura clásica en capas puede volverse difícil de mantener. 🚀
