
## Inyección de dependencias (DI)

La inyección de dependencias es un patrón de diseño que te permite desacoplar tus clases al proporcionarles sus dependencias desde el exterior. En lugar de que una clase cree sus propias dependencias, las recibe como parámetros en su constructor.

**Beneficios:**

* **Desacoplamiento:** Reduce la dependencia entre clases, lo que facilita el mantenimiento y la evolución del código.
* **Reutilización:** Las clases se vuelven más reutilizables, ya que no están atadas a implementaciones específicas de sus dependencias.
* **Pruebas unitarias:** Facilita la escritura de pruebas unitarias, ya que puedes reemplazar las dependencias reales con mocks o stubs.
* **Flexibilidad:** Permite cambiar fácilmente las implementaciones de las dependencias sin modificar las clases que las usan.

**Inyección de dependencias en Node.js con TypeScript:**

TypeScript, con sus interfaces y tipado estático, hace que la DI sea aún más poderosa. Puedes definir interfaces para tus dependencias y luego inyectar implementaciones concretas de esas interfaces.

**Arquitectura hexagonal y DI**

La arquitectura hexagonal, también conocida como arquitectura de puertos y adaptadores, se basa en el principio de separar el núcleo de tu aplicación (la lógica de negocio) de sus dependencias externas (bases de datos, APIs, interfaces de usuario).

**¿Cómo se aplica la DI en la arquitectura hexagonal?**

* El núcleo de la aplicación define interfaces (puertos) para sus dependencias.
* Los adaptadores implementan esas interfaces y se encargan de interactuar con el mundo exterior.
* La DI se utiliza para inyectar los adaptadores en el núcleo de la aplicación.

**Estructura de directorios (ejemplo):**

```
src/
├── domain/
│   ├── entities/
│   │   └── user.ts
│   ├── ports/
│   │   └── user-repository.ts
│   └── use-cases/
│       └── create-user.ts
├── infrastructure/
│   ├── database/
│   │   └── user-repository-mongodb.ts
│   ├── http/
│   │   └── user-controller.ts
│   └── di/
│       └── container.ts
└── index.ts
```

**Explicación:**

* **`domain/`**: Contiene la lógica de dominio de la aplicación, incluyendo:
    * **`entities/`**: Define las entidades (objetos de dominio con identidad) y los objetos de valor (objetos de dominio sin identidad).
    * **`ports/`**: Define las interfaces (puertos) para las dependencias externas.
    * **`use-cases/`**: Implementa los casos de uso (interactores) que orquestan la lógica de dominio.
* **`infrastructure/`**: Contiene los adaptadores que implementan los puertos definidos en el dominio:
    * **`database/`**: Implementa los adaptadores para interactuar con las bases de datos.
    * **`http/`**: Implementa los adaptadores para interactuar con las APIs HTTP.
    * **`di/`**: Configura el contenedor de inyección de dependencias.
* **`index.ts`**: El punto de entrada de la aplicación.

**Ejemplo de código (simplificado):**

**`src/domain/ports/user-repository.ts`**

```typescript
export interface UserRepository {
  save(user: User): Promise<void>;
}
```

**`src/infrastructure/database/user-repository-mongodb.ts`**

```typescript
export class UserRepositoryMongoDB implements UserRepository {
  async save(user: User): Promise<void> {
    // Lógica para guardar el usuario en MongoDB
  }
}
```

**`src/domain/use-cases/create-user.ts`**

```typescript
export class CreateUser {
  constructor(private userRepository: UserRepository) {}

  async execute(user: User): Promise<void> {
    await this.userRepository.save(user);
  }
}
```

**`src/infrastructure/di/container.ts`**

```typescript
import { container } from 'tsyringe';
import { UserRepository } from '../../domain/ports/user-repository';
import { UserRepositoryMongoDB } from '../database/user-repository-mongodb';

container.register<UserRepository>('UserRepository', UserRepositoryMongoDB);

export { container };
```

**`src/infrastructure/http/user-controller.ts`**

```typescript
import { container } from '../../infrastructure/di/container';
import { CreateUser } from '../../domain/use-cases/create-user';

export class UserController {
  async createUser(req: Request, res: Response): Promise<void> {
    const createUser = container.resolve(CreateUser);
    await createUser.execute(req.body);
    res.status(201).send();
  }
}
```
