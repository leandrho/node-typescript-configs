# Patrón de Diseño Adaptador

El **Patrón Adaptador** es un patrón estructural que permite que dos interfaces incompatibles trabajen juntas. En **Node.js con TypeScript y Arquitectura Limpia**, se usa para desacoplar la infraestructura (como APIs externas, bases de datos o servicios de terceros) del **dominio** de la aplicación.

---

## 🔹 **¿Por qué usar el Patrón Adaptador en Arquitectura Limpia?**
En Arquitectura Limpia, las capas deben estar desacopladas. La lógica de negocio (**Dominio** y **Casos de Uso**) no debe depender directamente de detalles de infraestructura (bases de datos, APIs externas, etc.).

El **Adaptador** actúa como un **puente** entre la **interfaz esperada** por el dominio y la **implementación real** de la infraestructura.

---

## 🔹 **Ejemplo práctico con Node.js y TypeScript**
Supongamos que tenemos una aplicación que gestiona usuarios y queremos desacoplar la lógica de negocio del servicio de persistencia (por ejemplo, una base de datos o una API externa).

---

### **1️⃣ Definir la Interfaz en el Dominio**
En la capa de dominio, definimos una **interfaz** que describe el contrato esperado por nuestra aplicación.

```ts
// src/domain/repositories/UserRepository.ts
export interface UserRepository {
  getUserById(id: string): Promise<User | null>;
  createUser(user: User): Promise<User>;
}
```

---

### **2️⃣ Implementar un Adaptador para una API Externa**
Este adaptador implementa la interfaz esperada y llama a la API real.

```ts
// src/infrastructure/adapters/ExternalUserApiAdapter.ts
import { UserRepository } from "../../domain/repositories/UserRepository";
import { User } from "../../domain/entities/User";
import axios from "axios";

export class ExternalUserApiAdapter implements UserRepository {
  private apiUrl = "https://jsonplaceholder.typicode.com/users";

  async getUserById(id: string): Promise<User | null> {
    try {
      const response = await axios.get(`${this.apiUrl}/${id}`);
      return response.data;
    } catch (error) {
      return null;
    }
  }

  async createUser(user: User): Promise<User> {
    const response = await axios.post(this.apiUrl, user);
    return response.data;
  }
}
```

---

### **3️⃣ Caso de Uso (Application Layer)**
La capa de **aplicación** usa el repositorio sin importar la implementación específica.

```ts
// src/application/use-cases/GetUserUseCase.ts
import { UserRepository } from "../../domain/repositories/UserRepository";

export class GetUserUseCase {
  constructor(private userRepository: UserRepository) {}

  async execute(id: string) {
    return this.userRepository.getUserById(id);
  }
}
```

---

### **4️⃣ Inyección de Dependencias en la Capa de Infraestructura**
En la capa de infraestructura, **inyectamos el adaptador** en los casos de uso.

```ts
// src/main.ts
import { ExternalUserApiAdapter } from "./infrastructure/adapters/ExternalUserApiAdapter";
import { GetUserUseCase } from "./application/use-cases/GetUserUseCase";

const userRepository = new ExternalUserApiAdapter();
const getUserUseCase = new GetUserUseCase(userRepository);

getUserUseCase.execute("1").then(console.log);
```

---

## 🔹 **Beneficios del Patrón Adaptador en esta Arquitectura**
✅ **Desacoplamiento**: La lógica de negocio no depende directamente de la API externa.
✅ **Facilidad de cambio**: Podemos cambiar de API o de base de datos sin modificar los casos de uso.
✅ **Testabilidad**: Se pueden usar mocks para probar la lógica sin depender de servicios externos.
