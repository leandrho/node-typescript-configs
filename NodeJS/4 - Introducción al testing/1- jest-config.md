# Jest en Node.js

## ¿Qué es Jest?

**Jest** es un **framework de testing** de JavaScript desarrollado por Meta. Está diseñado para ser rápido, sencillo y ofrecer una configuración mínima. Es ampliamente utilizado en proyectos frontend y backend gracias a su compatibilidad con TypeScript, su integración con múltiples entornos y su facilidad de uso.

## ¿Por qué es importante en Node.js?

En el contexto de Node.js, Jest es una herramienta clave para garantizar la **calidad y estabilidad del código** en aplicaciones backend. Ofrece varias ventajas:

- ✅ **Pruebas unitarias**: Permite verificar el comportamiento de funciones y módulos individuales.
- ✅ **Pruebas de integración**: Ayuda a probar la comunicación entre distintos componentes, como rutas y controladores en una API.
- ✅ **Mocks y espías**: Facilita la simulación de dependencias externas (bases de datos, servicios de terceros, etc.).
- ✅ **Ejecuta pruebas en paralelo**: Optimiza el rendimiento en entornos de CI/CD.
- ✅ **Genera informes de cobertura de código**, permitiendo identificar áreas sin pruebas.

Gracias a su compatibilidad con TypeScript y su facilidad de uso, Jest es una de las herramientas más populares para testing en entornos Node.js.

---

# Configuración de Jest con TypeScript en Node.js

Esta guía detallada te guiará a través de los pasos para configurar Jest con TypeScript en un proyecto Node.js. Incluye la instalación de dependencias, la configuración de Jest y la creación de scripts para ejecutar las pruebas.

## 1. Instalación de dependencias

Comienza instalando las siguientes dependencias de desarrollo:

```bash
npm install -D jest @types/jest ts-jest supertest
```

*   `jest`: El framework de testing.
*   `@types/jest`: Definiciones de tipos para Jest.
*   `ts-jest`: Preprocesador para usar TypeScript con Jest.
*   `supertest`: (Opcional) Útil para probar aplicaciones Express.

## 2. Inicialización de Jest

Crea el archivo de configuración de Jest ejecutando el siguiente comando:

```bash
npx jest --init
```

Este comando generará un archivo `jest.config.js` (o `jest.config.ts` si usas TypeScript para la configuración) con una configuración básica.

## 3. Configuración de `jest.config.js` (o `jest.config.ts`)

Modifica el archivo de configuración de Jest para que funcione con TypeScript:

```javascript
// jest.config.js (o jest.config.ts)
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node', // o 'jsdom' si pruebas en un entorno de navegador
  // setupFiles: ['dotenv/config'], // Opcional: Carga variables de entorno desde un archivo .env
  // Otras opciones de configuración...
};
```

*   `preset: 'ts-jest'`: Indica a Jest que use el preprocesador `ts-jest` para archivos TypeScript.
*   `testEnvironment: 'node'`: Establece el entorno de prueba a Node.js. Usa `'jsdom'` si estás probando código que se ejecuta en un navegador.
*   `setupFiles`: (Opcional) Un array de rutas a módulos que se ejecutan antes de cada prueba. Útil para configurar el entorno de pruebas, como cargar variables de entorno desde un archivo `.env` usando `dotenv`.

## 4. Scripts en `package.json`

Agrega los siguientes scripts al archivo `package.json` para ejecutar las pruebas:

```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  }
}
```

*   `test`: Ejecuta las pruebas una vez.
*   `test:watch`: Ejecuta las pruebas en modo "watch", volviéndolas a ejecutar automáticamente cuando se detectan cambios en los archivos.
*   `test:coverage`: Ejecuta las pruebas y genera un informe de cobertura de código.

## Ejemplo de prueba (con TypeScript)

Crea un archivo llamado `sum.ts` (o `sum.js` si no usas TypeScript):

```typescript
// sum.ts
export function sum(a: number, b: number): number {
  return a + b;
}
```

Y un archivo de prueba llamado `sum.test.ts` (o `sum.test.js`):

```typescript
// sum.test.ts
import { sum } from './sum';

describe('sum', () => {
  it('should return the sum of two numbers', () => {
    expect(sum(1, 2)).toBe(3);
  });
});
```

Ahora puedes ejecutar las pruebas con `npm test` o `npm test:watch`.

## Consideraciones adicionales

*   **`tsconfig.json`:** Asegúrate de tener un archivo `tsconfig.json` correctamente configurado para TypeScript.
*   **Otras configuraciones de Jest:** Jest ofrece muchas opciones de configuración. Consulta la [documentación oficial](https://jestjs.io/docs/configuration) para obtener más detalles.
*   **Cobertura de código:** El script `test:coverage` generará un informe de cobertura. Puedes configurar el umbral de cobertura en `jest.config.js`.
