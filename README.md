# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

1. As far as I can tell, the only reason we would need an interface or trait in our program, is if we intend to have more than one type of Subscriber-Subject relation in our project other than the current User Subscriber- Product Type relation we have now. This is because the interface would only be used to define what a subscriber and what a subject must do, so if we have multiple types of observer-subject relations, we can use the same or similar structs or functions to manipulate and utilise different types of observer-subject relations without too much concern, since the interfaces would put a contract in place stating what functions need to exist for every observer and for every subject. 

This is less necessary if we only intend to have one type of observer-subject relation, since there would only be one set of structs to implement one observer-subject relation, so the advantage of being able to utilise similar code for different types of subject-observer relationships is not helpful at all. In fact it may be a hindrance, since adding such an interface that is only used by one class, can add unnecessary complexity and may cause refactoring and updating the Subscriber and Observers to be more tedious, without there being any real benefit to do so.


However, it is worth noting that if we intend to add more features later on and want to use the Observer pattern in the future, it may be better to add the interface, for scalibility's, as having such an interface and coding the structs using those interfaces would adhere to the Dependency Inversion and Open Closed Principles of SOLID


2. Technically, you can use Vec(lists) to implement the repositories, but that would be unnecessarily tedious, as you would need to check if a program already has that id or if a subscriber already has that url whenever you try to add a program/subscriber. On a list, that type of search would be O(n) time complexity, which would be absurd to do if we can just use a Dashmap, which already has handling of unique keys such as id and url built in (since all keys in a dictionary are unique and if you try to add an item with an id that already exists, it would replace the old value), as well as would be O(1) time complexity to check if that key already exists (which is useful if you want to handle such cases differently). Furthermore, searching for any subscriber or program would also take O(n) time complexity. With more subscribers, this O(n) time complexity could pose a real problem, especially if we consider the possibility of multiple people accessing the repository at the same time, which could cause some performance issues.


Hence, although you can **technically** implement such with Vec, thus meaning Vec is a **sufficient** data structure to use, I would advise against doing such and just use the dictionary instead to handle repository data, especially if there are unique values that can act as keys that each program and subscriber must have.

3. From what I can tell, the use of a Dashmap and the implementation of the Singleton design pattern aren't mutually exclusive, in fact I believe that the implementation of the Product and Subscriber Repositories are both examples of the Singleton design pattern. The Singleton Design pattern just states that instead of providing a constructor of an object when users want to access an object of that class (like a database), they are instead given a static method that gives them a static instance of that class, only ever creating an instance of the class if none exist. This guarantees that there is only ever one instance of a class and that users can't make their own instances of the class, which is advantageous for security reasons (e.g. you don't want users making their own instances of database connections to your database). 


In the code we see that there is only one static instance of the Dashmap that acts as our repository as the static variable PRODUCTS or SUBSCRIBERS, with the use of the lazy_static method (which is how we are taught to do the Singleton design pattern in the module). We access this Dashmap using that static variable and we do not create new instances of it. If another programmer wanted to access this static Dashmap, they can't just make a new Dashmap or an instance of ProductRepository, since all functions of the ProductRepository are static, so there is no point in making a new ProductRepository (plus there is no constructor), and the new Dashmap wouldn't contain any of the important data. Thus we have one static variable Dashmap, and a global access point, which the ProductRepository or SubscriberRepository struct fulfills (since the struct itself is a static instance, and you can't really make new versions of them, without it being completely different and being unable to access our repository data). 


Overall, it seems like we already utilise the Singleton design pattern, and the use of a Dashmap (or any implementation of the design pattern, as long as it follows the rules set by the pattern) does not matter when discussing the implementation of the design pattern. 

#### Reflection Publisher-2

#### Reflection Publisher-3
