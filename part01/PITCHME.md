# REST API's with Django and Django Rest Framework

### A Powerful way to do REST API's

---
## Introduction
- What's a Rest?
- The Constraints|
- The foundations of REST|
- REST vs. SOAP|
- The best practices of HTTP that guide REST|
- Some practices to use our resources|
- References|

---
## What's a REST?

- It had its origin in Roy Fielding' PhD Thesis|
- Roy Fielding was one of the authors of HTTP protocol|
- REST means REpresentational State Transfer|
- REST or RESTFul??|
- Roy proposed some constraints to define the REST architecture|

+++
## The Constraints
- Client-Server
  - The main purpose of this constraint is to separate the responsibilities of each part of the application|
  - Servers are not concerned with the user interface or user state so that servers can be simpler and more scalable.|
  - Servers and clients may also be replaced and developed independently, as long as the interface is not altered.|

+++
## The Constraints
- Stateless
  - Essentially, what this means is that the necessary state to handle the request is contained within the request itself, whether as part of the URI, query-string parameters, body, or headers.|
  - In REST, the client must include all information for the server to fulfill the request, resending state as necessary if that state must span multiple requests.|
  
+++
## The Constraints
- Cacheable
  - As on the World Wide Web, clients can cache responses. Responses must therefore, implicitly or explicitly, define themselves as cacheable, or not, to prevent clients reusing stale or inappropriate data in response to further requests. Well-managed caching partially or completely eliminates some client–server interactions, further improving scalability and performance.|
  
+++
## The Constraints
- Uniform Interface
  - The uniform interface constraint defines the interface between clients and servers. It simplifies and decouples the architecture, which enables each part to evolve independently. The four guiding principles of the uniform interface are:|
    - Resource-Based: Individual resources are identified in requests using URIs as resource identifiers.|

+++
## The Constraints
- Uniform Interface
  - four guiding principles (continuation)  
    - Manipulation of Resources Through Representations: When a client holds a representation of a resource it has enough information to modify or delete the resource on the server, provided it has permission to do so.|
    - Self-descriptive Messages: Each message includes enough information to describe how to process the message.|

+++
## The Constraints
- Uniform Interface
  - four guiding principles (continuation)  
    - Hypermedia as the Engine of Application State (HATEOAS): Clients deliver state via body contents, query-string parameters, request headers and the requested URI (the resource name). Services deliver state to clients via body content, response codes, and response headers. This is technically referred-to as hypermedia.

+++
## The Constraints
- Layered System
  - A client cannot ordinarily tell whether it is connected directly to the end server, or to an intermediary along the way. Intermediary servers may improve system scalability by enabling load-balancing and by providing shared caches. Layers may also enforce security policies.

+++
## The Constraints
- Code on Demand (optional)
  - Servers are able to temporarily extend or customize the functionality of a client by transferring logic to it that it can execute. Examples of this may include compiled components such as Java applets and client-side scripts such as JavaScript.

---

## The foundations of REST

- The first concept fundamental in REST is the resource
  - Basically, the resource is the most important concept that we need to understand;| 
  - The resource is the set of data that traffics by the protocol;|
  - The resources define how our URLs will be described.|

---

## The foundations of REST

- The second concept fundamental in REST is the representation
  - The representation is the form that the information is exchanged between client and server;| 
  - The representation can be a JSON, XML, HTML, JPG, PNG, MP3, MP4 etc;|

---
## REST vs. SOAP
- REST is an architecture model.
- SOAP is a protocol.

| REST                                        | SOAP                                    | 
|---------------------------------------------|-----------------------------------------|
| architecture model                          | Protocol                                |
| Simple HTTP Request                         | Invokes services by calling RPC method  |
| Support many types like XML, JSON, and YAML | Support only XML                         |

---
### The best practices of HTTP that guide REST

- Appropriate use of HTTP methods;
  - GET, POST, PUT, PUSH and DELETE (but not only!!)
- Appropriate use of URLs; |
- Appropriate use of HTTP status code to represent success or fails;|
  - The families: 100, 200, 300, 400 and 500|
- Appropriate use of HTTP headers;|
- The possibility to link various different resources.|

---
### Some practices to use our resources - 1:

1 Use nouns, not verbs
   - The resources are a set of data about somewhere or something, then use nouns not verbs, 
   e.g. as follow 

<br>

| Resource  | GET read               | POST create              | PUT update             | DELETE                 |
|-----------|------------------------|--------------------------|------------------------|------------------------|
| /cars     | Returns a list of cars | Create a new car         | Bulk update of cars    | Delete all cars        |
| /cars/711 | Returns a specific car | Method not allowed (405) | Updates a specific car | Deletes a specific car |

---
### Some practices to use our resources - 2a:

2 The HTTP methods
  - The best practice in REST is the appropriate use of HTTP methods: GET, POST, PUT, PUSH and DELETE|
  - GET: This method is used to return a list of all objects of our resource or all data about a specific object of our resource, to return a specific object we use the ID of it.|
  - POST: This method is used to create a new instance of an object into our resource.|

---
### Some practices to use our resources - 2b:

2 The HTTP methods
  - PUT: This method is used to update all data of a specific object of our resource.|
  - PUSH: This method is similar to PUT, but it can update a partial data of a specific object of our resource.|
  - DELETE: Well, with this method we can delete all objects of our resource, if we call it into a root of our resource, or we can delete a specific object of our resource with its ID.|

---
### Some practices to use our resources - 3:
3 GET method and query parameters should not alter the state of our resources
  - To alter the state of our resources we shold use the POST, PUT, PUSH or DELETE methods;|
  - The common error is use something like:|
    - GET /cars/711?activate|
    - GET /cars/711/activate|
  - That is not a good practice, the GET method is used to return a list of all objects or a specific object with its ID|
  
---
### Some practices to use our resources - 4a:
4 Use HTTP status code
  - The HTTP standard provides over 70 status codes to describe the return values. 
  - We don’t need them all, but  there should be used at least a mount of 10:|
  - Family 200:|
    - 200 – OK – Eyerything is working|
    - 201 – OK – New resource has been created|
    - 204 – OK – The resource was successfully deleted|

---
### Some practices to use our resources - 4b:
4 Use HTTP status code
  - Family 300:
    - 304 – Not Modified – The client can use cached data|
  - Family 400:|
    - 400 – Bad Request – The request was invalid or cannot be served. The exact error should be explained in the error payload. E.g. "The JSON is not valid"|
---
### Some practices to use our resources - 4c:
4 Use HTTP status code
  - Family 400:    
    - 401 – Unauthorized – The request requires an user authentication|
    - 403 – Forbidden – The server understood the request, but is refusing it or the access is not allowed.|
    - 404 – Not found – There is no resource behind the URI.|
  
---
### Some practices to use our resources - 4d:
4 Use HTTP status code
  - Family 400:
    - 405 – Method not allowed – Indicates that the request method is known by the server but has been disabled and cannot be used.|
    - 422 – Unprocessable Entity – Should be used if the server cannot process the enitity, e.g. if an image cannot be formatted or mandatory fields are missing in the payload.|
---
### Some practices to use our resources - 4e:
4 Use HTTP status code
  - Family 500:
    -  500 – Internal Server Error – API developers should avoid this error. If an error occurs in the global catch blog, the stracktrace should be logged and not returned as response.
---
## References

- Fielding, Roy Thomas. [Representational State Transfer (REST)](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm). In: Architectural Styles and the Design of Network-based Software Architectures. Doctoral dissertation, University of California, Irvine, 2000.
- Mozilla Developer Network - [HTTP response status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [REST: Construa API's inteligentes de maneira simples](https://www.casadocodigo.com.br/products/livro-rest)
- [RESTful Web APIs: Services for a Changing World](https://www.amazon.com.br/RESTful-Web-APIs-Services-Changing-ebook/dp/B00F5BS966?__mk_pt_BR=ÅMÅŽÕÑ&crid=1BZRSA6D9SLUW&keywords=Restful+Web+API&qid=1523476637&sprefix=django+rest+fra%2Caps%2C276&sr=8-2&ref=sr_1_2)

---
## References

- [Django RESTful Web Services: The easiest way to build Python RESTful APIs and web services with Django](https://www.amazon.com.br/Django-RESTful-Web-Services-services-ebook/dp/B079GZ949B?__mk_pt_BR=ÅMÅŽÕÑ&crid=1RJDGXRK5MIC9&keywords=django+restful+web+services&qid=1523476696&sprefix=Django+Restful%2Cdigital-text%2C284&sr=8-1-fkmrnull&ref=sr_1_fkmrnull_1)
- [http://www.restapitutorial.com](http://www.restapitutorial.com)
- [Entendendo e documentando REST / RESTful APIs](https://www.udemy.com/restful-apis/learn/v4/overview)


---
# Thank you!


