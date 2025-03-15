# design_principles

## Dependency Injection 

Here’s a summary of the article **[Flutter Dependency Injection: A Beginner's Guide](https://www.filledstacks.com/post/flutter-dependency-injection-a-beginners-guide/)** by FilledStacks, using the same method with explanations and **code examples**:

---

### **Flutter Dependency Injection (DI)**
Dependency Injection (DI) is a design pattern that allows you to decouple your code by injecting dependencies (e.g., services, repositories) into classes rather than hardcoding them. This makes your code more modular, testable, and maintainable.

---

### **1. Why Use Dependency Injection?**
- **Testability**: Easily mock dependencies for unit testing.
- **Reusability**: Share services across multiple widgets or classes.
- **Maintainability**: Change implementations without modifying dependent classes.

---

### **2. Manual Dependency Injection**
You can manually inject dependencies by passing them through constructors.

#### Example:
```dart
class UserService {
  void fetchUser() {
    print('Fetching user...');
  }
}

class HomeViewModel {
  final UserService userService;

  HomeViewModel({required this.userService});

  void loadUser() {
    userService.fetchUser();
  }
}

void main() {
  final userService = UserService();
  final viewModel = HomeViewModel(userService: userService);
  viewModel.loadUser();
}
```

---

### **3. Using a Service Locator**
A service locator is a central registry for managing dependencies. The `get_it` package is a popular choice for implementing a service locator in Flutter.

#### Example:
1. Add `get_it` to your `pubspec.yaml`:
   ```yaml
   dependencies:
     get_it: ^7.2.0
   ```

2. Set up the service locator:
   ```dart
   import 'package:get_it/get_it.dart';

   final getIt = GetIt.instance;

   void setupLocator() {
     getIt.registerSingleton<UserService>(UserService());
   }
   ```

3. Use the service locator in your app:
   ```dart
   void main() {
     setupLocator();
     final userService = getIt<UserService>();
     userService.fetchUser();
   }
   ```

---

### **4. Injecting Dependencies into Widgets**
You can use the service locator to inject dependencies into widgets.

#### Example:
```dart
class HomeView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final userService = getIt<UserService>();
    return Scaffold(
      appBar: AppBar(title: Text('Home')),
      body: Center(
        child: ElevatedButton(
          onPressed: userService.fetchUser,
          child: Text('Fetch User'),
        ),
      ),
    );
  }
}
```

---

### **5. Using Dependency Injection with State Management**
You can combine DI with state management solutions like `Provider` or `Riverpod`.

#### Example with `Provider`:
1. Add `provider` to your `pubspec.yaml`:
   ```yaml
   dependencies:
     provider: ^6.0.0
   ```

2. Set up the provider:
   ```dart
   void main() {
     setupLocator();
     runApp(
       MultiProvider(
         providers: [
           Provider<UserService>(create: (_) => getIt<UserService>()),
         ],
         child: MyApp(),
       ),
     );
   }
   ```

3. Use the provider in your widget:
   ```dart
   class HomeView extends StatelessWidget {
     @override
     Widget build(BuildContext context) {
       final userService = Provider.of<UserService>(context);
       return Scaffold(
         appBar: AppBar(title: Text('Home')),
         body: Center(
           child: ElevatedButton(
             onPressed: userService.fetchUser,
             child: Text('Fetch User'),
           ),
         ),
       );
     }
   }
   ```

---

### **6. Combining Properties**
Here’s an example combining manual DI and `get_it`:

#### Example:
```dart
void main() {
  setupLocator();
  final userService = getIt<UserService>();
  final viewModel = HomeViewModel(userService: userService);
  viewModel.loadUser();
}
```

---

### **Summary of Dependency Injection**
| Method                | Description                              | Example                                   |
|-----------------------|------------------------------------------|-------------------------------------------|
| Manual DI             | Pass dependencies via constructors       | `HomeViewModel(userService: userService)` |
| Service Locator (`get_it`) | Central registry for dependencies   | `getIt.registerSingleton<UserService>()`  |
| Provider              | Combine DI with state management         | `Provider<UserService>(create: (_) => ...)` |

---

### **Example: Full Usage**
Here’s a complete example of using `get_it` for dependency injection in a Flutter app:
```dart
import 'package:flutter/material.dart';
import 'package:get_it/get_it.dart';

final getIt = GetIt.instance;

void setupLocator() {
  getIt.registerSingleton<UserService>(UserService());
}

class UserService {
  void fetchUser() {
    print('Fetching user...');
  }
}

class HomeViewModel {
  final UserService userService;

  HomeViewModel({required this.userService});

  void loadUser() {
    userService.fetchUser();
  }
}

class HomeView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final userService = getIt<UserService>();
    return Scaffold(
      appBar: AppBar(title: Text('Home')),
      body: Center(
        child: ElevatedButton(
          onPressed: userService.fetchUser,
          child: Text('Fetch User'),
        ),
      ),
    );
  }
}

void main() {
  setupLocator();
  runApp(MaterialApp(home: HomeView()));
}
```

---

## SOLID Principiles 

Here’s a summary of the article **[S.O.L.I.D Principles in Dart](https://medium.flutterdevs.com/s-o-l-i-d-principles-in-dart-e6c0c8d1f8f1)** by FlutterDevs, using the same method with explanations and **code examples**:

---

### **S.O.L.I.D Principles in Dart**
S.O.L.I.D is an acronym for five design principles that help developers write clean, maintainable, and scalable code. These principles are:
1. **S**ingle Responsibility Principle (SRP)
2. **O**pen/Closed Principle (OCP)
3. **L**iskov Substitution Principle (LSP)
4. **I**nterface Segregation Principle (ISP)
5. **D**ependency Inversion Principle (DIP)

---

### **1. Single Responsibility Principle (SRP)**
A class should have only one reason to change, meaning it should have only one responsibility.

#### Example:
```dart
// Bad: A class with multiple responsibilities
class User {
  void saveUser() {
    // Save user to database
  }

  void sendEmail() {
    // Send email to user
  }
}

// Good: Separate responsibilities into different classes
class UserRepository {
  void saveUser() {
    // Save user to database
  }
}

class EmailService {
  void sendEmail() {
    // Send email to user
  }
}
```

---

### **2. Open/Closed Principle (OCP)**
Software entities (classes, modules, functions) should be open for extension but closed for modification.

#### Example:
```dart
// Bad: Modifying existing class to add new functionality
class Rectangle {
  double width;
  double height;

  Rectangle(this.width, this.height);
}

class AreaCalculator {
  double calculateArea(Rectangle rectangle) {
    return rectangle.width * rectangle.height;
  }
}

// Good: Extending functionality without modifying existing code
abstract class Shape {
  double calculateArea();
}

class Rectangle extends Shape {
  double width;
  double height;

  Rectangle(this.width, this.height);

  @override
  double calculateArea() {
    return width * height;
  }
}

class Circle extends Shape {
  double radius;

  Circle(this.radius);

  @override
  double calculateArea() {
    return 3.14 * radius * radius;
  }
}
```

---

### **3. Liskov Substitution Principle (LSP)**
Objects of a superclass should be replaceable with objects of a subclass without affecting the correctness of the program.

#### Example:
```dart
// Bad: Subclass violates LSP
class Bird {
  void fly() {
    print('Flying');
  }
}

class Ostrich extends Bird {
  @override
  void fly() {
    throw Exception('Ostrich cannot fly');
  }
}

// Good: Subclass adheres to LSP
abstract class Bird {
  void move();
}

class Sparrow extends Bird {
  @override
  void move() {
    print('Flying');
  }
}

class Ostrich extends Bird {
  @override
  void move() {
    print('Running');
  }
}
```

---

### **4. Interface Segregation Principle (ISP)**
Clients should not be forced to depend on interfaces they do not use. Instead, create smaller, more specific interfaces.

#### Example:
```dart
// Bad: A single interface with unrelated methods
abstract class Worker {
  void work();
  void eat();
}

class HumanWorker implements Worker {
  @override
  void work() {
    print('Working');
  }

  @override
  void eat() {
    print('Eating');
  }
}

class RobotWorker implements Worker {
  @override
  void work() {
    print('Working');
  }

  @override
  void eat() {
    throw Exception('Robots do not eat');
  }
}

// Good: Segregated interfaces
abstract class Workable {
  void work();
}

abstract class Eatable {
  void eat();
}

class HumanWorker implements Workable, Eatable {
  @override
  void work() {
    print('Working');
  }

  @override
  void eat() {
    print('Eating');
  }
}

class RobotWorker implements Workable {
  @override
  void work() {
    print('Working');
  }
}
```

---

### **5. Dependency Inversion Principle (DIP)**
High-level modules should not depend on low-level modules. Both should depend on abstractions. Abstractions should not depend on details. Details should depend on abstractions.

#### Example:
```dart
// Bad: High-level module depends on low-level module
class MySQLDatabase {
  void saveData(String data) {
    print('Saving data to MySQL: $data');
  }
}

class DataManager {
  final MySQLDatabase database;

  DataManager(this.database);

  void save(String data) {
    database.saveData(data);
  }
}

// Good: Both depend on abstractions
abstract class Database {
  void saveData(String data);
}

class MySQLDatabase implements Database {
  @override
  void saveData(String data) {
    print('Saving data to MySQL: $data');
  }
}

class DataManager {
  final Database database;

  DataManager(this.database);

  void save(String data) {
    database.saveData(data);
  }
}
```

---

### **Summary of S.O.L.I.D Principles**
| Principle             | Description                              | Example                                   |
|-----------------------|------------------------------------------|-------------------------------------------|
| **SRP**               | A class should have only one responsibility | `UserRepository`, `EmailService`         |
| **OCP**               | Open for extension, closed for modification | `Shape`, `Rectangle`, `Circle`           |
| **LSP**               | Subclasses should be substitutable for their base classes | `Bird`, `Sparrow`, `Ostrich`             |
| **ISP**               | Create smaller, specific interfaces      | `Workable`, `Eatable`                    |
| **DIP**               | Depend on abstractions, not concretions  | `Database`, `MySQLDatabase`, `DataManager` |

---

### **Example: Full Usage**
Here’s a complete example of applying S.O.L.I.D principles in Dart:
```dart
// SRP: Single Responsibility Principle
class UserRepository {
  void saveUser() {
    print('User saved');
  }
}

class EmailService {
  void sendEmail() {
    print('Email sent');
  }
}

// OCP: Open/Closed Principle
abstract class Shape {
  double calculateArea();
}

class Rectangle extends Shape {
  double width;
  double height;

  Rectangle(this.width, this.height);

  @override
  double calculateArea() {
    return width * height;
  }
}

class Circle extends Shape {
  double radius;

  Circle(this.radius);

  @override
  double calculateArea() {
    return 3.14 * radius * radius;
  }
}

// LSP: Liskov Substitution Principle
abstract class Bird {
  void move();
}

class Sparrow extends Bird {
  @override
  void move() {
    print('Flying');
  }
}

class Ostrich extends Bird {
  @override
  void move() {
    print('Running');
  }
}

// ISP: Interface Segregation Principle
abstract class Workable {
  void work();
}

abstract class Eatable {
  void eat();
}

class HumanWorker implements Workable, Eatable {
  @override
  void work() {
    print('Working');
  }

  @override
  void eat() {
    print('Eating');
  }
}

class RobotWorker implements Workable {
  @override
  void work() {
    print('Working');
  }
}

// DIP: Dependency Inversion Principle
abstract class Database {
  void saveData(String data);
}

class MySQLDatabase implements Database {
  @override
  void saveData(String data) {
    print('Saving data to MySQL: $data');
  }
}

class DataManager {
  final Database database;

  DataManager(this.database);

  void save(String data) {
    database.saveData(data);
  }
}

void main() {
  // SRP
  final userRepo = UserRepository();
  userRepo.saveUser();

  final emailService = EmailService();
  emailService.sendEmail();

  // OCP
  final rectangle = Rectangle(10, 20);
  print('Rectangle Area: ${rectangle.calculateArea()}');

  final circle = Circle(5);
  print('Circle Area: ${circle.calculateArea()}');

  // LSP
  final sparrow = Sparrow();
  sparrow.move();

  final ostrich = Ostrich();
  ostrich.move();

  // ISP
  final humanWorker = HumanWorker();
  humanWorker.work();
  humanWorker.eat();

  final robotWorker = RobotWorker();
  robotWorker.work();

  // DIP
  final mySQLDatabase = MySQLDatabase();
  final dataManager = DataManager(mySQLDatabase);
  dataManager.save('Some data');
}
```

---

## OOP 

Here’s a summary of the article **[OOP in Flutter: In-Depth](https://medium.com/@ahsan-001/oop-in-flutter-in-depth-6cbf40640d69)** by Ahsan Ayaz, using the same method with explanations and **code examples**:

---

### **OOP in Flutter: In-Depth**
Object-Oriented Programming (OOP) is a programming paradigm that uses objects and classes to structure code. Flutter, being built on Dart, fully supports OOP principles. This article explores how OOP concepts are applied in Flutter.

---

### **1. Classes and Objects**
A **class** is a blueprint for creating objects, and an **object** is an instance of a class.

#### Example:
```dart
class Car {
  String brand;
  String model;

  Car(this.brand, this.model);

  void drive() {
    print('Driving $brand $model');
  }
}

void main() {
  Car myCar = Car('Toyota', 'Corolla');
  myCar.drive(); // Output: Driving Toyota Corolla
}
```

---

### **2. Encapsulation**
Encapsulation is the bundling of data (attributes) and methods (functions) that operate on the data into a single unit (class). It also restricts direct access to some of an object's components.

#### Example:
```dart
class BankAccount {
  double _balance = 0; // Private variable

  void deposit(double amount) {
    if (amount > 0) {
      _balance += amount;
    }
  }

  double getBalance() {
    return _balance;
  }
}

void main() {
  BankAccount account = BankAccount();
  account.deposit(100);
  print(account.getBalance()); // Output: 100.0
}
```

---

### **3. Inheritance**
Inheritance allows a class (subclass) to inherit properties and methods from another class (superclass).

#### Example:
```dart
class Animal {
  void eat() {
    print('Eating...');
  }
}

class Dog extends Animal {
  void bark() {
    print('Barking...');
  }
}

void main() {
  Dog dog = Dog();
  dog.eat(); // Output: Eating...
  dog.bark(); // Output: Barking...
}
```

---

### **4. Polymorphism**
Polymorphism allows objects of different classes to be treated as objects of a common superclass. It can be achieved through method overriding.

#### Example:
```dart
class Animal {
  void makeSound() {
    print('Animal sound');
  }
}

class Dog extends Animal {
  @override
  void makeSound() {
    print('Bark');
  }
}

class Cat extends Animal {
  @override
  void makeSound() {
    print('Meow');
  }
}

void main() {
  Animal dog = Dog();
  Animal cat = Cat();

  dog.makeSound(); // Output: Bark
  cat.makeSound(); // Output: Meow
}
```

---

### **5. Abstraction**
Abstraction is the concept of hiding the complex implementation details and showing only the necessary features of an object. It can be achieved using abstract classes and interfaces.

#### Example:
```dart
abstract class Shape {
  void draw(); // Abstract method
}

class Circle extends Shape {
  @override
  void draw() {
    print('Drawing a circle');
  }
}

class Square extends Shape {
  @override
  void draw() {
    print('Drawing a square');
  }
}

void main() {
  Shape circle = Circle();
  Shape square = Square();

  circle.draw(); // Output: Drawing a circle
  square.draw(); // Output: Drawing a square
}
```

---

### **6. Composition**
Composition is a way to combine simple objects or data types into more complex ones. It represents a "has-a" relationship.

#### Example:
```dart
class Engine {
  void start() {
    print('Engine started');
  }
}

class Car {
  final Engine engine;

  Car(this.engine);

  void startCar() {
    engine.start();
    print('Car started');
  }
}

void main() {
  Engine engine = Engine();
  Car car = Car(engine);
  car.startCar(); // Output: Engine started, Car started
}
```

---

### **7. Mixins**
Mixins are a way to reuse code in multiple class hierarchies. They allow you to add functionality to a class without using inheritance.

#### Example:
```dart
mixin Flyable {
  void fly() {
    print('Flying...');
  }
}

class Bird with Flyable {
  void chirp() {
    print('Chirping...');
  }
}

void main() {
  Bird bird = Bird();
  bird.chirp(); // Output: Chirping...
  bird.fly(); // Output: Flying...
}
```

---

### **8. Using OOP in Flutter**
OOP principles are widely used in Flutter to create reusable and maintainable widgets and logic.

#### Example:
```dart
class CustomButton extends StatelessWidget {
  final String text;
  final VoidCallback onPressed;

  CustomButton({required this.text, required this.onPressed});

  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: onPressed,
      child: Text(text),
    );
  }
}

void main() {
  runApp(MaterialApp(
    home: Scaffold(
      appBar: AppBar(title: Text('OOP in Flutter')),
      body: Center(
        child: CustomButton(
          text: 'Click Me',
          onPressed: () {
            print('Button clicked');
          },
        ),
      ),
    ),
  ));
}
```

---

### **Summary of OOP Concepts**
| Concept               | Description                              | Example                                   |
|-----------------------|------------------------------------------|-------------------------------------------|
| **Classes and Objects** | Blueprint and instance of a class      | `Car myCar = Car('Toyota', 'Corolla');`   |
| **Encapsulation**     | Bundling data and methods               | `BankAccount` with private `_balance`     |
| **Inheritance**       | Subclass inherits from superclass       | `Dog extends Animal`                      |
| **Polymorphism**      | Overriding methods in subclasses        | `Dog` and `Cat` override `makeSound`      |
| **Abstraction**       | Hiding implementation details           | `Shape` abstract class with `draw` method |
| **Composition**       | Combining objects into complex ones     | `Car` has an `Engine`                     |
| **Mixins**            | Reusing code across classes             | `Bird with Flyable`                       |

---

### **Example: Full Usage**
Here’s a complete example of applying OOP principles in Flutter:
```dart
import 'package:flutter/material.dart';

// Encapsulation
class BankAccount {
  double _balance = 0;

  void deposit(double amount) {
    if (amount > 0) {
      _balance += amount;
    }
  }

  double getBalance() {
    return _balance;
  }
}

// Inheritance
class Animal {
  void eat() {
    print('Eating...');
  }
}

class Dog extends Animal {
  void bark() {
    print('Barking...');
  }
}

// Polymorphism
class Cat extends Animal {
  @override
  void eat() {
    print('Cat is eating...');
  }
}

// Abstraction
abstract class Shape {
  void draw();
}

class Circle extends Shape {
  @override
  void draw() {
    print('Drawing a circle');
  }
}

// Composition
class Engine {
  void start() {
    print('Engine started');
  }
}

class Car {
  final Engine engine;

  Car(this.engine);

  void startCar() {
    engine.start();
    print('Car started');
  }
}

// Mixins
mixin Flyable {
  void fly() {
    print('Flying...');
  }
}

class Bird with Flyable {
  void chirp() {
    print('Chirping...');
  }
}

// Flutter Widget
class CustomButton extends StatelessWidget {
  final String text;
  final VoidCallback onPressed;

  CustomButton({required this.text, required this.onPressed});

  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: onPressed,
      child: Text(text),
    );
  }
}

void main() {
  // Encapsulation
  BankAccount account = BankAccount();
  account.deposit(100);
  print(account.getBalance()); // Output: 100.0

  // Inheritance
  Dog dog = Dog();g
  dog.eat(); // Output: Eating...
  dog.bark(); // Output: Barking...

  // Polymorphism
  Animal cat = Cat();
  cat.eat(); // Output: Cat is eating...

  // Abstraction
  Shape circle = Circle();
  circle.draw(); // Output: Drawing a circle

  // Composition
  Engine engine = Engine();
  Car car = Car(engine);
  car.startCar(); // Output: Engine started, Car started

  // Mixins
  Bird bird = Bird();
  bird.chirp(); // Output: Chirping...
  bird.fly(); // Output: Flying...

  // Flutter App
  runApp(MaterialApp(
    home: Scaffold(
      appBar: AppBar(title: Text('OOP in Flutter')),
      body: Center(
        child: CustomButton(
          text: 'Click Me',
          onPressed: () {
            print('Button clicked');
          },
        ),
      ),
    ),
  ));
}
```

---

