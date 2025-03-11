## Winston

**¿Qué es Winston?**

Winston es una librería de registro (logging) para Node.js que proporciona una forma flexible y configurable de registrar mensajes en diferentes destinos (consolas, archivos, bases de datos, servicios externos, etc.). Es una herramienta esencial para depurar, monitorear y mantener aplicaciones Node.js.

**Características principales:**

* **Múltiples transportes:** Permite enviar registros a diferentes destinos simultáneamente.
* **Niveles de registro:** Admite diferentes niveles de registro (error, warn, info, verbose, debug, silly) para controlar la verbosidad de los mensajes.
* **Formatos de registro:** Permite personalizar el formato de los mensajes de registro.
* **Extensibilidad:** Se puede extender con transportes y formatos personalizados.
* **Integración con TypeScript:** Proporciona definiciones de tipo para una experiencia de desarrollo fluida con TypeScript.

**Instalación:**

```bash
npm install winston
npm install --save-dev @types/winston # Si usas TypeScript
```

**Uso básico:**

```typescript
// src/logger.ts
import winston from 'winston';

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'logs/combined.log' }),
  ],
});

export default logger;
```

```typescript
// src/index.ts
import logger from './logger';

logger.info('This is an info message');
logger.warn('This is a warning message');
logger.error('This is an error message');
```

**Explicación:**

* `winston.createLogger()`: Crea una instancia de logger.
* `level`: Define el nivel de registro mínimo que se registrará.
* `format`: Define el formato de los mensajes de registro.
* `transports`: Define los destinos de los mensajes de registro.

**Niveles de registro:**

Winston admite los siguientes niveles de registro, ordenados de mayor a menor gravedad:

* `error`: Errores graves que impiden el funcionamiento normal de la aplicación.
* `warn`: Advertencias sobre situaciones inesperadas que podrían causar problemas.
* `info`: Información general sobre el funcionamiento de la aplicación.
* `http`: Registros de solicitudes y respuestas HTTP.
* `verbose`: Información detallada para depuración.
* `debug`: Información aún más detallada para depuración.
* `silly`: El nivel de registro más detallado.

**Formatos de registro:**

Winston proporciona varios formatos de registro predefinidos, como:

* `winston.format.json()`: Formatea los mensajes como JSON.
* `winston.format.simple()`: Formatea los mensajes como texto simple.
* `winston.format.timestamp()`: Agrega una marca de tiempo a los mensajes.
* `winston.format.prettyPrint()`: Formatea los objetos para que sean legibles.
* `winston.format.printf()`: Permite definir formatos personalizados.

También puedes combinar formatos usando `winston.format.combine()`:

```typescript
const logger = winston.createLogger({
  // ...
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.prettyPrint()
  ),
  // ...
});
```

**Transportes:**

Winston proporciona varios transportes predefinidos, como:

* `winston.transports.Console()`: Envía los registros a la consola.
* `winston.transports.File()`: Envía los registros a un archivo.
* `winston.transports.Http()`: Envía los registros a un servicio HTTP.
* `winston.transports.MongoDB()`: Envía los registros a una base de datos MongoDB.

Puedes agregar múltiples transportes a un logger para enviar los registros a diferentes destinos simultáneamente.

**Integración con TypeScript:**

Winston proporciona definiciones de tipo para TypeScript, lo que permite una experiencia de desarrollo fluida y segura. Puedes definir interfaces para tus propios objetos de registro y usarlas con Winston.

**Ejemplo avanzado con TypeScript:**

```typescript
// src/interfaces/log-data.ts
export interface LogData {
  userId: string;
  action: string;
  message: string;
}
```

```typescript
// src/logger.ts
import winston from 'winston';
import { LogData } from './interfaces/log-data';

const logger = winston.createLogger({
  // ...
});

export function logAction(data: LogData) {
  logger.info('User action', data);
}

export default logger;
```

```typescript
// src/index.ts
import logger, { logAction } from './logger';

logAction({
  userId: '123',
  action: 'login',
  message: 'User logged in successfully',
});
```

**Consejos adicionales:**

* Usa niveles de registro adecuados para cada tipo de mensaje.
* Personaliza el formato de los mensajes para que sean fáciles de leer y analizar.
* Configura múltiples transportes para enviar los registros a diferentes destinos.
* Considera usar un servicio de gestión de registros centralizado para aplicaciones en producción.
* Para proyectos grandes, considere crear diferentes loggers, para diferentes secciones de su proyecto.

