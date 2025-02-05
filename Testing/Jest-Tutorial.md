## ¿Qué es Jest?

Jest es un framework de pruebas de JavaScript ampliamente utilizado y mantenido por Facebook. Su popularidad se debe a su facilidad de uso, su rica funcionalidad y su integración perfecta con proyectos de React, aunque funciona con cualquier proyecto de JavaScript o Typescript. Jest nos proporciona las herramientas necesarias para escribir, ejecutar y depurar pruebas de manera eficiente.

### Estructura básica de una prueba con Jest

1.  **`describe(name, fn)`:**

*   Agrupa un conjunto de pruebas relacionadas bajo un nombre descriptivo.
*   Ayuda a organizar las pruebas y a entender qué se está probando.

    ```javascript
    describe('MiComponente', () => {
      // Pruebas aquí
    });
    ```

2.  **`it(name, fn)`:**

*   Define una prueba individual dentro de un bloque `describe`.
*   Describe el comportamiento específico que se está probando.

    ```javascript
    it('debería renderizar el título correctamente', () => {
      // Aserciones aquí
    });
    ```

3.  **`expect(value)`:**

*   Crea una aserción sobre un valor.
*   Se combina con "matchers" para realizar comprobaciones específicas.

    ```javascript
    expect(miVariable).toBe(5);
    ```

4.  **Matchers:**

*   Son funciones que se utilizan con `expect` para realizar diferentes tipos de aserciones.
*   Jest ofrece una amplia gama de matchers para cubrir diversas situaciones.

    *   **Igualdad**:*
        *   `toBe(value)`: Igualdad estricta (`===`).
        *   `toEqual(value)`: Igualdad profunda (compara propiedades de objetos).
    *   **Valores**:*
        *   `toBeNull()`: Comprueba si un valor es `null`.
        *   `toBeUndefined()`: Comprueba si un valor es `undefined`.
        *   `toBeDefined()`: Comprueba si un valor no es `undefined`.
        *   `toBeTruthy()`: Comprueba si un valor es "verdadero" en un contexto booleano.
        *   `toBeFalsy()`: Comprueba si un valor es "falso" en un contexto booleano.
    *   **Números**:*
        *   `toBeGreaterThan(number)`: Mayor que.
        *   `toBeGreaterThanOrEqual(number)`: Mayor o igual que.
        *   `toBeLessThan(number)`: Menor que.
        *   `toBeLessThanOrEqual(number)`: Menor o igual que.
        *   `toBeCloseTo(number, numDigits?)`: Compara números decimales con cierta precisión.
    *   **Cadenas**:*
        *   `toContain(string)`: Comprueba si una cadena contiene otra.
        *   `toMatch(regexp)`: Comprueba si una cadena coincide con una expresión regular.
    *   **Arrays**:*
        *   `toContain(item)`: Comprueba si un array contiene un elemento específico.
        *   `toHaveLength(number)`: Comprueba la longitud de un array.
    *   **Objetos**:*
        *   `toHaveProperty(key, value?)`: Comprueba si un objeto tiene una propiedad específica (y opcionalmente, un valor).
    *   **Funciones**:*
        *   `toHaveBeenCalled()`: Comprueba si se llamó a una función.
        *   `toHaveBeenCalledTimes(number)`: Comprueba cuántas veces se llamó a una función.
        *   `toHaveBeenCalledWith(...args)`: Comprueba si se llamó a una función con argumentos específicos.
    *   **Excepciones**:*
        *   `toThrow(error?)`: Comprueba si una función lanza una excepción (y opcionalmente, un error específico).
        *   `toThrowError(error?)`: Similar a `toThrow`, pero se centra en el mensaje de error.

### Ciclo de vida de las pruebas

Jest nos permite controlar el ciclo de vida de las pruebas con los siguientes hooks:

*   **`beforeEach(fn)`:** Se ejecuta antes de cada prueba en un bloque `describe`.
*   **`afterEach(fn)`:** Se ejecuta después de cada prueba en un bloque `describe`.
*   **`beforeAll(fn)`:** Se ejecuta una vez antes de todas las pruebas en un bloque `describe`.
*   **`afterAll(fn)`:** Se ejecuta una vez después de todas las pruebas en un bloque `describe`.

Estos hooks son útiles para preparar el entorno de prueba (por ejemplo, inicializar variables, crear mocks) y limpiarlo después de cada prueba o de todo el conjunto de pruebas.

### Pruebas asíncronas

Jest facilita la prueba de código asíncrono (promesas, llamadas a APIs, etc.) utilizando `async/await` o promesas.

```javascript
it('debería obtener datos de la API', async () => {
  const datos = await obtenerDatosDeLaAPI();
  expect(datos).toBeDefined();
});
```

### Mocks

Los mocks son objetos simulados que se utilizan para aislar la unidad que se está probando de sus dependencias. Jest facilita la creación de mocks para funciones, clases y módulos.

```javascript
const miFuncionMock = jest.fn();
miFuncionMock.mockReturnValue(10);

miFuncionMock(); // Llama al mock
expect(miFuncionMock).toHaveBeenCalled(); // Verifica si se llamó al mock
expect(miFuncionMock()).toBe(10); // Verifica el valor de retorno del mock
```

### Otras características útiles

*   **Cobertura de código**: Jest puede generar informes de cobertura de código para mostrar qué partes de tu código están cubiertas por las pruebas.
*   **Modo de vigilancia (`--watch`)**: Jest puede ejecutarse en modo de vigilancia, lo que significa que vuelve a ejecutar las pruebas automáticamente cada vez que se guardan cambios en los archivos.
*   **Depuración**: Puedes depurar tus pruebas utilizando herramientas como el depurador de Node.js o extensiones de depuración en tu editor de código.

### Recursos adicionales

*   Documentación de Jest: [https://jestjs.io/](https://jestjs.io/)
*   Ejemplos de pruebas con Jest: [https://testing-library.com/docs/react-testing-library/intro/](https://testing-library.com/docs/react-testing-library/intro/)
