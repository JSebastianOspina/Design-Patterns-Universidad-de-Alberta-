#### Design Patterns (Universidad de Alberta) 

## Introducción a patrones de diseño: Patrones creacionales y estructurales

### 2.1.1 ¿Que es un patrón de diseño?

Son como las recetas de cocina, algunas personas ya la has realizado previamente y saben que funcionan bien, es por esto, que los demás adoptan esta "receta" porque saben que es la mejor manera de cocinar ese plato.

Hay 23 patrones de diseño, que pueden encontrarse en el libro Design Patterns: Eelements of Reusable Object-Oriented Software (Gamma, Heml, Johnson and Vlissides).

Hay muchos patrones de diseño, y en algunas ocasiones puede parecer que cumple con los requisitos del softwre, pero puede resultarn o ser el mas apropiado.

### 2.1.2 Patrones creacionales, estructurales y de comportamiento.

#### Patrones creacionales

Se empecializan en la creación y clonación de objetos.

#### Patrones estrucurales

Crea relaciones entre objetos de diferentes clases.

#### Patrones de comportamiento

Establece como los objetos se distribuyen el trabajo para lograr un objetivo determinado y como cada objeto tiene una sola función cohesiva.

### 2.1.3 Singleton Pattern

Es un **patrón creacional** pues describe la manera en la que se crean los objetos y es uno de los patrones mas simples y poderosos que existen.

- Establece que se debe tener **uno solo un objeto de una clase Singleton**.
  
- Tiene un objeto Singleton que es accesible globalmente.
  

#### ¿Como se logra?

A través de un constructor privado. Si este es privado, no se puede acceder al constructor de la clase desde afuera, lo que impide la creación de nuevos objetos de esta clase. Se puede hacer uso de "lazy creation", lo que significa que el objeto de esta clase no es creado hasta que de verdad se necesita.

Podría llegar causar problemas para el caso de multiples threads cuando estos intenten acceder simultaneamente a el mismo objeto.

```java
public class ExampleSingIeton { // lazy construction
// the class variable is null i f no instance is
// instantiated
private static ExampIeSingIeton uniquelnstance = null;
private ExampleSingleton() {
    ...
}
// lazy construction of the instance
public static ExampleSingleton getlnstance() {
  if (uniquelnstance == null) {
  uniquelnstance = new ExampleSingleton();
  }
  return uniqueInstance;
}
```

### 2.1.3 Factory Method Pattern (AKA Virtual Constructor)

```
El patrón de diseño de fábrica define una interfaz para crear objetos, 
pero deja a las subclases decidir que clase se debe instanciar
```

Se orienta a la creación de objetos. En este patrón, la creación de objetos ocurre dentro de las denominadas **fábricas**, lo cual hace al software facil de mantener y cambiar.

Este patron de diseño sugiere que se remplaza **la creación directa de objetos** (usar el operador new) con llamados a el **factory method**. Los objetos que retorna el factory method son llamados usualmente **productos**.

![](file://C:\Users\U15280500267\AppData\Roaming\marktext\images\2022-11-23-17-27-04-solution1.png?msec=1669242424796)

Las subclases pueden alterar el tipo de objeto que retorna el factory method modificando unicamente las subclases. Pero esto únicamente es permitido si estas tienen una base en común (o una interfaz). Tambien el factory method en la clase base debe tener el tipo de dato que retorna declarado como esta interfaz.

![](file://C:\Users\U15280500267\AppData\Roaming\marktext\images\2022-11-23-17-30-58-solution2-en.png?msec=1669242658412)

Todos los productos deben seguir la misma interfaz.

![](file://C:\Users\U15280500267\AppData\Roaming\marktext\images\2022-11-23-17-37-01-structure-2x.png?msec=1669243021217)

####  Object creator

La clase de la fábrica es una clase que contiene un método encargado de crear los diferentes tipos de objetos.

```java
public class KnifeFactory {
     public Knife createKnife (String knifeType) {
          Knife knife e = null;
          // create Knife object
          if (knifeType equals ("steak"))
               knife = new SteakKnife();
          } else if (knifeType equals ("chefs"))
               knife = new ChefsKnife();
          }
          return knife;
     }

```

La clase que utiliza la clase de la fábrica, se convierte en un "cliente" de la "fábrica".

```java
public class KnifeStore {
     private KnifeFactory factory;
     // require a KnifeFactory object to be passed to
     // this constructor:
     public KnifeStore (KnifeFactory factory) {
          this. factory = factory;
     }
     public Knife orderKnife (String knifeType) {
          Knife knife;
          // use the create method in the factory
          knife = factory.createKnife(nifeType);
          // prepare the Knife
          knife. sharpen();
          knife.polish();
          knife.package();
          return knife;
 }

```

#### Beneficios de los objetos de fábricas

- Si hay multiples clientes que quieren instanciar las mismas clases, se puede ahorrar todo este código redundante y hacer el software facil de modificar.
  
- El cliente no se preocupa por la logica detras de la creación de las clases, lo cual permite aislar de cierta manera el cliente y este funcionará siempre y cuando la fábrica entregue el objeto.
  
- El cliente no tiene que solicitar clases concretas (en el caso de haber mas clases, por ejemplo cuchillo de chef, de pan, etc), sino que trabaja con un generalización del cuchillo.
  

```
Coding to an interface, not an implementation
```

Sin embargo, aún no es un patrón. Para convertirse en patrón la clase de tienda tiene **subclases** encargadas de la creación de los objetos.

- KnifeStore
  
  - BudgetKnifeStore
    
  - QualityKnifeStore
    

Se debe crear una superclase, que es la que usará la fabrica, y esta debe ser **abstracta**. Al tratarse de una clase abstracta, esta **no se puede instanciar**. Esto hace que el **factory method sea definido en las subclases**.

```java
public abstract class KnifeStore {
     public Knife orderKnife (String knifeType) {
          Knife knife;
          // now creating a knife is a method in the class
          knife = createKnife (knifeType);
          // this is still the same as before!
          knife. sharpen();
          knife.polish();
          knife.package();
          return knife;
     }
     abstract Knife createKnife (String knifeType);
 }

```

Ahora, una subclase se vería de la siguiente manera. Notese el uso de la palabra reservada **extends**.

```java
public BudgetKnifeStore extends KnifeStore {
     // up to any subclass of KnifeStore to define this method
     Knife createKnife(String knifeType) {
          if (knifeType. equals ("steak")) {
               return new BudgetSteakKnife();
          } else if (knifeType equals ("chefs"))
               return new BudgetChefsKnife();
             // more types
          else return null;
 }

```