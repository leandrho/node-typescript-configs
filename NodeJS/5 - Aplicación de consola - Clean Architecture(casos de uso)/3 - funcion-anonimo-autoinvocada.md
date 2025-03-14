# Conceptos de Javascript

## Refactorización
Basicamente es el proceso de cambiar el codigo para mejorar su estructura, legibilidad y mantenibilidad sin cambiar su funcionalidad. Se hace para optimizar el rendimiento, reducir la duplicación y facilitar futuras modificaciones.

## Funciones anónimas autoinvocadas
Las funciones anónimas autoinvocadas en JavaScript, también conocidas como IIFE (Immediately Invoked Function Expression), son funciones que se definen y se ejecutan inmediatamente después de su definición.

## Sintaxis

```javascript
(function() {
  // Código de la función
})();
```
ó
```javascript
(()=> {
  // Código de la función
})();
```

La función se define dentro de paréntesis y se invoca inmediatamente con otro par de paréntesis.

## ¿Para qué sirven?

Las funciones anónimas autoinvocadas tienen varios usos importantes en JavaScript:

### 1. Evitar la contaminación del ámbito global

Las variables declaradas dentro de una IIFE no son accesibles desde fuera de la función, lo que evita que interfieran con otras variables o funciones en el ámbito global. Esto es especialmente útil en proyectos grandes o cuando se utilizan bibliotecas de terceros.

### 2. Crear módulos

Las IIFE se pueden utilizar para crear módulos en JavaScript. Un módulo es un bloque de código que encapsula variables y funciones relacionadas. Los módulos ayudan a organizar el código y a evitar colisiones de nombres.

### 3. Ejecutar código una sola vez

Las IIFE se ejecutan una sola vez, justo después de su definición. Esto puede ser útil para inicializar variables o ejecutar código que solo debe ejecutarse una vez.

## Ejemplos

### Evitar la contaminación del ámbito global

```javascript
(function() {
  var mensaje = "Hola desde la IIFE";
  console.log(mensaje); // "Hola desde la IIFE"
})();

console.log(mensaje); // Error: mensaje no está definido
```

En este ejemplo, la variable `mensaje` está definida dentro de la IIFE, por lo que no es accesible desde fuera de la función.

### Crear módulos

```javascript
var modulo = (function() {
  var nombre = "Mi módulo";

  function saludar() {
    console.log("Hola desde " + nombre);
  }

  return {
    saludar: saludar
  };
})();

modulo.saludar(); // "Hola desde Mi módulo"
```

En este ejemplo, la IIFE crea un módulo que contiene la variable `nombre` y la función `saludar`. El módulo se asigna a la variable `modulo`, que se puede utilizar para acceder a las funciones del módulo.

### Ejecutar código una sola vez

```javascript
var resultado = (function() {
  var fecha = new Date();
  return fecha.getTime();
})();

console.log(resultado); // Un número de tiempo
```

En este ejemplo, la IIFE se utiliza para obtener la fecha actual y almacenarla en la variable `resultado`. La IIFE se ejecuta una sola vez, por lo que la fecha se obtiene una sola vez.

## En resumen

Las funciones anónimas autoinvocadas son una herramienta útil en JavaScript para evitar la contaminación del ámbito global, crear módulos y ejecutar código una sola vez.
