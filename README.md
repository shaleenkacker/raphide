# Raphide
A microservice can be designed as a collection of functions which perform a specific role. The atomicisation of  the microservice enables scalability, concurrency and fault tolerance, among other benefits. It also makes the service loose grained and highly extensible. The functions can be used in an event sourced mode and real time mode. 
                Having a servlerless microservice design helps removing the dependency of the service on containers to provision and deploy and aids in portability of the software across environments. 
               The above is an architectural diagram of an idea for a serverless microservice design. The service delivers its context using functions. The core functions of a typical microservice are, 
1.	Application Context. 
2.	Processor or Server 
3.	Realtime request handler. 
4.	Batch handler
5.	Scheduler
 
Core components of the design. 
1.	RphContext : Works in the Async mode and processes requests, payloads to attach a context to the request. The request, for example, could be to add, update or delete a record.
2.	RphProcessQ: A Topic that distributes a request with a context to the server. It's also subscribed to by the events handler which creates an event for every message in the topic. 
3.	RphServer: It is the core function of the microservice. The server processes the request with the context. It delegates further processing to additional functions if required and aggregates the results. It also talks with external interfaces, other microservices and persistence. It does not, however, respond to a request from the context in real time. That is done through the real time handler. 
4.	RphRT: A synchronous, light weight, equivalent of the Context, processes the request and prepares the response in real time. It can fetch the results from persistence or cache, depends on the state. 
5.	RphBatch: Is a batch processor which is triggered by a scheduler. The scheduler sets the ball in motion and creates a task with context for the server. The server iteratively prepares the workload for the task and hands it over to the batch processor to serve. The batch server processes the tasks and adds a context for the Server for final or further processing or delivery to persistence.  
6.	RphResponseQ: A queue for the batch handler to process. 
7.	RphRetryQ: Requests that cannot be processed successfully by the RphServer are sent to the Retry queue for the retry handler to process. The handler may try to fix inconsistencies in the task that caused it to not complete. It will also start a retry counter on the task it will return to the processor for a retry attempt. Once the maximum number of tries are exhausted, the process is permanently rejected.   
