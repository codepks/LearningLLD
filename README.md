# LearningLLD

# Concepts
## Networking protocol

7 layers of communication. Important ones:
1. Application Layer : HTTP
2. Transport Layer : TCP

> Application Layer

**Client Server Protocol:**
1. HTTP : Unidirectional : client sends request and server sends response
2. FTP
3. SMTP
4. Web Sockets : Bidirectionals : client and server both can send request and get response. Generally used in case of messaging apps like whatsapp. SErver also keeps on querying for messages from the client

**Peer to Peer**
WebRTC : Client can talk to server, server to client and client to client. Uses UDP.


> Transport Layer

**TCP** : The packets are send and acknowledgement is received. If it is not recieved then that packet is sent back. Ordering of packets is there.

**UDP** : No acknowledgement and ordering but it is fast. Used in case of video calling.


## CAP Thoeram

C : Consistency
A : Availability
P - Partition Tolerance

Desirable property of distributed system with replicated data.

We cannot use all three in a distributed system.

C : The data read is consistent across distributed systems
A : The systems should be available anytime
P : If the linkage between the systems breaks anytime, the systems should still conitue to operate

CA: The system data can be consistent and available.
CP: Here breakage means one system is down and you are interacting with one data only so the consistency is still maintained
AP : The system is available with partition tolerance but consistency wouldn't be there.

What would you tradeoff between C, A, and P
We cannot tradeoff with Partition, we can only tradeoff between consistency and availability.

source : https://www.youtube.com/watch?v=3qRBeZsUa18&list=PL6W8uoQQ2c63W58rpNFDwdrBnq5G3EfT7&index=3

## Microservices

There are two types of architecture: Monolithic and Microservices

**Monolithic** : All end to end functionalities are available in one architecture only. Sometime such architectures are legacy softwares.
Issues with it:
1. It overloads IDE and IDE becomes slow as the size of code is too huge
2. Scaling is very hard
3. Tight coupled application
4. Even in small changes all the code needs to be retested

**Microservices**:
The best way to modularize your code like, they are loosely coupled too :
1. Product service
2. Order service
3. Billing service
4. etc

A bug can be resolved in that particular module and tested.
Component level scaling is also good.

Disadvantages:
1. If they are not loosely coupled then there would be too much communication involved
2. There could be latency issue
3. The dependent components need to be monitored

Different phases of Microservices creation
> Decomposition
1. Decompose by business capability
2. Decompose by subdomain

> Database
1. Would you have separate database for each service or common for all

> Communication
1. API communication
2. via Events

> Integration
1. These microservices need to be integrated to the services like UI

**Decomposition Pattern**
1. Business capability - order management, product management, account management, billing etc like MS's
2. Domain driven design : Divide application into domains which can be divided into microservices in it. For e.g. Payment is a domain which can have microservices like forward payment, reverse payment (refund)

**Strangler Pattern**

This is basically used for refactoring a monolithic service to a microservice.
We start building micorservice and start strangulating the monolithic service by reducing the API traffic one by one until our microservice is ready.

**SAGA Pattern**

*Sequence of Local Transaction*
It is use for database management.
Suppose different microservices are using different databases but there is one type of transaction that uses all these three databases, to manage such case we use saga pattern by still maintaining ACID property of each database.

Caution : No service can query other services's DB.
Different services can have different types of DB, could be SQL and no-SQL etc.

*Example*
MS1 : Order, MS2 : Inventory , MS3 : Payment
Suppose trasaction passes MS1, MS2 but fails at MS3.
As per ACID property, either all should pass or none. It should roll back (MS2 and MS1 should roll back).

*Solutin*
All the services would be linked by event handling, once any of the transaction fails, it sends back event of failure to the previous microservice to roll back.


**CQRS Pattern**

Suppose DB1 of MS1 has table 1-5, DB2 of MS2 has table 6-10,DB3 of MS3 has table 11-15.
There is query which utilizes table 6 of DB2 and table 12 of DB3. The join operation of query is handled by this pattern.

This DB will have a common DB which will have data of all smaller DBs from different services, but this DB will have only read services and will be updated based on event linking from different services.

source: https://youtu.be/l1OCmsBnQ3g?list=PL6W8uoQQ2c63W58rpNFDwdrBnq5G3EfT7

# System Design

## Scaling from Zero to Million

**Single Server** 

A very naive project would have a application and db in a single server catering to a client. This method is non-scalable.

client <--> (application_server + databse_server)

**Application & DB Server Application**

The next step to above is DB server and application server separation.
So now you have a client, connected to an Application server which in turn is connected to a database server.

client <--> application_server <--> databse_server

**Load Balancer + Multiple app servers**

**Database replication**

**Cache**

**CDN**

**Data Center**

**Messaging Queue**

**Database Scaling**






# Low Level Design
## Parking Lot

Elements to work with
1. Vehicles : types :  Heavy vehicles, buses, SUVs, cars, bikes
2. Payment mode : UPI, CC, DB, Wallet
3. Strategy
4. Printers
5. CCTVs
6. Terminals
7. Parking ticket

  **Class Design**
1. Parking lot would be a singleton class
2.  Elements like payment processors, strategy class,
3.  printers, terminals would be a coming from factory class
4.  CCTVs would be under the observer class to notify in ase of issue
5.  Vechiles would be coming from the factory class
  

