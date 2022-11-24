#### Design Patterns (Universidad de Alberta)

##  Introducción a patrones de diseño: Patrones creacionales y estructurales

### 2.1.1 ¿Que es un patrón de diseño?

Son como las recetas de cocina, algunas personas ya la has realizado previamente y saben que funcionan bien, es por esto, que los demás adoptan esta "receta" porque saben que es la mejor manera de cocinar ese plato.

Hay 23 patrones de diseño, que pueden encontrarse en el libro Design Patterns: Eelements of Reusable Object-Oriented Software (Gamma, Heml, Johnson and Vlissides).

Hay muchos patrones de diseño, y en algunas ocasiones puede parecer que cumple con los requisitos del softwre, pero puede resultarn o ser el mas apropiado.

### 2.1.2 Patrones creacionales, estructurales y de comportamiento

#### Patrones creacionales

Se empecializan en la creación y clonación de objetos.

#### Patrones estrucurales

Crea relaciones entre objetos de diferentes clases.

#### Patrones de comportamiento

Establece como los objetos se distribuyen el trabajo para lograr un objetivo determinado y como cada objeto tiene una sola función cohesiva.

### 2.1.3 Singleton Pattern

Es un **patrón creacional** pues describe la manera en la que se crean los objetos y es uno de los patrones mas simples y poderosos que existen.

- Establece que se debe tener **uno solo un objeto de una clase Singleton**.
  
- Tiene un objeto Singleton que es accesible globalmente.
  
#### ¿Como se logra?

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

### 2.1.4 Factory Method Pattern (AKA Virtual Constructor)

```
El patrón de diseño de fábrica define una interfaz para crear objetos,
pero deja a las subclases decidir que clase se debe instanciar
```

Se orienta a la creación de objetos. En este patrón, la creación de objetos ocurre dentro de las denominadas **fábricas**, lo cual hace al software facil de mantener y cambiar.

Este patron de diseño sugiere que se remplaza **la creación directa de objetos** (usar el operador new) con llamados a el **factory method**. Los objetos que retorna el factory method son llamados usualmente **productos**.

![](/images/road-logistics.png)

Las subclases pueden alterar el tipo de objeto que retorna el factory method modificando unicamente las subclases. Pero esto únicamente es permitido si estas tienen una base en común (o una interfaz). Tambien el factory method en la clase base debe tener el tipo de dato que retorna declarado como esta interfaz.

![](/images/truckandship.png)

Todos los productos deben seguir la misma interfaz.

![Factory method pattern structure](/images/factory-method-pattern.png)
<div align="center">
   <b>Factory method pattern structure</b>
</div>

1) La clase **Product** es una **interfaz**, esta define lo que es común en todos los objetos que serán producidos por el creador y las subclases.
2) ConcreteProductA y ConcreteProductB con las clases concretas y son diferentes implementaciones de la interdaz **Product**.
3) La clase **Creator** declara el factory method que retorna nuevos objetos de la clase productos. Es importante denotar que **el tipo de dato de retorno de este metodo debe coincidir con el tipo de dato de la interfaz**.
Se puede **definir el factory method como abstracto** , para de esta manera obligar a todas las subclases a implemtar su propia versión del factory method.
Cabe resaltar también que la creación de productos no necesariamente es el método principal de la clase creador, usualmente esta tiene mas lógica de negocio central realacionada con los productos, esto ayuda a desacoplar esta lógica de las clases de producto concretar (ConcreteProductA & B).

#### Ejemplo de la vida real

```php
<?php

namespace RefactoringGuru\FactoryMethod\RealWorld;

/**
 * The Creator declares a factory method that can be used as a substitution for
 * the direct constructor calls of products, for instance:
 *
 * - Before: $p = new FacebookConnector();
 * - After: $p = $this->getSocialNetwork;
 *
 * This allows changing the type of the product being created by
 * SocialNetworkPoster's subclasses.
 */
abstract class SocialNetworkPoster
{
    /**
     * The actual factory method. Note that it returns the abstract connector.
     * This lets subclasses return any concrete connectors without breaking the
     * superclass' contract.
     */
    abstract public function getSocialNetwork(): SocialNetworkConnector;

    /**
     * When the factory method is used inside the Creator's business logic, the
     * subclasses may alter the logic indirectly by returning different types of
     * the connector from the factory method.
     */
    public function post($content): void
    {
        // Call the factory method to create a Product object...
        $network = $this->getSocialNetwork();
        // ...then use it as you will.
        $network->logIn();
        $network->createPost($content);
        $network->logout();
    }
}

/**
 * This Concrete Creator supports Facebook. Remember that this class also
 * inherits the 'post' method from the parent class. Concrete Creators are the
 * classes that the Client actually uses.
 */
class FacebookPoster extends SocialNetworkPoster
{
    private $login, $password;

    public function __construct(string $login, string $password)
    {
        $this->login = $login;
        $this->password = $password;
    }

    public function getSocialNetwork(): SocialNetworkConnector
    {
        return new FacebookConnector($this->login, $this->password);
    }
}

/**
 * This Concrete Creator supports LinkedIn.
 */
class LinkedInPoster extends SocialNetworkPoster
{
    private $email, $password;

    public function __construct(string $email, string $password)
    {
        $this->email = $email;
        $this->password = $password;
    }

    public function getSocialNetwork(): SocialNetworkConnector
    {
        return new LinkedInConnector($this->email, $this->password);
    }
}

/**
 * The Product interface declares behaviors of various types of products.
 */
interface SocialNetworkConnector
{
    public function logIn(): void;

    public function logOut(): void;

    public function createPost($content): void;
}

/**
 * This Concrete Product implements the Facebook API.
 */
class FacebookConnector implements SocialNetworkConnector
{
    private $login, $password;

    public function __construct(string $login, string $password)
    {
        $this->login = $login;
        $this->password = $password;
    }

    public function logIn(): void
    {
        echo "Send HTTP API request to log in user $this->login with " .
            "password $this->password\n";
    }

    public function logOut(): void
    {
        echo "Send HTTP API request to log out user $this->login\n";
    }

    public function createPost($content): void
    {
        echo "Send HTTP API requests to create a post in Facebook timeline.\n";
    }
}

/**
 * This Concrete Product implements the LinkedIn API.
 */
class LinkedInConnector implements SocialNetworkConnector
{
    private $email, $password;

    public function __construct(string $email, string $password)
    {
        $this->email = $email;
        $this->password = $password;
    }

    public function logIn(): void
    {
        echo "Send HTTP API request to log in user $this->email with " .
            "password $this->password\n";
    }

    public function logOut(): void
    {
        echo "Send HTTP API request to log out user $this->email\n";
    }

    public function createPost($content): void
    {
        echo "Send HTTP API requests to create a post in LinkedIn timeline.\n";
    }
}

/**
 * The client code can work with any subclass of SocialNetworkPoster since it
 * doesn't depend on concrete classes.
 */
function clientCode(SocialNetworkPoster $creator)
{
    // ...
    $creator->post("Hello world!");
    $creator->post("I had a large hamburger this morning!");
    // ...
}

/**
 * During the initialization phase, the app can decide which social network it
 * wants to work with, create an object of the proper subclass, and pass it to
 * the client code.
 */
echo "Testing ConcreteCreator1:\n";
clientCode(new FacebookPoster("john_smith", "******"));
echo "\n\n";

echo "Testing ConcreteCreator2:\n";
clientCode(new LinkedInPoster("john_smith@example.com", "******"));
```

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

### 2.1.4 Facade Pattern
A pesar de que la logica del negocio pueda ser amplia y compleja, lo cual conllevaría a que partes del código sean tambíen complejas; esto no debe significar que la clase del cliente deba ser igual de complicada, pues se puede volver tedioso trabajar con ella.

- El Patron fachada (Facade pattern) crea una sola y simplificada interza para que la clase del cliente interacture con el subsistema.
- No añade mas funcionalidades, Solo funciona como un punto de entrada en el subsistemas.
- Es una clase Wrapper que encapsula un subsistema para ocultar su complejidad.
- Este puede usarse para envolver todas las interfaces y clases de un subsistemas.

#### ¿Cómo aplicarlo?

1) Diseñar la interfáz

```java
public interface IAccount{
     public void deposit(BigDecimal amout);
     public void withdraw(BigDecimal amout);
     public void transfer(BigDecimal amout);
     public int getAccountNumber();
}

```

2) **Implementar** la interfaz en una o mas clases. Ya que esto permite crear "subtipos", lo que significa que las siguientes clases **son subtipos de IAccount y se espera que se comporten como un tipo de dato IAccount**.

```java
public class Chequing implements IAccount { ... }
public class Saving implements IAccount { ... }
public class Investment implements IAccount { ... }

}
```

3) Crear la clase fachada y atrapar las clases que implementan la interfaz.

``` java
public class BankService {
      private Hashtable<int,IAccount> bankAccounts;
      public BankService() {
           this.bankAccounts = new Hashtable<int, IAccount>;
      } 
      public int createNewAccount (String type, BigDecimal initAmount) {
           IAccount newAccount null;
           switch (type) {
                case "chequing":
                    newAccount = new Chequing (initAmount);
                    break;
                case "saving":
                    newAccount = new Saving( (initAmount);
                    break;
                case
                    "investment":
                    newAccount = new Investment( (initAmount);
                    break;
                default:
                     System.out.println( ("Invalid account type");
                     break;
           }
           if (newAccount != null)
                this.bankAccounts.put(newAccount. getAccountNumber(), newAccount);
                return newAccount.getAccountNumber();
           }
           return -1;
           }

      public void transferMoney (int to, int from, BigDecimal amount){
           IAccount toAccount = this.bankAccounts.get(to);
           IAccount fromAccount = this.bankAccounts.get(from);
           fromAccount.tranfer(toAccount,amount);
     }

```

4) Usar el facade para acceder al subsistema.

``` java
public class Customer {
     public static void main (String args[]) {
          BankService myBankService = new BankService();
          int mySaving = myBankService.createNewAccount ("saving",
               new BigDecimal (500.00));
          int myInvestment = myBankService.createNewAccount(
               "investment", new BigDecimal( (1000.00));
          myBankService.transferMoney (mySaving, myInvestment, new
               BigDecimal( (300.00));

```

#### Resumen

- Su proposito es oculta la complejidad de un subsistmma, mediante un wrapper que unifica todo llamado la clase facade.
- Remove la necesidad de la clase del cliente de manajerar el subsistema, resultando en menos acoplamieno entre los subsistemas y las clases del cliente.
- maneja la inicualización y redireccion de tareas a la clase apropiada del subsistema.
- Se provee al cliente una interdaz simplficiada al subsistema.
- Funciona solo como un punto de entrada al subsistrma y no añade mas funcionalidades al mismo. 