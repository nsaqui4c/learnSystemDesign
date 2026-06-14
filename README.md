# System Design

## SOLID Principle
| Principle | Goal                     |
| --------- | ------------------------ |
| SRP       | One responsibility       |
| OCP       | Extend without modifying |
| LSP       | Child can replace parent |
| ISP       | Small focused interfaces |
| DIP       | Depend on abstractions   |

* Single responsibility principle -
* Open close principle -> open for extension and close fro modification
  * Express allow us to entend the functionality without changing the code
  ```
  app.use(authMiddleware);
  app.use(loggerMiddleware);
  ```
```
class Express {
  constructor() {
    this.middlewares = [];
  }

  use(middleware) {
    this.middlewares.push(middleware);
  }
}

When you do:

app.use(authMiddleware);
app.use(loggerMiddleware);

Internal state becomes:

middlewares = [
  authMiddleware,
  loggerMiddleware
];


--------------------------

handleRequest(req, res) {
  let index = 0;

  const next = () => {
    const middleware = this.middlewares[index++];

    if (!middleware) {
      return;
    }

    middleware(req, res, next);
  };

  next();
}
```
* Liskov Substitution Principle - If code works with a parent type, it should work correctly with any child type without knowing which child it received.
  * If I replace Parent with Child will my application still behave correctly?
Contract:
```
class Storage {
  save(data) {}
  find(id) {}
}
```
Implementations:
```
class MongoStorage extends Storage {}
class PostgresStorage extends Storage {}
```
Service:
```
class UserService {
  constructor(storage) {
    this.storage = storage;
  }

  create(user) {
    this.storage.save(user);
  }
}
```
Now UserService doesn't care whether it's Mongo or Postgres.
```
new UserService(new MongoStorage());
or
new UserService(new PostgresStorage());
```
Both work. LSP satisfied.

* Interface Segregation Principle - Clients should not depend on methods they don't use.
  * should not create interface with function which classes do need to always use, causing unneccessary creating of the function
* Dependency Inversion Principle - do not create object inside the class, rather get the object as an argument.
  * also can be explain as be loose coupled.


### Coupling vs Cohesion
* Tight couple  - database connection is created inside the class. Had to rewrite the class, if need to add different DB.
```
class OrderService {
  constructor() {
    this.db = new MySQLDatabase();
  }
}
```


* Loose coupling - Db connection is passed as argument. If changed, we can change it in one place and pass the same db everywhere

```
class OrderService {
  constructor(database) {
    this.db = database;
  }
}
```

* Low cohesion - Measures how closely related responsibilities are.
Below we are using utility class with all the utility in one place. This class does not have cohesion as unrelated things are present in here
```
class Utility {
  calculateTax() {}
  sendEmail() {}
  generatePdf() {}
}
```
* High cohesion 
```
class TaxCalculator {}
class EmailService {}
class PdfService {}
```
**What is good design?**
Low coupling
High cohesion

### Composition vs Inheritance
* Inheritance
Represents "IS-A" relationship.
```
class Animal {
  eat() {}
}
```
class Dog extends Animal {}
Dog IS-A Animal.

Problems

Deep inheritance chains become difficult.
```
Animal
  -> Mammal
      -> Dog
          -> PoliceDog
```
Changes ripple everywhere.


* Composition

Represents "HAS-A" relationship.
```
class Engine {
  start() {}
}

class Car {
  constructor() {
    this.engine = new Engine();
  }
}
```
Car HAS-A Engine.
Why Composition Is Preferred

Instead of:
class FlyingCar extends Car {}
Use:
```
class FlyBehavior {
  fly() {}
}

class Car {
  constructor(flyBehavior) {
    this.flyBehavior = flyBehavior;
  }
}
```
Can change behavior dynamically.

**Famous Interview Question**

Inheritance or Composition?

Answer:
```
Favor Composition Over Inheritance

This is one of the core principles behind many design patterns.

Patterns using composition:

Strategy
Decorator
Adapter
Bridge
```

## Design Pattern

| Pattern                 | Type       | Purpose                                        | Real-World Example                 | Pros                        | Cons                       | Use When                            |
| ----------------------- | ---------- | ---------------------------------------------- | ---------------------------------- | --------------------------- | -------------------------- | ----------------------------------- |
| Singleton               | Creational | Ensure only one instance exists                | Logger, Config Manager, DB Pool    | Saves memory, Global access | Hard to test, Global state | Need exactly one shared object      |
| Factory Method          | Creational | Create objects without exposing creation logic | Payment Gateway Factory            | Loose coupling, Extensible  | More classes               | Object type determined at runtime   |
| Abstract Factory        | Creational | Create families of related objects             | Windows/Mac UI Components          | Consistent product families | Complex structure          | Multiple related object groups      |
| Builder                 | Creational | Construct complex objects step-by-step         | Query Builder, Burger Builder      | Readable, Flexible          | Extra code                 | Many optional parameters            |
| Prototype               | Creational | Clone existing objects                         | Game Characters, Templates         | Fast object creation        | Deep copy challenges       | Object creation is expensive        |
| Adapter                 | Structural | Convert one interface into another             | Stripe Adapter, Legacy API Adapter | Reuse existing code         | Additional layer           | Integrating incompatible systems    |
| Facade                  | Structural | Simplify a complex subsystem                   | Order Service Facade               | Easy to use                 | Can become God Object      | Hide subsystem complexity           |
| Proxy                   | Structural | Control access to an object                    | Cache Proxy, API Proxy             | Security, Lazy Loading      | Additional complexity      | Need access control or caching      |
| Decorator               | Structural | Add behavior dynamically                       | Express Middleware, Logging        | Flexible extension          | Many wrapper objects       | Avoid inheritance explosion         |
| Composite               | Structural | Treat individual and grouped objects uniformly | Folder/File Tree                   | Recursive structure support | Hard validation            | Tree-like structures                |
| Bridge                  | Structural | Separate abstraction from implementation       | Remote Control & TV                | Independent evolution       | More abstraction           | Multiple dimensions of change       |
| Flyweight               | Structural | Share common state to save memory              | Text Editor Characters             | Memory efficient            | Complex management         | Large number of similar objects     |
| Observer                | Behavioral | Notify multiple subscribers                    | Kafka, EventEmitter, React Events  | Loose coupling              | Debugging complexity       | Event-driven systems                |
| Strategy                | Behavioral | Switch algorithms dynamically                  | Payment Methods, Sorting           | Open/Closed Principle       | More classes               | Multiple interchangeable algorithms |
| State                   | Behavioral | Change behavior based on state                 | Order Lifecycle                    | Removes large conditionals  | Many state classes         | State-dependent behavior            |
| Command                 | Behavioral | Encapsulate requests as objects                | Queue Jobs, Kafka Commands         | Undo, Queueing              | Many command classes       | Need action abstraction             |
| Chain of Responsibility | Behavioral | Pass request through handlers                  | Express Middleware                 | Flexible processing chain   | Hard debugging             | Multiple processing steps           |
| Template Method         | Behavioral | Define algorithm skeleton                      | Report Generator                   | Reuse common workflow       | Inheritance dependency     | Workflow is fixed but steps vary    |
| Mediator                | Behavioral | Centralize communication                       | Chat Server, Air Traffic Control   | Reduces coupling            | Mediator may become large  | Many objects communicate            |
| Memento                 | Behavioral | Save and restore object state                  | Undo/Redo                          | Easy rollback               | Memory consumption         | Need history snapshots              |
| Iterator                | Behavioral | Traverse collections uniformly                 | Array Iterators                    | Simplifies traversal        | Extra abstraction          | Custom collections                  |
| Visitor                 | Behavioral | Add operations without modifying classes       | AST Processing, Compiler           | Easy extension              | Difficult to understand    | Stable object structure             |
| Interpreter             | Behavioral | Define grammar and evaluate expressions        | SQL Parser, Regex Engine           | Easy grammar extension      | Performance overhead       | Domain-specific languages           |

### Pattern selection cheat sheet
| Problem                                     | Pattern                 |
| ------------------------------------------- | ----------------------- |
| Need only one object                        | Singleton               |
| Need object creation logic hidden           | Factory                 |
| Need complex object construction            | Builder                 |
| Need multiple related object families       | Abstract Factory        |
| Need to clone objects                       | Prototype               |
| Need to integrate incompatible APIs         | Adapter                 |
| Need a simplified API                       | Facade                  |
| Need access control or caching              | Proxy                   |
| Need to add features dynamically            | Decorator               |
| Need tree structures                        | Composite               |
| Need event notifications                    | Observer                |
| Need interchangeable algorithms             | Strategy                |
| Need state-driven behavior                  | State                   |
| Need request queueing/undo                  | Command                 |
| Need request pipeline                       | Chain of Responsibility |
| Need fixed workflow with customizable steps | Template Method         |
| Need centralized communication              | Mediator                |
| Need undo/redo                              | Memento                 |
| Need custom traversal                       | Iterator                |
| Need new operations on stable classes       | Visitor                 |


### Nodejs example of desugn pattern
| Technology                  | Design Pattern          |
| --------------------------- | ----------------------- |
| Express Middleware          | Chain of Responsibility |
| EventEmitter                | Observer                |
| Passport Authentication     | Strategy                |
| Mongoose Models             | Factory                 |
| Winston Logger              | Singleton               |
| Redis Cache Layer           | Proxy                   |
| API Gateway                 | Facade                  |
| BullMQ Jobs                 | Command                 |
| Kafka Consumers             | Observer + Command      |
| Socket.IO Events            | Observer                |
| NestJS Dependency Injection | Factory + Singleton     |
| React Context               | Observer                |
| React Hooks Composition     | Decorator/Composition   |
| Redux Reducers              | State Pattern Concepts  |




| Priority | Pattern                 | Typical Interview Example      |
| -------- | ----------------------- | ------------------------------ |
| ⭐⭐⭐⭐⭐    | Singleton               | Spring Bean Scope, Logger      |
| ⭐⭐⭐⭐⭐    | Factory                 | Payment Gateway Factory        |
| ⭐⭐⭐⭐⭐    | Builder                 | Query Builder, Request Builder |
| ⭐⭐⭐⭐⭐    | Strategy                | Multiple Payment Methods       |
| ⭐⭐⭐⭐⭐    | Observer                | Kafka, EventEmitter            |
| ⭐⭐⭐⭐     | State                   | Order Status Workflow          |
| ⭐⭐⭐⭐     | Command                 | Task Queue, Job Processing     |
| ⭐⭐⭐⭐     | Chain of Responsibility | Express Middleware             |
| ⭐⭐⭐⭐     | Adapter                 | Third-Party Integration        |
| ⭐⭐⭐⭐     | Facade                  | Service Aggregator             |
| ⭐⭐⭐⭐     | Proxy                   | Cache Layer                    |
| ⭐⭐⭐⭐     | Decorator               | Middleware, Logging            |
| ⭐⭐⭐      | Composite               | Menu Tree                      |
| ⭐⭐⭐      | Template Method         | Report Generator               |
| ⭐⭐⭐      | Abstract Factory        | Cross-Platform UI              |



## Creational Design Patterns
### Singleton
### Factory
### Builder Pattern
### Prototype Pattern
### Abstract Factory Pattern



## Structural Design Patterns
### Adapter
### Facade
### Decorator
### Composite
### Proxy



## Behavioral Design Patterns
### Observer
### Strategy
### Command
### State
### Chain of Responsibility
### Template Method
### Mediator
### Memento
### Iterator
### Visitor



### Database
There are two types of DB
* SQL      ->
* No SQL   ->
  * key-value DB
  * Documnet DB (JSON Object)
  * GraphDB -> consist of nodes and edges. Relationship of nodes is shown by edges.

#### CAP Theorem -> We cannot achieve all three in a distributed system
* Consistent                -> if we write in one DB, all DB should have same data.
* Availability              -> Every request should get a response, either error or response, even in case of high load.
* Partition Tolerance       -> should be able to partition i.e distributed system. we have a backup server in case of any fault
  ![image](https://github.com/user-attachments/assets/ce4a330f-7214-47ec-bc14-44f9ca800101)

#### ACID

#### BASE


| SQL | NOSQL |
| :---:  | :---: |
| <span style="color:red">Data Structure</span>| |
| Data is stored in tables with predefined schemas. | Data is stored in various formats such as documents, key-value pairs, wide-column stores, or graphs. |
| Each row must conform to the structure defined by the schema. | Schema is often flexible or schema-less, meaning the structure of data can vary from one record to another. |
| Scalability| |
|Primarily vertically scalable (you scale by increasing the resources of a single machine, like CPU or RAM).|Typically horizontally scalable (you scale by adding more servers or nodes).|
|Some SQL databases (e.g., MySQL, PostgreSQL) support horizontal scaling with sharding, but it's more complex.| Designed to handle large amounts of distributed data. |
| Transactions and ACID Compliance: ||
|Strong support for ACID (Atomicity, Consistency, Isolation, Durability) properties.| Supports BASE (Basically Available, Soft state, Eventual consistency) properties.|
|Ideal for applications requiring reliable transactions (like banking or financial systems).| Transactions are often less strict and optimized for scalability rather than consistency.|
| Performance | |
| Great for structured data and when transactions are critical. May slow down as data grows if not properly optimized. | Optimized for high throughput and large amounts of unstructured or semi-structured data. Generally performs better for read-heavy and write-heavy workloads at scale. |



#### When to Use SQL:
**Structured Data:**

* Your data is structured and can fit into predefined schemas (tables with rows and columns).
* Example: Financial systems, inventory management, and enterprise resource planning (ERP).
  
**ACID Transactions:**

* You need strict consistency, and transactions are crucial.
* Example: Banking systems, order management, and any application where data integrity is critical.
  
**Complex Queries:**

* Your application requires complex joins, aggregations, and subqueries.
* Example: Data analytics platforms, reporting systems.
  
**Long-term Consistency:**

* If consistency is more important than availability, especially in smaller systems where scalability is not the top priority.
* Example: Centralized databases for small to medium-sized applications.
#### When to Use NoSQL:
**Unstructured or Semi-Structured Data:**

* Your data doesn’t fit well into tables (e.g., documents, JSON, multimedia).
* Example: Content management systems, document storage, real-time web apps.
  
**High Scalability:**

* You need to scale horizontally to handle massive amounts of data across distributed systems.
* Example: Big data applications, social media platforms, IoT data storage.
  
**Flexible Schema:**

* Your data structure is constantly evolving, and you need the ability to quickly adjust the schema.
* Example: Startups or fast-growing apps that require agility in development.
  
**Eventual Consistency:**

* If your system can tolerate eventual consistency, NoSQL databases can offer high availability.
* Example: Social media feeds, recommendation engines, real-time analytics, or any application where speed is prioritized over strong consistency.
  
**Summary:**
* Use SQL for structured data, complex transactions, and applications needing ACID compliance.
* Use NoSQL for large-scale, distributed systems with flexible schema, unstructured data, or where high availability and horizontal scalability are more important than strict consistency.



### Sharding
 Database partioning into smaller, faster and more easily manged part.
* Sharding Technique
  * Horizontal partitioning      -> range based partitioning   -> We store data according user ID-> first partition will have user 1-100, then 101-200 and so on.
  * Vertical partitioning        -> feature based partitioning -> DB for userProfile, DB for friend list, DB for images
  * Directory based partitioning -> create a directory server which will have mapping of data and its DB-> first lookup directory to find the DB and then lookup data in that DB
    * most commonly used.
    * We can add more DB easily, as we can update directory server
  * Hashed Based partition       -> we generate a hash function and accordingly decide, in which DB to save data.
     * hash function is tightly coupled with number of DB
     * If DB increase or decrease we need to change function.
   
Problem :
1) Joins and Denormalization are difficult
2) Rebalancing in case on DB is highly utilized, because of incorrect sharding criteria
