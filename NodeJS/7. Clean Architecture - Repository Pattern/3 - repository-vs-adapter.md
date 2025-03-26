## **Patrón Adaptador** y el **Patrón Repositorio** tienen similitudes, pero cumplen propósitos diferentes dentro de una arquitectura limpia.

---

## **🔹 Similitudes entre el Patrón Adaptador y el Patrón Repositorio**
1. **Ambos desacoplan la lógica de negocio de la infraestructura**
   - Evitan que la capa de aplicación dependa directamente de APIs externas, bases de datos o servicios de terceros.

2. **Ambos exponen una interfaz clara**
   - Definen una interfaz común para que la capa de aplicación no dependa de implementaciones concretas.

3. **Ambos pueden modificar el formato de los datos**
   - Transforman los datos de la fuente (base de datos, API externa, etc.) para que se ajusten a lo que espera la lógica de negocio.

---

## **🔹 Diferencias Clave**
| Característica       | Patrón Adaptador | Patrón Repositorio |
|----------------------|-----------------|---------------------|
| **Propósito** | Adaptar una API externa a la interfaz esperada por la aplicación. | Proporcionar una abstracción sobre el acceso a datos (normalmente bases de datos). |
| **Uso común** | Integración con servicios externos (APIs, archivos, microservicios). | Acceso a datos internos (base de datos, ORM). |
| **Capa donde se ubica** | Capa de infraestructura (para adaptar API externas). | Capa de infraestructura (para encapsular acceso a datos). |
| **Ejemplo** | Adaptar OpenWeather API a una interfaz común. | Crear un repositorio para obtener usuarios desde una base de datos. |

---

## **🔹 Cuándo Usar Cada Patrón**
🔹 **Usar Adaptador** cuando necesitas integrar una API externa sin afectar la lógica de negocio.
🔹 **Usar Repositorio** cuando necesitas manejar operaciones CRUD con una base de datos sin exponer detalles de la infraestructura.

---

## **🔹 Ejemplo Comparativo**
### **Ejemplo de Patrón Adaptador (API Externa)**
#### 📌 **Queremos obtener datos desde OpenWeather API y adaptarlos a nuestra aplicación.**
```ts
// Interfaz esperada
export interface IWeatherService {
  getTemperature(city: string): Promise<number>;
}

// Servicio externo
export class ExternalWeatherAPI {
  async fetchWeather(city: string): Promise<{ temp: number }> {
    return { temp: 25 }; // Simulación de API
  }
}

// Adaptador que transforma la API externa a la interfaz esperada
export class OpenWeatherAdapter implements IWeatherService {
  private api: ExternalWeatherAPI;

  constructor(api: ExternalWeatherAPI) {
    this.api = api;
  }

  async getTemperature(city: string): Promise<number> {
    const data = await this.api.fetchWeather(city);
    return data.temp;
  }
}
```
✅ **El Adaptador transforma la API externa para que sea compatible con la aplicación.**

---

### **Ejemplo de Patrón Repositorio (Base de Datos)**
#### 📌 **Queremos acceder a la base de datos para obtener usuarios sin exponer SQL directamente.**
```ts
// Interfaz esperada
export interface IUserRepository {
  findUserById(id: number): Promise<User | null>;
}

// Implementación con base de datos (ejemplo con Sequelize)
export class UserRepository implements IUserRepository {
  async findUserById(id: number): Promise<User | null> {
    return await UserModel.findByPk(id);
  }
}
```
✅ **El Repositorio encapsula el acceso a la base de datos y expone una interfaz limpia.**

---

## **🚀 Conclusión**
🔹 **Ambos patrones encapsulan detalles de implementación y exponen interfaces limpias.**
🔹 **El Adaptador se usa para integrar APIs externas, mientras que el Repositorio es para bases de datos.**
🔹 **Si una API externa se comporta como una base de datos (por ejemplo, Firebase, Supabase, o una API REST de usuarios), se pueden combinar ambos patrones.**


## Vamos a combinar ambos patrones en un ejemplo donde accedemos a usuarios desde **dos fuentes distintas**:  
1. **Base de datos interna** (usando el **Patrón Repositorio**).  
2. **API externa** (usando el **Patrón Adaptador**).  

🔹 **Objetivo:** Crear un servicio de usuarios que pueda obtener datos desde la base de datos o desde una API externa, sin que la capa de aplicación sepa de dónde vienen los datos.  

---

## **📌 Paso 1: Definir una Interfaz Común**
Ambas fuentes de datos (base de datos y API externa) deben implementar la misma interfaz.  

```ts
// Definimos una interfaz común para acceder a los usuarios
export interface IUserService {
  getUserById(id: number): Promise<User | null>;
}

// Definimos nuestro modelo de usuario
export class User {
  constructor(public id: number, public name: string, public email: string) {}
}
```
✅ **Esto permite que la capa de aplicación use un servicio de usuario sin importar su origen.**  

---

## **📌 Paso 2: Implementar el Patrón Repositorio (Base de Datos)**
```ts
import { IUserService, User } from "./IUserService";
import { UserModel } from "./models/UserModel"; // Supongamos que usamos Sequelize

export class UserRepository implements IUserService {
  async getUserById(id: number): Promise<User | null> {
    const user = await UserModel.findByPk(id);
    if (!user) return null;
    return new User(user.id, user.name, user.email);
  }
}
```
✅ **Este repositorio encapsula el acceso a la base de datos.**  

---

## **📌 Paso 3: Implementar el Patrón Adaptador (API Externa)**
```ts
import { IUserService, User } from "./IUserService";
import axios from "axios";

export class ExternalUserAPI {
  async fetchUser(id: number): Promise<{ id: number; username: string; email: string }> {
    const response = await axios.get(`https://api.example.com/users/${id}`);
    return response.data;
  }
}

export class UserAPIAdapter implements IUserService {
  private api: ExternalUserAPI;

  constructor(api: ExternalUserAPI) {
    this.api = api;
  }

  async getUserById(id: number): Promise<User | null> {
    const data = await this.api.fetchUser(id);
    return new User(data.id, data.username, data.email);
  }
}
```
✅ **Este adaptador permite que la API externa se use como si fuera parte de nuestra aplicación.**  

---

## **📌 Paso 4: Crear un Servicio que Use Ambos Patrones**
Vamos a hacer que nuestra aplicación pueda elegir entre **repositorio (base de datos)** o **adaptador (API externa)** dinámicamente.  

```ts
export class UserService {
  private source: IUserService;

  constructor(source: IUserService) {
    this.source = source;
  }

  async getUserById(id: number): Promise<User | null> {
    return this.source.getUserById(id);
  }
}
```
✅ **Este servicio puede recibir cualquier implementación de `IUserService`, ya sea un repositorio o un adaptador.**  

---

## **📌 Paso 5: Usarlo en la Aplicación**
```ts
const useExternalAPI = true; // Cambia esto según la configuración

let userService: UserService;
if (useExternalAPI) {
  userService = new UserService(new UserAPIAdapter(new ExternalUserAPI()));
} else {
  userService = new UserService(new UserRepository());
}

async function main() {
  const user = await userService.getUserById(1);
  console.log(user);
}

main();
```
✅ **Aquí decidimos si obtenemos los usuarios de la base de datos o de la API externa.**  

---

## **🚀 Conclusión**
🔹 **El Repositorio encapsula la base de datos.**  
🔹 **El Adaptador permite usar una API externa con la misma interfaz.**  
🔹 **Podemos intercambiar ambas fuentes sin modificar la capa de aplicación.**  

