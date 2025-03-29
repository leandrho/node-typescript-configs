
Entendiendo hexagonal architecture
---


**1. Lógica de Negocio (Dominio): El Corazón del Sistema**

* **¿Qué es?**
    * Esta es la esencia de tu aplicación. Define las reglas, los procesos y las entidades que representan el "cómo funciona" tu negocio.
    * Aquí se encuentra el código que implementa las reglas de negocio: cálculos, validaciones, flujos de trabajo, etc.
    * Es el "qué" de la aplicación, sin preocuparse por el "cómo" se interactúa con el mundo exterior.
* **Características:**
    * Independiente de tecnologías externas (bases de datos, APIs, interfaces de usuario).
    * Altamente cohesiva y enfocada en el dominio del problema.
    * Fácil de probar y mantener.
* **Ejemplo:**
    * En un sistema de comercio electrónico, la lógica de negocio definiría cómo se calcula el precio de un producto, cómo se aplican los descuentos, cómo se gestionan los pedidos, etc.
* **Nota:**
    * El calculo del precio o descuentos realmente se realiza en esta capa, en la misma entidad o en un servicio, pero siempre dentro de la misma capa.

**2. Lógica de Aplicación (Puertos): La Orquestación**

* **¿Qué es?**
    * Esta lógica actúa como un intermediario entre el dominio y la infraestructura.
    * Define los casos de uso de la aplicación, es decir, las acciones que los usuarios pueden realizar.
    * Coordina la interacción entre las entidades del dominio y los adaptadores de infraestructura.
* **Características:**
    * Depende del dominio, pero no de la infraestructura.
    * Orquesta el flujo de datos y la lógica de negocio para cumplir con los casos de uso.
    * Puede incluir validaciones específicas de la aplicación y transformaciones de datos.
* **Ejemplo:**
    * El caso de uso "realizar un pedido" implicaría coordinar la lógica de negocio para calcular el precio total, la lógica de infraestructura para guardar el pedido en la base de datos y, posiblemente, la lógica de infraestructura para enviar un correo electrónico de confirmación.

**3. Lógica de Infraestructura (Adaptadores): El Mundo Exterior**

* **¿Qué es?**
    * Esta lógica se encarga de la interacción con el mundo exterior.
    * Implementa los adaptadores que permiten que la aplicación se conecte a bases de datos, APIs externas, interfaces de usuario, etc.
    * Traduce los datos entre el formato del dominio y el formato de los sistemas externos.
* **Características:**
    * Depende de las tecnologías externas.
    * Aísla el dominio de los cambios en la infraestructura.
    * Permite cambiar las tecnologías externas sin afectar la lógica de negocio.
* **Ejemplo:**
    * Un adaptador de base de datos se encargaría de guardar y recuperar datos de la base de datos.
    * Un adaptador de API se encargaría de comunicarse con un servicio web externo.
    * Un adaptador de interfaz de usuario se encargaría de mostrar la información al usuario.

**En resumen:**

La arquitectura hexagonal separa estas tres lógicas para crear un sistema más flexible, mantenible y adaptable a los cambios. El dominio se mantiene puro, la aplicación orquesta las interacciones y la infraestructura se encarga de la comunicación con el mundo exterior.
