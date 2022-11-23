#### Design Patterns (Universidad de Alberta) 

## Introducción a patrones de diseño: Patrones creacionales y estructurales

### 2.1.1 ¿Que es un patrón de diseño?

Son como las recetas de cocina, algunas personas ya la has realizado previamente y saben que funcionan bien, es por esto, que los demás adoptan esta "receta" porque saben que es la mejor manera de cocinar ese plato.

Hay 23 patrones de diseño, que pueden encontrarse en el libro Design Patterns: Eelements of Reusable Object-Oriented Software (Gamma, Heml, Johnson and Vlissides)

Hay muchos patrones de diseño, y en algunas ocasiones puede parecer que cumple con los requisitos del softwre, pero puede resultarn o ser el mas apropiado.

### 2.1.2 Patrones creacionales, estructurales y de comportamiento.

#### Patrones creacionales

Se empecializan en la creación y clonación de objetos.

#### Patrones estrucurales

Crea relaciones entre objetos de diferentes clases.

#### Patrones de comportamiento

Establece como los objetos se distribuyen el trabajo para lograr un objetivo determinado y como cada objeto tiene una sola función cohesiva.

### 2.1.3 Singleton Pattern

Es un **patrón creacional ** pues describe la manera en la que se crean los objetos y es uno de los patrones mas simples y poderosos que existen.

- Establece que se debe tener **uno solo un objeto de una clase SIngleton **
  
- Tiene un objeto Singleton que es accesible globalmente.
  

#### ¿Como se logra?

A través de un constructor privado. Si este es privado, no se puede acceder al constructor de la clase desde afuera, lo que impide la creación de nuevos objetos de esta clase.

```java
public class ExampleSingIeton { // lazy construction
// the class variable is null i f no instance is
// instantiated
private static ExampIeSingIeton uniquelnstance = null;
private ExampleSingIeton() {
    ...
}
// lazy construction of the instance
public static ExampIeSingleton getlnstance() {
  if (uniquelnstance == null) {
  uniquelnstance = new ExampIeSingleton();
  }
  return uniqueInstance;
}
```
