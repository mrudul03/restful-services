# RESTful API Best Practices

This post is not an invention, but is a gathering of best practices and insights provided by many across the community.

# What is API
> As per Wikipedia: An Application Programming Interface (API) is a specification intended to be used as an interface by software components to communicate with each other. 

# What is REST
[REST is a set of principles] (http://www.infoq.com/articles/rest-introduction) that define how Web standards, such as HTTP and URIs, are supposed to be used (which often differs quite a bit from what many people actually do). REST stands for Representational State Transfer, and was first described in chapter 5 of [Roy Fielding's Ph.D dissertation] (http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm).

#### [REST Architectural Constraints] (http://en.wikipedia.org/wiki/Representational_state_transfer#Constraints) defined by Wikipedia
* Client-Server
* Stateless
* Cacheable
* Layered System
* Uniform Interface

#### REST principles adopted by RESTful APIs.
* Expose 'Resources' to represent important concepts and objects
* Ensure each resource is uniquely 'addressable' via a URI, so that clients may interact with them over HTTP
* Provide 'representations' (e.g., using JSON and or XML) of those resources
* Provide a consistent interface based upon the standard HTTP methods
* Ensure interaction with the API is stateless in nature, in order to provide flexibility in deployment as well as to promote scalability

# RESTful vs REST
The difference between REST and RESTful is simply semantics. REST is an architectural style applied to a client-server relationship. RESTful is simply a way of telling your clients that you use REST. RESTful API should conform to REST constraints defined above.

Martin Fowler authored a very nice overview of the [Richardson Maturity Model] (http://martinfowler.com/articles/richardsonMaturityModel.html) used for gauging an API's 'level of RESTfulness'. This seems to be the most popular maturity model for REST.

# What makes a good RESTful API
* Follows Robustness principle which says “Follow a general principle of robustness: be conservative in what you do, be liberal in what you accept from others."
* An API is a developer's UI - just like any UI, it's important to ensure the user's experience is thought out carefully!   * An API is only as good as its documentation. The documents should be easy to find and publically accessible.
* Provides support for backward compatibility and or versioning
* Does not expose internal domain object structures or database entity as resource representations

# API Design Guidelines

#### Expose Meaningful Resources for API
“The key abstraction of information in REST is a resource. Any information that can be named can be a resource: a document or image, a temporal service (e.g. "today's weather in Los Angeles"), a collection of other resources, a non-virtual object (e.g. a person), and so on. In other words, any concept that might be the target of an author's hypertext reference must fit within the definition of a resource. A resource is a conceptual mapping to a set of entities, not the entity that corresponds to the mapping at any particular point in time.” - [Roy Fielding’s dissertation] (http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm#sec_5_2_1_1).

It is very important to select the right resources and model the resources at the right granularity while designing the REST API so that the API consumers get the desired functionality from the APIs, the APIs behave correctly and the APIs are maintainable.

Identifying the resources to expose, and the content of those resources, is an iterative process and requires feedback. It is also important not to allow one client to drive the API design at the expense of other clients (if they are also known), or to deviate from common API practices or this strategy. APIs are basically a "business layer" or middleware application tier. APIs should either encapsulate business operations or, in the ever-more-popular REST style, application resources.

#### Fine Grained Resources VS Coarse Grained Resources
If we design the API around fine-grained resources, we would end up with a chattier API for consumer applications. On the other hand, if we design the API based on very coarse-grained resources (designed to do everything) there will not be enough variations to support all the API consumers’ needs and the API may become too difficult to use and maintain.

There may be situations where an API that is built around low-level resources may just be fine. For example, to get bank account balance information, an API that is built around “Account” resource is good enough. On the other hand, if the need is to do a money transfer or to get a bank statement, the API needs to be built around the coarse-grained “Transactions” resource. Service designer should consider the nature of the client-service interactions imposed by the contract to design the APIs as fine-grained or coarse-grained.

#### Updates & creation should return a resource representation
A PUT, POST or PATCH call may make modifications to fields of the underlying resource that weren't part of the provided parameters (for example: created_at or updated_at timestamps). To prevent an API consumer from having to hit the API again for an updated representation, have the API return the updated (or created) representation as part of the response.

#### Define a consumable error payload
Just like an HTML error page shows a useful error message to a visitor, an API should provide a useful error message in a known consumable format. The representation of an error should be no different than the representation of any resource, just with its own set of fields.

#### Use Common URI Conventions
With respect to URIs, RESTful APIs should:
* Use transparent (readable) URIs unless specific requirements preclude
* Use dashes (aka hyphens) in lieu of underscores (when easily supported by your framework)
* Use dashes (aka hyphens) in lieu of camel case. (This is proposed only for API URLs, and only if supported by your framework.)
* Support nested resources when doing so is logical and only two levels deep
* The use of plural resource names is preferred, but not required. For example, it is proposed to use 'colleges' versus 'college' for URLs used to refer to both collections and specific elements within a collection. Using singular naming is not incorrect, but using plural naming is also common and may arguably read better.
* Representations for a collection and a single instance of a resource are to be considered the same resource from an addressing perspective. That is, a list of colleges may be referenced as /colleges, a specific college instance may be referenced using a URI such as '/colleges/college-43'.

#### API Documentation
An API is only as good as its documentation. The docs should be easy to find and publically accessible. The docs should show examples of complete request/response cycles.
[Swagger] (http://swagger.io/) seems to become the default standard for defining, implementing, discovering and testing REST services.

#### Use Standard HTTP Methods
APIs should at a minimum expose a standard REST interface using the four HTTP methods (aka verbs) shown below:

HTTP Method  | Description
------------- | -------------
GET  | Read a resource or list of resources
POST  | Create a new resource 
PUT | Update an existing resource or create one if the key is pre-defined
DELETE | Remove a resource

#### Use Standard HTTP Status Codes
RESTful APIs should be designed to use standard HTTP status codes. The following status codes are recommended:

HTTP Method  | Description
------------- | -------------
200 | The request was successful.
201 | A resource was successfully created.
202 | Request has been received. This is only used for asynchronous processing.
304 | The GET request was conditional and contained an 'If-None-Match' header that was used to determine the current representation has not changed
400 | The request cannot be understood. This should be used if there are syntax errors and validation errors, (422 is also acceptable for validation errors.)
401 | Authentication has failed, or the authenticated client is not authorized.
404 | The requested resource could not be found.
405 | The method is not supported by the resource.
406 | Not Acceptable. Return this code when the Accept header (or a custom header as discussed in this strategy) identifies an unsupported format.
409 | There is a conflict. Return this if the request reflects stale data (that violates an optimistic lock).
412 | A precondition failed. Used for conditional requests (e.g., when an 'If-Match' header does not match a resource's Etag value).
415 | Unsupported Media Type. Return when a client requests content in an unsupported format.
417 | Expectation Failed. Return this if appropriate when supporting 'look-before-you-leap' requests.
422 | Unprocessable entity. See note below.
429 | The rate limit is exceeded.
500 | An undisclosed server error occurred. This is a generic 'fail whale' response.


#### Use API versioning
It is important to version APIs in order to provide clients with stability. Versioning helps you iterate faster and prevents invalid requests from hitting updated endpoints. There are several common approaches used across the industry today for versioning RESTful APIs, the most common being to specify a version:

* within the URL
* using a query parameter
* using a custom media type identified in the Accept and Content-Type headers

RESTful APIs should use custom media types (previously known as 'MIME types') to allow client applications the ability to interact with a stable, versioned API when desired.

#### Support Pagination
When returning collections, it may be necessary to support paging. There is no single standard for paging, however 'page' and 'per-page' are recommended, the specific query parameter names may differ based upon your framework.

#### Understand Common REST Design Patterns

* Asynchronous Processing with REST Asynchronous processing may be supported by having an endpoint perform work asynchronously, and immediately return a '202' status code with a link that the caller may subsequently use to check the status of the asynchronous process.

* Business Transactions with REST SOAP allows for externalization of business transactions using WS-BusinessActivity. Business (aka logical) transactions may be modeled explicitly as a resource, so that a business transaction can be started using a POST, status updated using PUT, and committed using PUT and or DELETE. 

* Reliable Messaging with REST While REST does not have built-in 'reliable messaging' support, it uses synchronous communication over the HTTP protocol and can thus recover more safely from potentially lost messages.

#### Governance
Governance is a broad area that may encompass service registries, service repositories, schema catalogs, server configurations, service management (consumption, usage patterns, etc.), and even development processes.

Service repositories are used to centrally store information about the services that are present in your organization's service inventory whereas service registries are often used at runtime to help with problems like endpoint resolution.

At a minimum, a lightweight process and an organization-wide schema reuse library, data dictionary, or repository is needed to promote commonality across an organization creating APIs. One can also build their own [simple service repository] (http://www.infoq.com/articles/SimpleServiceRepository) or use tools such as [IgniteXML] (http://www.digitalml.com/).

Governance should ensure consistent exposure of resources with respect to representations and semantics.

#### API Management
At the highest level, an API management solution needs to include a –

* Developer Portal for developers to discover APIs, understand usage and sign up for access
* API Gateway that secures and mediates the traffic between your APIs and its consumers
* API Lifecycle Management to manage the process of designing, developing, deploying, versioning and retiring APIs

#### Automated Testing
IN PROGRESS

### References
1.	http://www.infoq.com/articles/rest-introduction
2.	http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm 
3.	http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api
4.	http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm
