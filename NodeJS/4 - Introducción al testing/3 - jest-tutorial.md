## Tutorial de Jest

**Jest** es un framework de pruebas de JavaScript ampliamente utilizado y mantenido por Facebook. Su popularidad se debe a su facilidad de uso, su rica funcionalidad y su integración perfecta con proyectos de React, aunque funciona con cualquier proyecto de JavaScript o Typescript. Jest nos proporciona las herramientas necesarias para escribir, ejecutar y depurar pruebas de manera eficiente.

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

Los **mocks** son una herramienta, que nos permiten simular el comportamiento de dependencias externas o componentes complejos para aislar la unidad que estamos probando.
En esencia, un mock es un objeto o función que reemplaza a una dependencia real durante las pruebas. Esta dependencia puede ser una función, un módulo, una clase o incluso un servicio externo. El mock imita el comportamiento de la dependencia real, permitiéndonos controlar su salida y observar cómo interactúa con la unidad bajo prueba.

### ¿Para qué sirven los mocks?

Los mocks son útiles en una variedad de situaciones, incluyendo:

* **Aislamiento de la unidad bajo prueba:** Al simular las dependencias, nos aseguramos de que la prueba se centre exclusivamente en la lógica de la unidad que estamos probando, sin verse afectada por el comportamiento de otros componentes.
* **Simulación de comportamientos complejos:** Podemos usar mocks para simular escenarios complejos o casos límite que serían difíciles de reproducir en un entorno real.
* **Control del flujo de datos:** Los mocks nos permiten definir las entradas y salidas de las dependencias, lo que nos da un control total sobre el flujo de datos durante la prueba.
* **Observación de interacciones:** Podemos usar mocks para registrar las llamadas a las dependencias y verificar si se llaman con los argumentos correctos y el número de veces esperado.

### ¿Cómo se usan los mocks en Jest con TypeScript y Node.js?

Jest ofrece varias formas de crear mocks, adaptándose a diferentes necesidades. A continuación, te presento algunas de las técnicas más comunes:

#### 1. Funciones Mock

Podemos crear funciones mock utilizando `jest.fn()`. Estas funciones pueden ser configuradas para retornar valores específicos, lanzar errores o simular cualquier otro comportamiento que necesitemos.

```typescript
// Ejemplo: Mock de una función que calcula el área de un círculo
const calcularArea = jest.fn((radio: number) => Math.PI * radio * radio);

// Llamamos a la función mock
calcularArea(5);

// Verificamos que la función fue llamada con el argumento correcto
expect(calcularArea).toHaveBeenCalledWith(5);

// Verificamos que la función retornó el valor esperado
expect(calcularArea(5)).toBe(Math.PI * 5 * 5);
```

#### 2. Mocks de módulos

Podemos simular módulos completos utilizando `jest.mock()`. Esto nos permite reemplazar un módulo real con un mock que definamos.

```typescript
// Ejemplo: Mock del módulo 'fs' para simular la lectura de un archivo
jest.mock('fs', () => ({
  readFileSync: jest.fn(() => 'Contenido del archivo mock'),
}));

import * as fs from 'fs';

// Llamamos a la función del módulo mockeado
const contenido = fs.readFileSync('archivo.txt', 'utf8');

// Verificamos que la función fue llamada con los argumentos correctos
expect(fs.readFileSync).toHaveBeenCalledWith('archivo.txt', 'utf8');

// Verificamos que la función retornó el valor esperado
expect(contenido).toBe('Contenido del archivo mock');
```

#### 3. Mocks de clases

También podemos simular clases utilizando `jest.mock()`. Esto nos permite reemplazar una clase real con un mock que definamos.

```typescript
// Ejemplo: Mock de la clase 'Logger' para simular el registro de mensajes
jest.mock('./logger', () => {
  return jest.fn(() => ({
    log: jest.fn(),
  }));
});

import { Logger } from './logger';

// Obtenemos una instancia de la clase mockeada
const logger = new Logger();

// Llamamos a un método de la clase mockeada
logger.log('Mensaje de prueba');

// Verificamos que el método fue llamado con el argumento correcto
expect(logger.log).toHaveBeenCalledWith('Mensaje de prueba');
```

### Conclusión

Los mocks son una herramienta poderosa que nos permite realizar pruebas unitarias de manera efectiva. Al simular dependencias y controlar el flujo de datos, podemos aislar la unidad bajo prueba y garantizar que nuestro código funcione correctamente. Jest ofrece varias formas de crear mocks, adaptándose a diferentes necesidades y escenarios.

---

### Done

**Done()**

En esencia, `done()` es una función callback que se utiliza en pruebas asíncronas dentro de Jest. Su propósito principal es indicarle a Jest que una prueba ha finalizado, especialmente cuando se trata de código asíncrono que no se resuelve de inmediato.

**¿Cómo funciona `done()`?**

Cuando trabajas con código asíncrono, como llamadas a APIs, timers o eventos, las pruebas pueden completarse antes de que el código asíncrono haya terminado de ejecutarse. Aquí es donde `done()` se vuelve crucial.

1.  **Definición de la prueba:**

    Dentro de una prueba (`it`), se proporciona un argumento a la función de prueba. Este argumento es la función `done()`.

    ```javascript
    it('debería obtener datos de la API', (done) => {
      // Código asíncrono aquí
    });
    ```

2.  **Llamada a `done()`:**

    Dentro del código asíncrono, una vez que la operación asíncrona ha finalizado y se han realizado las aserciones necesarias, se llama a la función `done()`. Esto le indica a Jest que la prueba ha terminado.

    ```javascript
    it('debería obtener datos de la API', (done) => {
      obtenerDatosDeLaAPI((datos) => {
        expect(datos).toBeDefined();
        done(); // Indica que la prueba ha terminado
      });
    });
    ```

**¿Para qué sirve `done()`?**

*   **Control del tiempo de finalización de la prueba:**

    `done()` permite controlar cuándo se considera que una prueba ha terminado, lo cual es esencial para pruebas asíncronas donde la operación puede tomar un tiempo indeterminado.

*   **Pruebas de callbacks:**

    Aunque las promesas y `async/await` son más comunes ahora, `done()` sigue siendo útil para probar código que utiliza callbacks.

*   **Manejo de errores en pruebas asíncronas:**

    Puedes pasar un error a `done()` (`done(error)`) para indicar que la prueba ha fallado debido a un error en el código asíncrono.

**Ejemplo práctico**

Imagina que tienes una función que simula una llamada a una API:

```javascript
function obtenerDatosDeLaAPI(callback) {
  setTimeout(() => {
    const datos = { nombre: 'Juan', edad: 30 };
    callback(datos);
  }, 1000); // Simula una llamada a la API que tarda 1 segundo
}
```

Para probar esta función con Jest y `done()`, podrías escribir la siguiente prueba:

```javascript
it('debería obtener datos de la API', (done) => {
  obtenerDatosDeLaAPI((datos) => {
    expect(datos).toBeDefined();
    expect(datos.nombre).toBe('Juan');
    expect(datos.edad).toBe(30);
    done(); // Indica que la prueba ha terminado
  });
});
```

**Consideraciones importantes**

*   **Evita usar `done()` con promesas y `async/await`:**

    Si utilizas promesas o `async/await`, Jest maneja automáticamente la asincronía, por lo que no necesitas `done()`. De hecho, usar `done()` con promesas puede llevar a errores.

*   **Llamar a `done()` una sola vez:**

    Asegúrate de llamar a `done()` una sola vez dentro de la prueba. Llamarlo varias veces puede causar comportamientos inesperados.

*   **Manejo de errores:**

    Es importante manejar los errores dentro del código asíncrono y pasarlos a `done()` en caso de que ocurran, para que Jest pueda marcar la prueba como fallida.

En resumen, `done()` es una herramienta útil para pruebas asíncronas en Jest, especialmente cuando se trabaja con callbacks. Sin embargo, es importante entender cuándo y cómo usarlo correctamente para evitar errores y asegurar que tus pruebas sean confiables.

---

### spyOn

En esencia, `spyOn` es una función poderosa dentro de Jest que nos permite "espiar" el comportamiento de una función específica. Su propósito principal es monitorear si una función ha sido llamada, cuántas veces ha sido llamada y con qué argumentos ha sido llamada.

**¿Cómo funciona `spyOn`?**

1.  **Creación del espía:**

    Utilizamos `jest.spyOn()` para crear un "espía" sobre una función existente. Esta función puede ser un método de un objeto, una función independiente o incluso una función importada de un módulo.

    ```javascript
    const objeto = {
      miFuncion: () => {
        // ...
      },
    };

    const espia = jest.spyOn(objeto, 'miFuncion');
    ```

2.  **Llamada a la función espiada:**

    Cuando se llama a la función original (en este caso, `objeto.miFuncion`), el espía registra esta llamada.

3.  **Aserciones sobre el espía:**

    Podemos utilizar el espía para realizar aserciones sobre cómo se ha comportado la función espiada. Por ejemplo, podemos verificar si ha sido llamada, cuántas veces ha sido llamada y con qué argumentos ha sido llamada.

    ```javascript
    expect(espia).toHaveBeenCalled(); // Verifica si la función fue llamada
    expect(espia).toHaveBeenCalledTimes(2); // Verifica si la función fue llamada 2 veces
    expect(espia).toHaveBeenCalledWith('argumento1', 'argumento2'); // Verifica si la función fue llamada con argumentos específicos
    ```

**¿Para qué sirve `spyOn`?**

*   **Monitoreo del comportamiento de funciones:**

    `spyOn` nos permite verificar si una función se está comportando como esperamos en diferentes escenarios. Esto es especialmente útil para probar interacciones entre diferentes partes de nuestro código.

*   **Aislamiento de dependencias:**

    Podemos utilizar `spyOn` para "espiar" las llamadas a funciones dependientes dentro de una unidad de código que estamos probando. Esto nos permite aislar la unidad bajo prueba y simular el comportamiento de las dependencias.

*   **Pruebas de efectos secundarios:**

    `spyOn` nos permite verificar si una función está produciendo los efectos secundarios esperados. Por ejemplo, podemos verificar si una función está modificando el estado de un objeto o realizando una llamada a una API externa.

**Ejemplo práctico**

Imagina que tienes un componente React que realiza una llamada a una API cuando se monta:

```javascript
class MiComponente extends React.Component {
  componentDidMount() {
    fetch('/api/datos')
      .then(respuesta => respuesta.json())
      .then(datos => {
        // ...
      });
  }

  // ...
}
```

Para probar este componente con Jest y `spyOn`, podrías escribir la siguiente prueba:

```javascript
it('debería llamar a la API al montarse', () => {
  global.fetch = jest.fn(() =>
    Promise.resolve({
      json: () => Promise.resolve({}),
    })
  );

  mount(<MiComponente />);

  expect(global.fetch).toHaveBeenCalledTimes(1);
  expect(global.fetch).toHaveBeenCalledWith('/api/datos');
});
```

En este ejemplo, utilizamos `jest.spyOn` para crear un espía sobre la función `fetch`. Luego, montamos el componente y verificamos si la función `fetch` fue llamada una vez y con el argumento correcto.

**Consideraciones importantes**

*   **Restauración del espía:**

    Es importante restaurar el espía después de cada prueba para evitar efectos secundarios en otras pruebas. Puedes utilizar `espia.mockRestore()` para restaurar la función original.

*   **Uso con mocks:**

    `spyOn` se puede combinar con mocks para simular el comportamiento de una función y al mismo tiempo monitorear su comportamiento.

En resumen, `spyOn` es una herramienta poderosa en Jest que nos permite "espiar" el comportamiento de funciones y realizar aserciones sobre ellas. Esto nos ayuda a escribir pruebas más completas y confiables.

---

### expect.any()

`expect.any()` toma un constructor como argumento (por ejemplo, `String`, `Number`, `Boolean`, `Function`, `Object`, `Array`, `Symbol`) y verifica si el valor que se está probando fue creado con ese constructor.

**¿Para qué sirve `expect.any()`?**

`expect.any()` es útil en situaciones donde:

*   **No conoces el valor exacto:**

    Estás probando una función que genera un valor dinámico (como un ID único o una fecha), y solo te importa verificar que sea del tipo correcto.

*   **Te importa el tipo de dato, no el valor:**

    Estás probando una función que recibe un argumento y solo necesitas asegurarte de que sea de un tipo específico (por ejemplo, un número o una cadena).

*   **Estás probando objetos con propiedades de tipos variados:**

    Quieres verificar que un objeto tenga propiedades de ciertos tipos, pero no te preocupan los valores específicos de esas propiedades.

**Ejemplos de uso**

1.  **Verificar si un valor es un número:**

    ```javascript
    expect(miFuncion()).toEqual(expect.any(Number));
    ```

2.  **Verificar si un valor es una cadena:**

    ```javascript
    expect(miVariable).toEqual(expect.any(String));
    ```

3.  **Verificar si un valor es un objeto:**

    ```javascript
    expect(miObjeto).toEqual(expect.any(Object));
    ```

4.  **Verificar si un array contiene elementos de cierto tipo:**

    ```javascript
    expect([1, 'hola', true]).toEqual([expect.any(Number), expect.any(String), expect.any(Boolean)]);
    ```

5.  **Verificar si un objeto tiene propiedades de ciertos tipos:**

    ```javascript
    const miObjeto = {
      id: 123,
      nombre: 'Juan',
      activo: true,
    };

    expect(miObjeto).toEqual({
      id: expect.any(Number),
      nombre: expect.any(String),
      activo: expect.any(Boolean),
    });
    ```

**Consideraciones importantes**

*   `expect.any()` solo verifica el tipo de dato, no el valor en sí.
*   Puedes combinar `expect.any()` con otros matchers para realizar aserciones más específicas.
*   Recuerda que `expect.any()` solo verifica si el valor fue *creado* con el constructor especificado. Esto significa que, por ejemplo, `expect.any(Number)` coincidirá con cualquier número, pero no con una cadena que se pueda convertir a número.

En resumen, `expect.any()` es un matcher útil en Jest para verificar tipos de datos sin necesidad de conocer los valores exactos. Esto te permite escribir pruebas más flexibles y robustas, especialmente cuando trabajas con datos dinámicos o estructuras de datos complejas.

---

### expect.objectContaining

`expect.objectContaining` es un matcher asimétrico en Jest que te permite verificar si un objeto contiene un conjunto específico de propiedades, sin necesidad de que coincida con todas las propiedades del objeto.

**¿Cómo funciona `expect.objectContaining`?**

`expect.objectContaining` toma un objeto como argumento, donde las claves son las propiedades que quieres verificar y los valores son los valores esperados de esas propiedades. Este matcher verifica si el objeto que se está probando contiene al menos las propiedades especificadas con los valores correspondientes.

**¿Para qué sirve `objectContaining`?**

`expect.objectContaining` es útil en situaciones donde:

*   **Solo te importan algunas propiedades del objeto:**

    Estás probando una función que devuelve un objeto con muchas propiedades, pero solo necesitas verificar que contenga algunas específicas.

*   **Estás probando la integración con un componente que recibe props:**

    Quieres asegurarte de que el componente recibe las props correctas, pero no te preocupan otras props que pueda tener.

*   **Estás trabajando con objetos que pueden tener propiedades adicionales:**

    Quieres verificar que un objeto tenga ciertas propiedades, pero no te importa si tiene propiedades adicionales que no estás probando.

**Ejemplos de uso**

1.  **Verificar si un objeto contiene ciertas propiedades:**

    ```javascript
    const miObjeto = {
      id: 123,
      nombre: 'Juan',
      edad: 30,
    };

    expect(miObjeto).toEqual(expect.objectContaining({
      nombre: 'Juan',
      edad: 30,
    }));
    ```

2.  **Verificar si un objeto contiene propiedades de ciertos tipos:**

    ```javascript
    const miObjeto = {
      id: 123,
      nombre: 'Juan',
      activo: true,
    };

    expect(miObjeto).toEqual(expect.objectContaining({
      id: expect.any(Number),
      nombre: expect.any(String),
    }));
    ```

3.  **Verificar si un objeto contiene propiedades anidadas:**

    ```javascript
    const miObjeto = {
      persona: {
        nombre: 'Juan',
        edad: 30,
      },
    };

    expect(miObjeto).toEqual(expect.objectContaining({
      persona: expect.objectContaining({
        nombre: 'Juan',
      }),
    }));
    ```

**Consideraciones importantes**

*   `expect.objectContaining` solo verifica si el objeto contiene las propiedades especificadas con los valores correspondientes. No verifica si el objeto tiene propiedades adicionales.
*   Puedes combinar `expect.objectContaining` con otros matchers para realizar aserciones más específicas sobre los valores de las propiedades.

En resumen, `expect.objectContaining` es un matcher útil en Jest para verificar si un objeto contiene un conjunto específico de propiedades, sin necesidad de que coincida con todas las propiedades del objeto. Esto te permite escribir pruebas más flexibles y robustas, especialmente cuando trabajas con objetos complejos o datos dinámicos.

---

### Otras características útiles

*   **Cobertura de código**: Jest puede generar informes de cobertura de código para mostrar qué partes de tu código están cubiertas por las pruebas.
*   **Modo de vigilancia (`--watch`)**: Jest puede ejecutarse en modo de vigilancia, lo que significa que vuelve a ejecutar las pruebas automáticamente cada vez que se guardan cambios en los archivos.
*   **Depuración**: Puedes depurar tus pruebas utilizando herramientas como el depurador de Node.js o extensiones de depuración en tu editor de código.

### Recursos adicionales

*   Documentación de Jest: [https://jestjs.io/](https://jestjs.io/)
*   Ejemplos de pruebas con Jest: [https://testing-library.com/docs/react-testing-library/intro/](https://testing-library.com/docs/react-testing-library/intro/)
