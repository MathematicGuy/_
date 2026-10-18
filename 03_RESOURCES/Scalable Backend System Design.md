 ![[Pasted image 20261018171754.png|254]]


![[Pasted image 20261018172121.png|318]]

Business Logic (Service Class) => For *Application Main Feature Logic Management.*
Data Logic Layer (Repository Class) => For *Data Management (change diff database flexibly from SQL/NoSQL).*

**Factory Pattern:** Object Creation in 1 Place. 
Note: each payment service is a Object forto Payment System.
![[Pasted image 20261018172727.png]]
![[Pasted image 20261018172751.png]]


**Singleton Pattern for BE configuration management:** for thing that should be centralize and have 1 single consistant instance. 
![[Pasted image 20261018172818.png]]

![[Pasted image 20261018172914.png]]

**Strategy Pattern** use when 1 operation can be used for multiple situation. 
+ $ 1 operation for each specialize customer. 
![[Pasted image 20261018180632.png]]
![[Pasted image 20261018180805.png]]

![[Pasted image 20261018180821.png]]



**Observer pattern:** Event-Driven Approach
-> need example code
Note: Order Service is a Service, 4 Service below it called a Service Listener. 
	Instead of 1 Service listener call every event, have each event interact with that service independetly if needed -> Flexible. 
![[Pasted image 20261018173257.png]]
Instead of Order Service have 4 Events within it, create a indepenednt "service listeners" that call only required events (e.g. order placed call 4 specific function from 4 Class) -> Deep module. 
![[Pasted image 20261018173358.png]]
![[Pasted image 20261018173551.png]]
Similars to these service:
![[Pasted image 20261018173613.png]]

**Builder Pattern**


**Decorator Pattern** - add-on function base on the core logic/function.
e.g. `log_and_time_execution` is a extension of `process_refund` function. Both func have the same input. 
*Problem:* 1 Function can have multiple requirements -> 1 service responsible for 2 many thing and difficult to understand.  
![[Pasted image 20261018175155.png]]
![[Pasted image 20261018175223.png]]
*Solution:* Decoration allow u to *add behavior without changing the core components.*
![[Pasted image 20261018175258.png]]
![[Pasted image 20261018175028.png]]

And Decoration can be turn on/off depending what the application need.![[Pasted image 20261018175350.png]]
![[Pasted image 20261018173908.png]]
-> The point is, make each Operation Clear and Independant -> Easier to Understand -> Simplier and Cleaner code. 
+ $ Extend without modifying core component.

**CQRS** - only apply if yoru application have Read Request inbalance throughput from Write Request -> seperate infrastructure to optimize price.
![[Pasted image 20261018175827.png]]
![[Pasted image 20261018175849.png]]


**Circuit Breaker Pattern** - Adaptive failure handling with auto retry for failed service. If service fail then open to blocked service from affacting more resource, check working with half-open and closed if working again.  
![[Pasted image 20261018175950.png]]
Resilient
![[Pasted image 20261018180117.png]]



**Overall** - Not to remember all of the Pattern but to *Understand what problem each Pattern solved and when to apply them* -> core idea.
![[Pasted image 20261018180203.png]]
Allow your system to be Scalable (with added features, dev, serv, traffic increase everyday), Understandable, Testable and reliable.
![[Pasted image 20261018180223.png]]



