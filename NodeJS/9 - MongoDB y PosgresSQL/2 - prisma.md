### ¿Qué es Prisma?
Prisma es un **ORM (Object-Relational Mapping)** de última generación para **Node.js y TypeScript**, que facilita la interacción con bases de datos relacionales como **PostgreSQL**. Su objetivo es simplificar las consultas y la gestión de datos con un enfoque declarativo y tipado.

---

### ¿Para qué sirve?
Prisma ayuda a:
1. **Definir el esquema** de la base de datos usando un lenguaje declarativo (`schema.prisma`).
2. **Generar un cliente de TypeScript** con tipado fuerte para realizar consultas seguras.
3. **Ejecutar migraciones** de la base de datos de forma sencilla.
4. **Optimizar el acceso a los datos**, evitando consultas manuales con SQL.

---

### ⚙️ ¿Cómo funciona?
Prisma tiene tres partes principales:

1. **Prisma Schema (`schema.prisma`)**:
   Define la estructura de la base de datos, los modelos y las relaciones.

2. **Prisma Client**:
   Un cliente generado automáticamente con funciones tipadas para interactuar con la base de datos.

3. **Prisma Migrate**:
   Herramienta para gestionar cambios en la base de datos mediante migraciones.

---

### Instalación y Configuración (PostgreSQL)
1️⃣ **Instalar Prisma y PostgreSQL Client**
```bash
npm install @prisma/client
npm install --save-dev prisma
```

2️⃣ **Inicializar Prisma**
```bash
npx prisma init
```
Esto crea un archivo `.env` y una carpeta `prisma/` con `schema.prisma`.

3️⃣ **Configurar PostgreSQL en `.env`**
```env
DATABASE_URL="postgresql://user:password@localhost:5432/mydatabase"
```

4️⃣ **Definir el esquema en `prisma/schema.prisma`**
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id    Int     @id @default(autoincrement())
  name  String
  email String  @unique
  posts Post[]
}

model Post {
  id       Int    @id @default(autoincrement())
  title    String
  content  String?
  userId   Int
  user     User   @relation(fields: [userId], references: [id])
}
```

5️⃣ **Ejecutar la migración**
```bash
npx prisma migrate dev --name init
```

6️⃣ **Generar el cliente de Prisma**
```bash
npx prisma generate
```

---

### Uso en TypeScript
1️⃣ **Importar y conectar Prisma Client**
```typescript
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();
```

2️⃣ **Crear un usuario**
```typescript
const newUser = await prisma.user.create({
  data: {
    name: "John Doe",
    email: "john@example.com",
  },
});
console.log(newUser);
```

3️⃣ **Obtener usuarios**
```typescript
const users = await prisma.user.findMany();
console.log(users);
```

4️⃣ **Actualizar usuario**
```typescript
const updatedUser = await prisma.user.update({
  where: { email: "john@example.com" },
  data: { name: "John Updated" },
});
```

5️⃣ **Eliminar usuario**
```typescript
await prisma.user.delete({ where: { email: "john@example.com" } });
```

---

### Beneficios de Prisma
✔ **Tipado fuerte** en TypeScript.
✔ **Menos código SQL manual**.
✔ **Mejor mantenimiento** con migraciones estructuradas.
✔ **Consultas optimizadas y relaciones fáciles**.

Prisma simplifica el manejo de bases de datos en proyectos **Node.js + TypeScript + PostgreSQL**, mejorando la productividad y seguridad en las consultas.
