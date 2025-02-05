
# AAA (Arrange, Act, Assert)

## ¿Qué es el patrón AAA?

El patrón AAA es una guía para estructurar las pruebas unitarias de manera clara y organizada. Las pruebas unitarias son pruebas automatizadas que verifican el correcto funcionamiento de una parte específica del código, como una función o un método. El patrón AAA sugiere dividir cada prueba unitaria en tres secciones principales:

1.  **Arrange (Organizar)**: En esta sección, se configuran los datos y el entorno necesarios para la prueba. Esto puede incluir la creación de objetos, la inicialización de variables y la configuración de mocks (objetos simulados). El objetivo es preparar el escenario para la acción que se va a probar.

2.  **Act (Actuar)**: Aquí se ejecuta la acción que se va a probar, es decir, se llama a la función o método que se quiere verificar. Esta sección debe ser lo más concisa posible y centrarse en la acción en sí.

3.  **Assert (Afirmar)**: En esta sección, se verifican los resultados de la acción realizada en la sección anterior. Se utilizan aserciones (afirmaciones) para comprobar si los resultados obtenidos son los esperados. Las aserciones pueden verificar valores de retorno, cambios en el estado de objetos o cualquier otra condición que se espere que se cumpla.

## Ejemplo en Node.js con TypeScript y Jest

```typescript
// math.ts (archivo con la función a probar)
export function sumar(a: number, b: number): number {
  return a + b;
}

// math.test.ts (archivo con las pruebas unitarias)
import { sumar } from './math';

describe('sumar', () => {
  it('debería sumar dos números correctamente', () => {
    // Arrange (Organizar)
    const numero1 = 5;
    const numero2 = 10;
    const resultadoEsperado = 15;

    // Act (Actuar)
    const resultadoObtenido = sumar(numero1, numero2);

    // Assert (Afirmar)
    expect(resultadoObtenido).toBe(resultadoEsperado);
  });

  it('debería sumar números negativos correctamente', () => {
    // Arrange (Organizar)
    const numero1 = -5;
    const numero2 = -10;
    const resultadoEsperado = -15;

    // Act (Actuar)
    const resultadoObtenido = sumar(numero1, numero2);

    // Assert (Afirmar)
    expect(resultadoObtenido).toBe(resultadoEsperado);
  });
});
```

En este ejemplo, se está probando la función `sumar` que se encuentra en el archivo `math.ts`. El archivo `math.test.ts` contiene las pruebas unitarias.

*   **`describe`**: Se utiliza para agrupar las pruebas relacionadas en un bloque. En este caso, se agrupan las pruebas de la función `sumar`.
*   **`it`**: Define una prueba individual con una descripción clara. Cada prueba sigue el patrón AAA.
*   **`expect`**: Se utiliza para realizar las aserciones. En este caso, se espera que el resultado obtenido sea igual al resultado esperado.
*   **`toBe`**: Es un matcher de Jest que verifica la igualdad estricta entre dos valores.

Este ejemplo ilustra cómo aplicar el patrón AAA en Node.js con TypeScript y Jest para crear pruebas unitarias claras y fáciles de entender. Recuerda que puedes adaptar este ejemplo a tus propias funciones y necesidades de prueba.

## Beneficios del patrón AAA

*   **Legibilidad**: Las pruebas son más fáciles de leer y entender gracias a la separación clara de las secciones.
*   **Mantenibilidad**: Las pruebas son más fáciles de mantener y modificar, ya que cada sección tiene un propósito específico.
*   **Comprensión**: El patrón AAA facilita la comprensión de qué se está probando y cuáles son las expectativas.
*   **Consistencia**: Al seguir un patrón consistente, las pruebas son más uniformes y fáciles de interpretar por otros desarrolladores.

