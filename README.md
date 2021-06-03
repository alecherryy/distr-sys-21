# Design Document
## Structure
The project follows the MVC architectural pattern. Starting with the `model` folder, you will find all the classes needed to implement a functional `Client`. To
abide the laws of scalability and performance, I decided to implement the consumer-producer pattern to generate and manage multiple threads. Below is a breakdown 
of the classes and packages I created.


### Server
The `Server` is built using Java's HTTP Servlet; it performs some basic operations such as validating the URL parameters.

### Client
The `Client` class only features a handful of lines of code. Its job is to request input from the user and instantiate the controller. 

### Model
Both clients' model include 4 classes: `Request`, `Producer`, `Consumer` and `DataQueue`; ClientTwo includes one additional class, `Record`, used to collect and 
store data about each `POST` request.

#### Request Class
The `Request` class can be used to dynamically create a new HTTP request. At this stage of the project, only `POST` requests are implemented, however, as the 
project evolves, additional request types can be added such as `GET`, `PUT` and `DELETE`. The object can easily be refactored to include an additional parameter 
in the class constructor to define the request type. After defining the type, a switch statement can be used to create the correct HTTP request. 
Once a new Request object is instantiated, the `send()` method can be used to append a message to the request body and receive an HTTP response in return. 
This class is used inside the `Consumer` to quickly generate and send new requests to the server.

#### Producer Class
The `Producer` class implements the `Runnable` interface and it's used to feed data to the data buffer. Producers accept two parameters, the data buffer and the 
file to evaluate. This object has only one method, `run()`.


#### Consumer Class
The `Consumer` class implements the `Runnable` interface as well and is responsible for creating and sending new `POST`requests and evaluate the response. 
Consumer threads run as long as the producer is working or as long as the `DataQueue` is not empty; we check both of these conditions to avoid an infinite waiting
period.

#### DataQueue Class
The `DataQueue` class is used to implement the data buffer between the producer and consumer. For the time being, the size of the `LinkedBlockingDeque` is set to 1000; however, 
as the project grows, this value might change or become dynamically set via user input. The `DataQueue` includes two `AtomicInteger` fields used to record the
total number of successful and failed responses.

### Controller
The controller package contains the `ClientController` which is responsible for consuming the file to be evaluated and dispatching threahds. The controller does so
by invoking the `ExecutorService` pattern, which can be used to create new thread pools. Once all threads terminate, the controller output a summary of the 
operations to the console.

