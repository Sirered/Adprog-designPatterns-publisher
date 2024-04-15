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


1. The main reason we make such separations is to fulfill the Single Responsibility Principle. Instead of the model classes having to handle all of the business or storing logic, the model classes are only responsible for representing the data. This makes it easier for us to convert from class to SQL and vice versa, as there is no information regarding business or storage methods that would muddle up the data being stored. Furthermore, it makes it easier to test and review the code, as the types of methods are separate, neat and unmuddled, making it easier to design simple tests and to find bugs in the codebase.

2. If we were to have all code regarding a specific model be all in that Model's class, it would be absurd and immensely complex. Firstly, it would be absurdly long, since you would need to contain the business, storing and controller (I'm assuming the question is also asking without Controller as well), would all have to be on one file, which will be a slog to get to.

To store the data, you would likely use a static list or dictionary in each model, that contains all instances of that class, and to make any changes made to an instance of a certain model to stick, you would need to update that static dictionary. If any of the models depend on another model, for example, notification has a subscriber and a product/program that it is associated with, thus it would need to access the dictionaries of the other 2 models, or you have to make methods for each model that would facilitate acts like fetching, adding or saving in the other models, which will further increase the amount of code, making it even more complex. 


Also, these methods that add to the dictionary would have to be static, which isn't really a problem in rust, but in programming languages like Java, where you can call static methods from instances of that class, it may be confusing whether the add method is for adding a subscriber to the dictionary or if it's to add a program to a subscriber's list for exxample


Furthermore, this structure would be incredibly coupled, so if any change is made to any method for any model, there could very easily be a side-effect that causes a method in another model to break. This would be quite a conundrum to solve, because with all methods being in only 3 classes, tracking the root of the problem will be challenging (even if you only make one change at a time, since the problem may be a method that was working before with previous implementation, but no longer works properly after a certain change, even if the problem method remains unchanged), and there is a very real possibility that the change to fix the problem can cause something else to break.


Overall, this would break multiple SOLID and Clean Code principles, which will cause maintainability to plummet and likelihood of rage to skyrocket.


3. Firstly, the main reason we would use Postman is to test API and backend responses to see what data is being sent and the format of such. Since we can put them into collections, run all requests in a collection at once, as well as automate these runs (apparently it's also possible to integrate it with CI/CD) it would be an invaluable resource to quickly analyse the data being sent by all endpoints. This is helpful for back-end programmers to make sure their programs are working, while it is helpful for front-end programmers to know what they are getting from each endpoint and the format, so they can effectively serve their data (this is what we did when we had to check json data that was being sent to flutter mobile apps). 

They also apparently allow us to set tests, giving us a good method to do tests on our application, that may be difficult depending on the framework and language that the application is made with. To further this point, we can set environment variables and scripts run on every request, so we can have variables and processes that run before every request for isolated testing.

Lastly, Postman allows us to easily create documentation for a collection of requests and responses, so you can create the requests, run them to get the responses, then use Postman's tools to make documentation out of it, so you can easily document and share what you can/should be able to do with the application and what the expected response is for each, so front-end developers have kind of cheat sheet as to what they can do, and back-end developers have a guide as to what the response should be like even after refactoring.


#### Reflection Publisher-3


1. It is a Push model, since whenever there is a change in the Product Service, such as the creation or the deletion of a product, a notification is made/pushed to all subscribers that is subscribed to thje product type that that created/deleted product has using the NotificationService's notify method that we had implemented.

2. 
Advantages:

The observer can choose when to get their updates, so if there are cases where certain observers are unresponsive/only on when the user is using the application (for example classes on front-end of a flutter mobile app), they can make it so that they get notifications/updates when the application is opened after some time of inactivity, instead of it being sent out whenever a change happens, in which case the application may not be active to catch it. It also makes it so that you don't have to push an update to EVERY observer on every change all at once, which would reduce overhead (since the frequency and amount being sent won't be too numerous) and latency (since a create won't cause a massive amount of update/notification functions to be run). It could also increase scalability, since updates are handled by each observer rather than it being handled entirely by the publisher, who would have to slave away until every observer is updated after any update if we use the push model.


Disadvantages:

We would have to ensure that we choose proper intervals or events as to when the observer performs a pull. If we make the intervals too short, we would end with a lot of instances of repeatedly and unnecessarily asking for updates, when no updates may have happened. This would actually badwidth usage and server load due to the frequency of unnecessary requests. However, if the intervals are too long or in cases where an update happens after the pull request, we may have occurences where data from the observer's side may be severely out of sync from the server and lacking crucial information, which would be especially detrimental in applications that require quick/real-time updates and responses. 


3. If we didn't use multithreading, we would have to wait for the notification of every single observer whenever a product is created or deleted, before we can finish the current request nor serve other requests that may or may not require notification. This would mean users who created somthing would notice that their requests take time, and other users who just so happen to request something when another user has just made a creation o deletion request, would also face similar lag even if they are requesting for something the shouldn't be heavy computationally, This latency would be unbearable as we increase the amount of observers/subscribers we have, so it would not scale as our service becomes larger. 