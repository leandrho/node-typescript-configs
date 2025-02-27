# Domain-Driven Design(DDD

El **patrón de diseño guiado por el dominio (Domain-Driven Design, DDD)** es un enfoque de desarrollo de software que enfatiza la organización del código en torno al **modelo de dominio** de la aplicación. En **Node.js con TypeScript**, se utiliza para crear aplicaciones estructuradas, escalables y mantenibles.

---

## 📌 **Principios Claves de DDD**
1. **Modelo de dominio**: Representa las reglas y lógica del negocio.
2. **Lenguaje ubicuo**: Un vocabulario compartido entre desarrolladores y expertos del dominio.
3. **Separación de responsabilidades**: Divide la aplicación en capas bien definidas.
4. **Bounded Contexts (Contextos delimitados)**: Cada subdominio tiene su propio modelo de dominio.
5. **Agregados y entidades**: Las entidades agrupan datos y lógica de negocio, mientras que los agregados controlan la consistencia.

---

## 🏗 **Arquitectura de DDD en Node.js con TypeScript**
En un proyecto de Node.js con TypeScript, DDD se implementa dividiendo la aplicación en **capas**:

1. **Capa de Dominio (`domain/`)** → Contiene reglas de negocio.
2. **Capa de Aplicación (`application/`)** → Orquesta casos de uso.
3. **Capa de Infraestructura (`infrastructure/`)** → Maneja bases de datos, API y frameworks.
4. **Capa de Interfaz (`presentation/`)** → Expone endpoints y recibe solicitudes.

---

## 📂 **Estructura de Carpetas en Node.js con TypeScript**
```bash
src/
│── domain/         # Reglas de negocio
│   ├── entities/   # Entidades y agregados
│   ├── repositories/ # Interfaces de persistencia
│   ├── services/   # Lógica del dominio
│── application/    # Casos de uso
│── infrastructure/ # Implementación de persistencia, APIs, frameworks
│── presentation/   # Controladores, HTTP, GraphQL
│── shared/         # Código común (errores, utils)
```

---

## 🚀 **Ejemplo de Implementación en TypeScript**
### **1️⃣ Definir una Entidad en el Dominio**
Una entidad representa un objeto con identidad única dentro del dominio.

```ts
// src/domain/entities/User.ts
export class User {
  constructor(
    public readonly id: string,
    public name: string,
    public email: string
  ) {
    if (!email.includes("@")) {
      throw new Error("Email inválido");
    }
  }
}
```

---

### **2️⃣ Crear un Repositorio (Interfaz)**
Los repositorios definen la forma en que interactuamos con la persistencia, pero sin implementarla directamente.

```ts
// src/domain/repositories/UserRepository.ts
import { User } from "../entities/User";

export interface UserRepository {
  findById(id: string): Promise<User | null>;
  save(user: User): Promise<void>;
}
```

---

### **3️⃣ Implementar el Caso de Uso**
Los casos de uso encapsulan la lógica de aplicación sin depender de detalles de infraestructura.

```ts
// src/application/use-cases/CreateUser.ts
import { User } from "../../domain/entities/User";
import { UserRepository } from "../../domain/repositories/UserRepository";

export class CreateUser {
  constructor(private userRepository: UserRepository) {}

  async execute(name: string, email: string): Promise<User> {
    const user = new User(Date.now().toString(), name, email);
    await this.userRepository.save(user);
    return user;
  }
}
```

---

### **4️⃣ Implementar la Infraestructura (Base de Datos)**
Aquí usamos una base de datos, como MongoDB o PostgreSQL, implementando la interfaz del repositorio.

```ts
// src/infrastructure/repositories/InMemoryUserRepository.ts
import { UserRepository } from "../../domain/repositories/UserRepository";
import { User } from "../../domain/entities/User";

export class InMemoryUserRepository implements UserRepository {
  private users: User[] = [];

  async findById(id: string): Promise<User | null> {
    return this.users.find(user => user.id === id) || null;
  }

  async save(user: User): Promise<void> {
    this.users.push(user);
  }
}
```

---

### **5️⃣ Crear el Controlador**
Aquí exponemos el caso de uso a través de una API.

```ts
// src/presentation/controllers/UserController.ts
import { Request, Response } from "express";
import { CreateUser } from "../../application/use-cases/CreateUser";
import { InMemoryUserRepository } from "../../infrastructure/repositories/InMemoryUserRepository";

const userRepository = new InMemoryUserRepository();
const createUser = new CreateUser(userRepository);

export class UserController {
  static async create(req: Request, res: Response) {
    const { name, email } = req.body;
    try {
      const user = await createUser.execute(name, email);
      res.status(201).json(user);
    } catch (error) {
      res.status(400).json({ error: error.message });
    }
  }
}
```

---

### **6️⃣ Configurar Express**
Finalmente, creamos el servidor Express para manejar las solicitudes.

```ts
// src/infrastructure/server.ts
import express from "express";
import { UserController } from "../presentation/controllers/UserController";

const app = express();
app.use(express.json());

app.post("/users", UserController.create);

app.listen(3000, () => {
  console.log("🚀 Server running on http://localhost:3000");
});
```

---

## ✅ **Beneficios de DDD en Node.js con TypeScript**
✔ **Código más organizado y escalable**
✔ **Facilita pruebas unitarias**
✔ **Mejor separación de responsabilidades**
✔ **Flexibilidad para cambiar la infraestructura sin afectar la lógica de negocio**

---

## 🎯 **Conclusión**
El **Domain-Driven Design (DDD)** ayuda a estructurar aplicaciones en Node.js con TypeScript separando **dominio, aplicación, infraestructura e interfaz**. Aunque agrega una curva de aprendizaje y requiere mayor esfuerzo inicial, la arquitectura resultante es **modular, mantenible y escalable**.
