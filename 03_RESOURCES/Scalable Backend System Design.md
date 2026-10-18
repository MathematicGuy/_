 ![[Pasted image 20261018171754.png|254]]


![[Pasted image 20261018172121.png|318]]

Business Logic (Service Class) => For *Application Main Feature Logic Management.*
Data Logic Layer (Repository Class) => For *Data Management (change diff database flexibly from SQL/NoSQL).*

**Factory Pattern:** Object Creation in 1 Place. 
![[Pasted image 20261018172727.png]]
![[Pasted image 20261018172751.png]]


**Singleton Pattern:** for thing that should be centralize and have 1 single consistant instance. 
![[Pasted image 20261018172818.png]]

![[Pasted image 20261018172914.png]]

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

**The Builder Pattern**
