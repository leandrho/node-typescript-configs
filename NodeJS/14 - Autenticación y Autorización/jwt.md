## 🔹 **¿Qué es JWT?**
JWT (**JSON Web Token**) es un estándar abierto (**RFC 7519**) que se usa para transmitir información entre dos partes de forma segura y compacta. Se utiliza comúnmente para autenticación y autorización en aplicaciones web.

---

## 🔹 **¿Para qué sirve?**
JWT permite:
- 🔑 **Autenticar usuarios** sin necesidad de almacenar sesiones en el servidor.
- 📩 **Transmitir información segura** entre cliente y servidor.
- 🔄 **Autorizar acceso a recursos** mediante tokens en APIs.

🔹 **Ejemplo práctico:**
1. Un usuario se loguea con su email y contraseña.
2. El servidor genera un JWT y lo envía al cliente.
3. El cliente usa ese JWT en cada petición para demostrar que está autenticado.
4. El servidor valida el JWT sin necesidad de una base de datos.

---

## 🔹 **Estructura de un JWT**
Un JWT tiene **tres partes** separadas por puntos (`.`):

```
header.payload.signature
```

Ejemplo de un JWT:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwibmFtZSI6IlBlcGUifQ.WpO4Lf6H6hXa93U8bXqIbLgt9xlJSCcX7PlfUfjg1K4
```

📌 **Desglose de las partes:**  
1️⃣ **Header** (Encabezado) – Contiene el tipo de token y algoritmo de firma.
   ```json
   {
     "alg": "HS256",
     "typ": "JWT"
   }
   ```

2️⃣ **Payload** (Cuerpo) – Contiene los datos del usuario y otras claims (información).
   ```json
   {
     "id": 1,
     "name": "Pepe",
     "role": "admin"
   }
   ```

3️⃣ **Signature** (Firma) – Se usa para validar que el token no ha sido alterado.
   ```bash
   HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
   ```

---

## 🔹 **Cómo se usa JWT en una API**
### **1️⃣ Instalación de librerías en Node.js**
Para usar JWT en **Node.js con Express**, instala `jsonwebtoken`:

```bash
npm install jsonwebtoken
```

---

### **2️⃣ Generar un JWT al autenticar un usuario**
Cuando un usuario inicia sesión, generamos un token:

```javascript
const jwt = require("jsonwebtoken");

const usuario = { id: 1, nombre: "Pepe" };
const claveSecreta = "secreto_super_seguro";

const token = jwt.sign(usuario, claveSecreta, { expiresIn: "1h" });

console.log(token);
```

**Explicación:**
- `jwt.sign(datos, clave, opciones)`:
  - `datos`: Información del usuario.
  - `clave`: Se usa para firmar el token.
  - `options`: Tiempo de expiración del token (`1h` = 1 hora).

---

### **3️⃣ Enviar el JWT al cliente**
El servidor devuelve el token al usuario después de iniciar sesión:

```javascript
app.post("/login", (req, res) => {
  const usuario = { id: 1, nombre: "Pepe" };
  const token = jwt.sign(usuario, "secreto_super_seguro", { expiresIn: "1h" });

  res.json({ token });
});
```

📌 **El cliente debe almacenar este token** (en `localStorage` o `sessionStorage`) y enviarlo en cada petición a la API.

---

### **4️⃣ Proteger rutas con JWT (Middleware de autenticación)**
Cada vez que el cliente haga una petición, debe enviar el token en los headers.

Middleware para verificar el JWT en Express:

```javascript
const verificarToken = (req, res, next) => {
  const token = req.headers["authorization"];

  if (!token) return res.status(401).json({ mensaje: "Acceso denegado" });

  try {
    const verificado = jwt.verify(token.split(" ")[1], "secreto_super_seguro");
    req.usuario = verificado;
    next();
  } catch (error) {
    res.status(403).json({ mensaje: "Token inválido" });
  }
};
```

📌 **Explicación:**
- Busca el token en `req.headers["authorization"]`.
- Verifica la firma con `jwt.verify(token, clave)`.
- Si el token es válido, agrega los datos del usuario a `req.usuario`.
- Si el token es inválido, devuelve un error `403`.

---

### **5️⃣ Usar el middleware en una ruta protegida**
```javascript
app.get("/perfil", verificarToken, (req, res) => {
  res.json({ mensaje: `Bienvenido, ${req.usuario.nombre}` });
});
```

📌 **El cliente debe enviar el token en la cabecera de cada petición**:
```http
GET /perfil HTTP/1.1
Authorization: Bearer <TOKEN_AQUÍ>
```

---

## 🔹 **Ventajas y desventajas de JWT**
✅ **Ventajas:**
- No necesita almacenar sesiones en el servidor.
- Es seguro si se usa correctamente.
- Se puede usar en distintos servicios y plataformas.

❌ **Desventajas:**
- Si el token es robado, se puede usar hasta que expire.
- No se puede invalidar un token sin manejar una lista de tokens revocados.
- El tamaño del token puede ser grande si contiene demasiada información.

---

## 🔹 **Resumen final**
1️⃣ **JWT es un token que permite autenticación sin sesiones en el servidor.**  
2️⃣ **Tiene 3 partes:** Header, Payload y Signature.  
3️⃣ **El servidor genera un token cuando el usuario inicia sesión.**  
4️⃣ **El cliente guarda el token y lo envía en cada petición.**  
5️⃣ **El servidor verifica el token en cada solicitud.**  
