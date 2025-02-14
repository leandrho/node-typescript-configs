# DTOS

Cuando trabajamos con arquitectura hexagonal y enviamos una entidad del dominio directamente a una API o a cualquier otra capa externa, pueden surgir **problemas de seguridad, encapsulación y mantenimiento**.

---

## 🔴 **Ejemplo del problema: Exposición de la contraseña**
Supongamos que tenemos la siguiente entidad en el dominio:

```ts
export class User {
  constructor(
    public readonly id: string,
    public name: string,
    public email: string,
    private password: string
  ) {}

  validateEmail() {
    if (!this.email.includes("@")) {
      throw new Error("Email inválido");
    }
  }
}
```
### 🔥 **¿Qué pasa si enviamos un `User` directamente como respuesta en la API?**

Imagina que en el controlador tenemos algo así:
```ts
import { Request, Response } from "express";
import { UserRepository } from "../../domain/interfaces/UserRepository";

export class UserController {
  constructor(private userRepository: UserRepository) {}

  async getUser(req: Request, res: Response) {
    const user = await this.userRepository.findById(req.params.id);
    res.json(user); // ⛔️ ¡Aquí está el problema!
  }
}
```
Si hacemos una petición GET `/users/1`, la API responde con el usuario **incluyendo la contraseña**:
```json
{
  "id": "1",
  "name": "Juan Pérez",
  "email": "juan@example.com",
  "password": "supersecreta123"  // ⛔️ EXPUESTA
}
```
🚨 **Problema**:
- **Se filtra información sensible**, lo que es un **gran riesgo de seguridad**.
- **Se expone la estructura interna** de la entidad del dominio.

---

## 🔴 **Otro problema: Exposición de lógica interna**
Si una entidad tiene métodos que contienen reglas de negocio, también podrían ser accedidos fuera del dominio si no tenemos cuidado.

Ejemplo:
```ts
const user = await userRepository.findById("1");
user.validateEmail(); // 🚫 ¡Este método solo debería usarse dentro del dominio!
```
**🚨 Problema**:
- Se permite que otras capas ejecuten lógica de negocio que debería estar encapsulada en el dominio.
- La API o la capa de infraestructura podrían modificar la entidad incorrectamente.

---

## ✅ **Solución: Usar un DTO para controlar la exposición de datos**
Para evitar estos problemas, **no retornamos la entidad del dominio directamente**, sino que transformamos los datos en un DTO (Data Transfer Object).

### **Creamos un DTO para respuesta de la API**
```ts
export class UserResponseDTO {
  constructor(
    public readonly id: string,
    public readonly name: string,
    public readonly email: string
  ) {}
}
```
🔹 **Diferencias clave con la entidad `User`**:
✅ **No incluye la contraseña.**
✅ **No tiene métodos de lógica interna.**
✅ **Solo contiene los datos necesarios para la respuesta.**

---

## ✅ **Aplicando el DTO en el controlador**
Ahora modificamos el controlador para usar el `UserResponseDTO`:

```ts
export class UserController {
  constructor(private userRepository: UserRepository) {}

  async getUser(req: Request, res: Response) {
    const user = await this.userRepository.findById(req.params.id);

    if (!user) {
      return res.status(404).json({ message: "Usuario no encontrado" });
    }

    const userDTO = new UserResponseDTO(user.id, user.name, user.email);
    res.json(userDTO);
  }
}
```
---

## 🎯 **¿Qué logramos con esto?**
🔹 **Protegemos la información sensible** → La API **ya no expone la contraseña**.
🔹 **Encapsulamos la lógica de negocio** → Métodos internos de la entidad **no son accesibles fuera del dominio**.
🔹 **Mantenemos independencia entre capas** → Si cambiamos la entidad `User`, la API sigue funcionando con el DTO.

---

### 🚀 **Resumen**
- **Nunca devuelvas directamente una entidad del dominio en la API**.
- Usa **DTOs para controlar qué datos se exponen**.
- La **capa de aplicación transforma la entidad en un DTO** antes de enviarlo.
- **Mejor seguridad y separación de responsabilidades** en la arquitectura.
