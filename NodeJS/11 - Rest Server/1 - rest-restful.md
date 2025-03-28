## 🔥 **Representational State Transfer (REST) y RESTful**

### 1️⃣ **¿Qué es Representational State Transfer (REST)?**
REST es un **estilo de arquitectura de software** para sistemas distribuidos como aplicaciones web. Fue propuesto por **Roy Fielding** en su tesis doctoral en el año 2000 y se basa en el uso de **recursos** identificados por URLs y manipulados a través de operaciones estándar del protocolo HTTP.

---

## 🌍 **Principios Fundamentales de REST**
Para que un sistema sea **RESTful** (es decir, siga la arquitectura REST), debe cumplir con los siguientes **seis principios**:

### 🔹 1. **Arquitectura Cliente-Servidor**
El sistema se divide en dos partes:
✅ **Cliente**: Solicita recursos al servidor (por ejemplo, un navegador o una aplicación móvil).
✅ **Servidor**: Proporciona los recursos y responde a las solicitudes del cliente.

➡️ **Ventaja**: Permite escalar los sistemas al separar responsabilidades.

---

### 🔹 2. **Interfaz Uniforme**
Todas las interacciones con el servidor siguen reglas bien definidas:
- Se accede a los recursos a través de URLs únicas.
- Se usan métodos HTTP estándar (`GET`, `POST`, `PUT`, `DELETE`).
- Las respuestas están en un formato estándar (generalmente **JSON** o **XML**).
- Se usan códigos de estado HTTP para indicar el resultado de la operación.

➡️ **Ventaja**: Permite que clientes y servidores evolucionen por separado sin necesidad de conocer los detalles internos.

---

### 🔹 3. **Stateless (Sin Estado)**
Cada petición del cliente **debe contener toda la información necesaria** para procesarla.
El servidor **no mantiene estado** entre peticiones.

Ejemplo de **estado** en una API no RESTful:
❌ **El servidor guarda información de la sesión del usuario** y espera que las siguientes peticiones se hagan con la misma sesión.

Ejemplo de **stateless en REST**:
✅ **Cada petición incluye toda la información necesaria** (por ejemplo, un `token` de autenticación).

➡️ **Ventaja**: Facilita la escalabilidad porque cualquier servidor puede procesar la solicitud sin depender de peticiones previas.

---

### 🔹 4. **Cacheable (Cacheable)**
Las respuestas del servidor deben indicar si pueden ser **almacenadas en caché**.
Esto mejora el rendimiento y reduce la carga en el servidor.

Ejemplo:
✅ Si un recurso cambia poco, el servidor puede incluir un encabezado `Cache-Control: max-age=3600` para indicar que la respuesta es válida por 1 hora.

➡️ **Ventaja**: Reduce la latencia y mejora la eficiencia de la red.

---

### 🔹 5. **Sistema en Capas (Layered System)**
La arquitectura REST permite dividir la aplicación en **capas** que pueden interactuar sin que el cliente sepa cuántas hay.

Ejemplo:
✅ Un cliente puede hacer una petición a una API sin saber si los datos provienen de un servidor, una base de datos, o un servicio de terceros.

➡️ **Ventaja**: Mejora la seguridad y escalabilidad.

---

### 🔹 6. **Código Bajo Demanda (Code on Demand) (Opcional)**
Un servidor puede **enviar código ejecutable al cliente** (como JavaScript) para extender su funcionalidad.

Ejemplo:
✅ Un servidor REST envía un script JavaScript que el cliente ejecuta para mejorar la experiencia de usuario.

➡️ **Ventaja**: Permite aplicaciones más dinámicas, pero se usa con precaución por razones de seguridad.

---

## 🔥 **RESTful: Aplicando REST a una API**
Una **API RESTful** es una API que sigue los principios de REST.

**Ejemplo de una API RESTful para gestionar usuarios:**

| Método HTTP  | URL                | Acción                       |
|-------------|--------------------|------------------------------|
| `GET`       | `/users`           | Obtener todos los usuarios   |
| `GET`       | `/users/1`         | Obtener un usuario por ID    |
| `POST`      | `/users`           | Crear un nuevo usuario       |
| `PUT`       | `/users/1`         | Actualizar un usuario        |
| `DELETE`    | `/users/1`         | Eliminar un usuario         |

📌 **Ejemplo de una respuesta en JSON** de `GET /users/1`
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com"
}
```

📌 **Ejemplo de una solicitud para crear un usuario (`POST /users`)**
```json
{
  "name": "Alice",
  "email": "alice@example.com"
}
```
📌 **Ejemplo de respuesta del servidor**
```json
{
  "id": 2,
  "name": "Alice",
  "email": "alice@example.com"
}
```

---

## 🚀 **Diferencias entre REST y RESTful**
| Concepto       | REST | RESTful |
|---------------|------|---------|
| Definición    | Es un conjunto de principios para el diseño de sistemas distribuidos. | Es una API que implementa los principios REST. |
| Estado       | Stateless (sin estado). | Stateless (sin estado). |
| Protocolos    | Puede usar cualquier protocolo (HTTP, WebSockets, etc.). | Se implementa comúnmente sobre HTTP. |
| Enfoque      | Arquitectura de software. | Implementación específica de REST en APIs. |

---

## ✅ **Ventajas de RESTful**
✔ **Simplicidad**: Usa HTTP y JSON, lo que facilita su adopción.
✔ **Escalabilidad**: La arquitectura sin estado permite distribuir cargas fácilmente.
✔ **Interoperabilidad**: Diferentes sistemas pueden interactuar sin problemas.
✔ **Seguridad**: Permite autenticación con `OAuth`, `JWT`, `API Keys`, etc.

---

## 🚫 **Errores Comunes al Diseñar una API RESTful**
1. **No usar correctamente los métodos HTTP**
   ❌ `POST /users/1/delete` (Incorrecto)
   ✅ `DELETE /users/1` (Correcto)

2. **Usar verbos en las URLs**
   ❌ `/getAllUsers` (Incorrecto)
   ✅ `/users` (Correcto)

3. **No usar códigos de estado HTTP adecuados**
   ❌ Retornar `200 OK` en caso de error.
   ✅ Usar `404 Not Found`, `400 Bad Request`, `500 Internal Server Error`, etc.

---

## 🎯 **Conclusión**
- **REST** es un conjunto de principios arquitectónicos para sistemas distribuidos.
- **RESTful** es una implementación de esos principios en una API web.
- REST se basa en **recursos, métodos HTTP, y formatos estándar como JSON**.
- Una API RESTful bien diseñada debe ser **stateless, cacheable y escalable**.
