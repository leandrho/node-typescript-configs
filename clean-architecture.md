## **Arquitectura Limpia (Clean Architecture) en Node.js**

La **Arquitectura Limpia**, propuesta por **Robert C. Martin (Uncle Bob)**, es un enfoque para estructurar el código de manera que **sea independiente de frameworks, UI, bases de datos y otras herramientas externas**. Su objetivo principal es mantener la lógica de negocio pura y separada de detalles de implementación.

---

## **📌 Principios clave**
### 1️⃣ **Dependencias dirigidas hacia adentro**
   - Los módulos de **alto nivel** (lógica de negocio) **no dependen** de los módulos de **bajo nivel** (frameworks, bases de datos, etc.).
   - Solo las capas externas dependen de las internas, pero nunca al revés.

### 2️⃣ **Separación de responsabilidades**
   - La aplicación se divide en **capas concéntricas**, cada una con una responsabilidad específica.

### 3️⃣ **Independencia de frameworks y tecnología**
   - Puedes cambiar de base de datos, framework o librerías sin afectar la lógica de negocio.

### 4️⃣ **Regla de la inversión de dependencias**
   - La lógica de negocio no debe depender directamente de herramientas externas, sino de **interfaces** (abstracciones).

---

## **📍 Estructura de capas**
La **Arquitectura Limpia** se organiza en capas concéntricas, donde la lógica de negocio es el núcleo y las dependencias externas se mantienen en los bordes.

📌 **Diagrama de capas**:
```
+----------------------+
|     Infraestructura  |  (Express, TypeORM, MongoDB, etc.)
+----------------------+
|       Interfaces     |  (Controllers, Presenters)
+----------------------+
|       Casos de Uso   |  (Aplicación, Servicios)
+----------------------+
|       Entidades      |  (Modelos de Negocio)
+----------------------+
```

📌 **Explicación de cada capa:**
1. **Entidades (Entities)**
   - Representan los objetos de dominio (Ej: `User`, `Product`).
   - Deben ser **puros**, sin lógica de infraestructura (base de datos, HTTP, etc.).
   - Se implementan como clases o interfaces en TypeScript.

2. **Casos de Uso (Use Cases o Application Services)**
   - Contienen la **lógica de negocio** (Ej: `CreateUser`, `PlaceOrder`).
   - No interactúan directamente con la base de datos ni con frameworks.
   - Solo trabajan con **interfaces**.

3. **Interfaces (Controllers, Gateways, Presenters)**
   - Adaptan los **casos de uso** a diferentes interfaces externas (REST, GraphQL, CLI, etc.).
   - Aquí se definen los controladores en Express/NestJS, o GraphQL resolvers.

4. **Infraestructura (Frameworks, DB, API Externa)**
   - Implementaciones de detalles como bases de datos, servidores web, autenticación, etc.
   - Puede contener Repositorios (`UserRepository`, `ProductRepository`) que implementan interfaces definidas en la capa de aplicación.

---

## **🛠 Implementación en Node.js con TypeScript**
Supongamos que estamos construyendo un **sistema de gestión de usuarios** en **Node.js** con **Express** y **MongoDB**.

📂 **Estructura del proyecto**
```
/src
 ├── /domain        # Entidades y Modelos de Dominio
 ├── /application   # Casos de Uso
 ├── /interfaces    # Controladores y Puertos (Gateways)
 ├── /infrastructure # Repositorios, DB, Frameworks
 ├── /config        # Configuración de la aplicación
 ├── server.ts      # Punto de entrada
```

---

## **1️⃣ Entidad: User**
📂 `src/domain/entities/User.ts`
```ts
export class User {
  constructor(
    public readonly id: string,
    public name: string,
    public email: string
  ) {}

  validateEmail(): boolean {
    return /\S+@\S+\.\S+/.test(this.email);
  }
}
```

---

## **2️⃣ Caso de uso: Crear Usuario**
📂 `src/application/use-cases/CreateUser.ts`
```ts
import { User } from "../../domain/entities/User";
import { UserRepository } from "../interfaces/UserRepository";

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

---

## **3️⃣ Interfaz del Repositorio (Contrato)**
📂 `src/application/interfaces/UserRepository.ts`
```ts
import { User } from "../../domain/entities/User";

export interface UserRepository {
  save(user: User): Promise<void>;
  findByEmail(email: string): Promise<User | null>;
}
```

---

## **4️⃣ Implementación del Repositorio (MongoDB)**
📂 `src/infrastructure/repositories/MongoUserRepository.ts`
```ts
import { UserRepository } from "../../application/interfaces/UserRepository";
import { User } from "../../domain/entities/User";
import { db } from "../database/mongo";

export class MongoUserRepository implements UserRepository {
  async save(user: User): Promise<void> {
    await db.collection("users").insertOne(user);
  }

  async findByEmail(email: string): Promise<User | null> {
    const data = await db.collection("users").findOne({ email });
    return data ? new User(data.id, data.name, data.email) : null;
  }
}
```

---

## **5️⃣ Controlador en Express**
📂 `src/interfaces/controllers/UserController.ts`
```ts
import { Request, Response } from "express";
import { CreateUser } from "../../application/use-cases/CreateUser";
import { MongoUserRepository } from "../../infrastructure/repositories/MongoUserRepository";

export class UserController {
  static async create(req: Request, res: Response) {
    try {
      const userRepository = new MongoUserRepository();
      const createUser = new CreateUser(userRepository);

      const user = await createUser.execute(req.body.name, req.body.email);
      res.status(201).json(user);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  }
}
```

---

## **6️⃣ Servidor Express**
📂 `src/server.ts`
```ts
import express from "express";
import { UserController } from "./interfaces/controllers/UserController";

const app = express();
app.use(express.json());

app.post("/users", UserController.create);

app.listen(3000, () => console.log("Server running on port 3000"));
```
---

## ** Nota **
En la **Arquitectura Limpia**, cada capa solo puede depender de capas **más internas**, pero **nunca de capas más externas**. Entonces, ¿por qué `CreateUser` puede referenciar tanto a `User` como a `UserRepository`?

### **📌 Regla clave: Dependencias siempre hacia adentro**
La capa de **Casos de Uso** (`application/use-cases`) **puede** depender de:
1. **Entidades (`domain/entities`)** porque están en un nivel más bajo.
2. **Interfaces (`application/interfaces`)** porque son solo contratos (abstracciones), **no implementaciones**.

---

## **📌 Beneficios de esta arquitectura en Node.js**
✅ **Código modular y reutilizable**
✅ **Fácil de probar (unit testing en cada capa)**
✅ **Independiente de frameworks** (podría cambiar Express por Fastify o NestJS sin afectar la lógica de negocio)
✅ **Facilita la escalabilidad**

---

## **📌 Conclusión**
La **Arquitectura Limpia** ayuda a organizar el código de forma mantenible y escalable. Separando la lógica de negocio de la infraestructura, puedes cambiar herramientas sin afectar la lógica central.

Si bien puede parecer un poco más compleja al inicio, a largo plazo permite desarrollar aplicaciones Node.js robustas y modulares. 🚀

---
