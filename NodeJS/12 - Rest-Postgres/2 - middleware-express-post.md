
En **Express**, los middlewares son funciones que se ejecutan en la cadena de procesamiento de las solicitudes HTTP antes de que lleguen a la ruta final o después de que la respuesta haya sido enviada.

Cuando manejamos **solicitudes POST**, los middlewares juegan un papel crucial, ya que ayudan a procesar los datos enviados en el cuerpo de la petición, validar la información, gestionar errores y aplicar seguridad.

---

## 🔹 **Middlewares Comunes en POST**
Aquí tienes los middlewares más utilizados cuando se manejan solicitudes **POST** en Express:

| Middleware        | Propósito |
|------------------|----------|
| `express.json()` | Parsea JSON del cuerpo de la petición. |
| `express.urlencoded({ extended: true })` | Parsea datos codificados en `application/x-www-form-urlencoded`. |
| `cors()` | Habilita CORS para permitir solicitudes desde otros dominios. |
| `helmet()` | Agrega cabeceras de seguridad HTTP. |
| `morgan()` | Registra las solicitudes en la consola. |
| `body-parser` (obsoleto) | Anteriormente se usaba para procesar `JSON` y `URL-encoded`, pero ahora `express.json()` lo reemplaza. |
| Middleware de validación (ej. `express-validator`) | Valida y sanitiza los datos antes de procesarlos. |
| Middleware de autenticación (ej. `jsonwebtoken`) | Verifica tokens en headers o en el body. |

---

## 🔹 **Ejemplo de Middleware en un POST en Express**

Vamos a crear un **endpoint POST** para recibir datos de usuarios y aplicar middlewares.

### **📌 Paso 1: Configurar el Servidor con Middlewares**
```ts
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import morgan from 'morgan';

const app = express();

// Middlewares
app.use(cors()); // Habilita CORS
app.use(helmet()); // Seguridad HTTP
app.use(morgan('dev')); // Registra solicitudes en consola
app.use(express.json()); // Parsea JSON en el body
app.use(express.urlencoded({ extended: true })); // Parsea datos en x-www-form-urlencoded

export default app;
```

---

### **📌 Paso 2: Crear una Ruta POST con Validación**
```ts
import { Router, Request, Response } from 'express';

const router = Router();

router.post('/register', (req: Request, res: Response) => {
  const { name, email, password } = req.body;

  // Validaciones básicas
  if (!name || !email || !password) {
    return res.status(400).json({ message: 'Todos los campos son obligatorios' });
  }

  if (password.length < 6) {
    return res.status(400).json({ message: 'La contraseña debe tener al menos 6 caracteres' });
  }

  res.status(201).json({ message: 'Usuario registrado', data: { name, email } });
});

export default router;
```

---

## 🔹 **Middlewares Adicionales para Mejorar el POST**

### **1️⃣ Middleware de Validación con `express-validator`**
```bash
npm install express-validator
```

📌 **Usamos `express-validator` para validar los datos antes de procesarlos:**
```ts
import { Router, Request, Response } from 'express';
import { body, validationResult } from 'express-validator';

const router = Router();

router.post(
  '/register',
  [
    body('name').notEmpty().withMessage('El nombre es obligatorio'),
    body('email').isEmail().withMessage('Debe ser un email válido'),
    body('password').isLength({ min: 6 }).withMessage('La contraseña debe tener al menos 6 caracteres')
  ],
  (req: Request, res: Response) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }

    const { name, email } = req.body;
    res.status(201).json({ message: 'Usuario registrado', data: { name, email } });
  }
);

export default router;
```

---

### **2️⃣ Middleware de Autenticación con JWT**
Si queremos **proteger una ruta** para que solo usuarios autenticados puedan hacer POST, usamos **jsonwebtoken**:

```bash
npm install jsonwebtoken
```

📌 **Middleware para verificar el token en los headers**
```ts
import { Request, Response, NextFunction } from 'express';
import jwt from 'jsonwebtoken';

const authMiddleware = (req: Request, res: Response, next: NextFunction) => {
  const token = req.header('Authorization');

  if (!token) {
    return res.status(401).json({ message: 'Acceso denegado. No hay token' });
  }

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET || 'defaultSecret');
    req.body.user = decoded; // Agrega los datos del usuario al request
    next();
  } catch (error) {
    return res.status(401).json({ message: 'Token inválido' });
  }
};

export default authMiddleware;
```

📌 **Usamos el middleware en la ruta protegida:**
```ts
router.post('/protected', authMiddleware, (req: Request, res: Response) => {
  res.json({ message: 'Ruta protegida', user: req.body.user });
});
```

---

## 🎯 **Resumen**
- `express.json()` → Parsea JSON en el `body`
- `express.urlencoded()` → Parsea `x-www-form-urlencoded`
- `cors()` → Permite solicitudes desde otros dominios
- `helmet()` → Seguridad HTTP
- `morgan()` → Registra logs de solicitudes
- `express-validator` → Valida y sanitiza datos en el `body`
- `jsonwebtoken` → Autenticación con tokens JWT

**👉 Con estos middlewares, aseguramos que los datos de las solicitudes POST sean seguros, válidos y procesables.** 🚀


## multipart/form-data
Si el **POST** está en **form-data**, Express no lo procesa automáticamente, ya que el `express.json()` y `express.urlencoded()` solo manejan **JSON** y **x-www-form-urlencoded**.

Para manejar **multipart/form-data**, necesitas un middleware especial, como **multer**.

---

## **🔹 Middleware para Procesar `multipart/form-data` con Multer**
Multer es un middleware de Express que permite manejar **archivos y datos en form-data**.

📌 **Instalar Multer**:
```bash
npm install multer
```

---

### **📌 Configurar Multer en una Ruta POST**
Vamos a permitir subir imágenes de perfil en una solicitud `POST /upload`.

#### **1️⃣ Configuración básica de Multer**
```ts
import express, { Request, Response } from 'express';
import multer from 'multer';
import path from 'path';

const app = express();

// Configuración del almacenamiento
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, 'uploads/'); // Carpeta donde se guardan los archivos
  },
  filename: (req, file, cb) => {
    cb(null, Date.now() + path.extname(file.originalname)); // Nombre único para evitar duplicados
  },
});

const upload = multer({ storage });

app.post('/upload', upload.single('profilePic'), (req: Request, res: Response) => {
  if (!req.file) {
    return res.status(400).json({ message: 'No se subió ningún archivo' });
  }

  res.status(200).json({
    message: 'Archivo subido exitosamente',
    file: req.file.filename,
  });
});

app.listen(3000, () => {
  console.log('Servidor corriendo en http://localhost:3000');
});
```
---

### **📌 Explicación del Código**
1. **Multer** se configura con `diskStorage`, definiendo:
   - `destination`: Carpeta donde se guardarán los archivos.
   - `filename`: Nombre único basado en la fecha y la extensión original.
2. `upload.single('profilePic')`:
   - Acepta un solo archivo enviado bajo el campo **"profilePic"** en form-data.
3. La respuesta incluye el nombre del archivo guardado.

---

### **📌 Enviar Datos y Archivos en `form-data`**
Si quieres enviar datos adicionales junto con el archivo (ej. nombre y email), simplemente lo agregas en el **body** y lo accedes a través de `req.body`:

```ts
app.post('/register', upload.single('profilePic'), (req: Request, res: Response) => {
  const { name, email } = req.body;

  if (!req.file) {
    return res.status(400).json({ message: 'No se subió ninguna imagen' });
  }

  res.status(201).json({
    message: 'Usuario registrado exitosamente',
    data: { name, email, profilePic: req.file.filename },
  });
});
```

---

## **🔹 Manejo de Múltiples Archivos**
Si necesitas subir **varios archivos**, usa `upload.array()`:

```ts
app.post('/upload-multiple', upload.array('images', 5), (req: Request, res: Response) => {
  if (!req.files || !(req.files as Express.Multer.File[]).length) {
    return res.status(400).json({ message: 'No se subieron archivos' });
  }

  const files = (req.files as Express.Multer.File[]).map(file => file.filename);

  res.status(200).json({
    message: 'Archivos subidos exitosamente',
    files,
  });
});
```
📌 Aquí `upload.array('images', 5)` permite subir hasta **5 archivos** en el campo `images`.

---

## **🔹 Alternativa: Multer en Memoria (Sin Guardar en Disco)**
Si prefieres manejar los archivos en memoria (ej. para subir a una base de datos o almacenamiento en la nube), usa `storage: multer.memoryStorage()`:

```ts
const uploadMemory = multer({ storage: multer.memoryStorage() });

app.post('/upload-memory', uploadMemory.single('file'), (req: Request, res: Response) => {
  if (!req.file) {
    return res.status(400).json({ message: 'No se subió ningún archivo' });
  }

  res.status(200).json({
    message: 'Archivo recibido en memoria',
    file: req.file.originalname,
    size: req.file.size,
  });
});
```

📌 En este caso, el archivo **no se guarda en disco**, sino que queda en `req.file.buffer`, lo que es útil para subirlo a **AWS S3, Firebase, o una base de datos**.

---

## **🎯 Resumen**
| Middleware | Propósito |
|------------|----------|
| `express.json()` | Procesa JSON (No sirve para `form-data`). |
| `express.urlencoded()` | Procesa `x-www-form-urlencoded` (No sirve para `form-data`). |
| `multer` | Permite procesar `multipart/form-data` (archivos e imágenes). |
| `upload.single('campo')` | Sube un solo archivo. |
| `upload.array('campo', límite)` | Sube múltiples archivos. |
| `multer.memoryStorage()` | Guarda archivos en memoria en lugar de disco. |

💡 **Conclusión**: Para manejar `form-data` en `POST`, **necesitas Multer** porque Express no lo procesa por defecto. 🚀
