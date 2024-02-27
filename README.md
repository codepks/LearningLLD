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

source : https://youtu.be/3qRBeZsUa18?si=5YRJHl9rdpYwts2E

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

source: https://youtu.be/l1OCmsBnQ3g?si=yPN1TWhqxDNve9_B

# System Design

## Scaling from Zero to Million

**Single Server** 

A very naive project would have a application and db in a single server catering to a client. This method is non-scalable.

*client <--> (application_server + databse_server)*

**Application & DB Server Application**

The next step to above is DB server and application server separation.
So now you have a client, connected to an Application server which in turn is connected to a database server.

*client <--> application_server <--> databse_server*

**Load Balancer + Multiple app servers**

Here we will have load balancer to manager the client request traffic to different servers.
Load balancer brings security as it communicates privately to app servers.

*client <--> Load_balancer <--> (Appliation server1 + server 2 .. server N) <--> database_server*

**Database replication**

In case of database scaling we have Master DB and Slave DB (replicas of Master DB) concept being used here.
We generally perform the write operation in master DB and read operation from the slave DB.
In case our Master DB fails, then one of the slave DB would promote  itseld to be the master DB.

*client <--> Load_balancer <--> (Appliation server1 + server 2 .. server N) 
                                      <--> ( Master database_server + Slave  database_server)*
                                      
**Cache**

Application server directly doing DB operation everytime can be expensive.
We can introduce Cache which is checked by Application servers before querying the DB servers.
If data not found on Cache then DB is queried.

*client <--> Load_balancer <--> (Appliation server1 + server 2 .. server N) <--> Cache
                                      <--> ( Master database_server + Slave  database_server)*

**CDN**

*Content Delivery Network*
In case of scaling to different countries, your local server might have more latency for far off countries.
CND can do caching and has other functionalities too.
In such cases we will put CDN nodes local to other countries which will provide Caching service to static data like HTML pages, video , CSS pages etc.

If a country's CDN node is unable to find the requested data then it searches for it in the nearby CND node.
If it is not found there also then it will request the original server.
CND is also secure making original server reach farther.
Client request goes to CDN first before going to load balancer

*client <-->  Load_balancer <--> CDN Nodes <--> (Appliation server1 + server 2 .. server N) <--> Cache
                                      <--> ( Master database_server + Slave  database_server)*

**Data Center**

This is again multiple countries based. The load balancer would be at international level.
Suppose you have entire setup as above in India and USA and USA's server fails then the entire traffic of USA would be diverted to India's  by the load balancer.
USA's traffic can find their data in India's data due to data replication.

**Messaging Queue**

Application servers can queue their message to let their subscriber services use it.
In case of failure the data can be retrieved from requeue.

**Database Scaling**

Two types:
1. Vertical : Increase the power and capability of the servers, but there's a limit to it
2. Horizontal : Sharding

*Sharding*
1. Horizontal Sharding : Different rows of data table can be distributed R1 - R100, R101 - R200. Columns would be same across. This is a better option
2. Vertical Sharding : We will do division based on Columns.


source : https://youtu.be/rExh5cPMZcI?si=n9FejS8rNdHguw31

# High Level Design
## Design a URL Shortner

**Requirement Analysis**

A URL shortner should be able to generate shorter URL for a long URL.
Also, the application should be able to retrieve longer version URL from the shorter URL (happens when you click the shorter URL).

**Question 1**<br>
How much shorter should our url be? 7 letter or 8 letters?

**Answer**<br>
For this we need to understand the traffic first.
Ask the interviewer over the daily traffic for url shortening request.
Let's assume it to be 1 million users per day. That means 365 million requests per year.
If our url expires after 10 years then it would be 3650 million requests over 10 years.
So our URL shortening word should be able to suffice us with minimum 3.65 billion combinations.

Now, if we go with 7 character(only smallcase alphabets) long url then there would be 26^7 = 8.03 billion combinations which is sufficient for our case.
So, a 7 character long url would be sufficient for us.

If interviewer says that we can use smallcase(26), uppercase(26) and numbers(10) = 62 characters then combination possibilties would change drastically. IT can give us 3.5 trillion combinations for 7 character long letters.

**Question 2** <br>
Which algorithm should be used in this case?

**Answer**<br>
So what are doing here is basically generating hash values and there are different algorithms to do that for us.
Two ways : 
1. Using hash function : MD5, SHA-1
2. Base62 encoding

> Hash Generator <br>

MD5 : Generates characted length of 32 character but our requirement is only 7 character lenth
SHA-1 : It will generate a 40 character length but requirement is only 7 character length

Althoug we can trim, but there is a possibility that initial 7 characters might be same for many strings.
So Hash generator wouldn't be an appropriate choice.

> Base62 <br>

It converts decimal to base62 value.
It takes a-z(26), A-Z(26), 0-1 (10) = 62 unique characters

provices upto 3.5 trillion combination for 7 character long code

> Database <br>

Relational db can work here as the system is not very complex.
It can have this schema:
long url <--> short url <--> expire time
**Question** <br>
The question is how database management happens when it is getting updated by different application servers? We have to make sure we are generating unique id in distributed systems(distributed application servers and db servers).

**Answer** <br>
Problems : 
1. Single db server is difficult to scale
2. Multiple db servers are difficult to synchronize
3. *Ticket Server* : Centralized autoincrement service. Having once common databse server which will be updated by multiple appliation server. But this is a single point failure design

Methods to deal with this :
1. *Snowflake* : Having multiple db servers with 41 bit metadata attached to data. 1 bit reserved, few bits on Timestamp, few bits on machine id  (basically which machine is writing data), followed by sequence number. So now if two machines try to write at the same type then at least their machine id would be different.
2. *Zookeeper* :
     *Distributed application can coordinate with each other reliably. The method is able to generate unique ids in distributed envrionment*
      Of 3.5 trilloin combinations, we will divide the database into certain ranges (for e.g. 1 million each) of the given combinations. This will make the application server get allocated their range and avoid duplicacy.

    combination range1 ... combination range2... combination range3... combination range4... combination range5... combination range6 ...... combination rangeN
Our application will now talk directly to zookeeper to generate or read the values.

Since the data entries could be in millions, therefore we need to have multiple servers which can be achieved via horizontal sharding.
To avoid single point failure we need to have backup servers too which can be achieved via master slave concept.

**Cache**
The read operation i.e. opening of the long url from the short url invokes read function which might be 10x used than the write operation and would be frequent. For this case we need to have cache for those frequently used database entries.




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
  

## Meeting Scheduler

**Problem Statement**
Design a Meeting Scheduler where you need to book a meeting room for a given time period for given attendees(optional) and block the room in room's calender.
Make the design scalable to open the scope for further development.

**Process Flow**
1. A user enters given time and day and capacity of a meeting in a meeting scheduler
2. Meeting Scheduler talks to Meeting Room manager holding the properties of different rooms and return the list of rooms accordingly
3. User choses the room being dummy in nature with properties like location and capacity gets choses by Meeting Scheduler and blocks the room in the meeting room calender.
4. Notify the users


**Object Oriented Analysis**
1. **User** class with company id and name as properties. This is public to company. We can make company id as protected member. 

```
struct User
{
  string name;
protected:
  int company_id;

User(string iName, int iCompany_id) : name(iName), company_id(iCompany_id) {};
};
```

2. **MeetingRoom** : They will have properties like capacity, Location 

```
struct MeetingRoom
{
  int capacity;
  struct Location
  {
    int floorNo;
    int roomNo;
    Location(int iFloorNo, int iRoomNo) : floorNo(iFloorNo), roomNo(iRoomNo) {};
  } location;

  MeetingRoom(Location iLocation, int iCapacity) : location(iLocation), capacity(iCapacity) {};
};

//initiliazed like
MeetingRoom obj{ {19,03},5};
```
3. **BookingTime** : This calender datastructure would be part of MeetingRooms which will hold the booking data. Its usage can be extended to users too.

```
class BookingTime
{
  int date;
  time startTime;
  time endTime;

public:
  void setDate() {};
  void setStartandEndTime() {};

  int getDate() {};
  time getStartTime() {};
  time getEndTime() {};
}
```

4. **SmartRoom** : A smartRoom uses MeetingRoom class and Calender class and has the functionality of availability. Similar functionality can be extended to users too.

class SmartRoom
{
  MeetingRoom room;
  list<BookingTime> bookingTimes = BookingTime();
public:
  book isAvailable(time startTime, time endTime) {};
}


4. **MeetingRoomManager** : This is a singleton class as there wouldn't be multiple managers, also making it thread safe. This is the smarter version of dummy meeting rooms which manages them and returns the list of meeting rooms based on request. It will compose of list of MeetingRoom object, Calender Object.
*Functionalities* : Should give me list of rooms when queried for rooms with given capacity and time slots.

```
MeetingRoomManager
{
  list<SmartRoom> roomList;//to be initialized with the list

public:
  list<SmartRoom> getListOfAvailableRoom(time startTime, time endTime, int capacity)
	{ //check through list for isAvailable()}
  void block
}
```

5. **Meeting** : A unique id of the meeting along with room details, and users involved will be availble here
   
```
class Meeting
{
  int id;
  list<User> userList;
  MeetingRoom bookeRoom;  
}
```

6. **Meeting Scheduler** : It is a singleton class. Should book the meetings and block the room for unavailability.

```
MeetingScheduler
{
  MeetingRoomManager manager;
  Meeting id = NULL;
  list<Users> userList;
  public:
  list<SmartRoom> getRoomList(time startTime, time endTime, int capacity) {};
  Meeting blockTheRoom(SmartRoom, time startTime, time endTime) {//Returns meeting object};
  void NotifyUsers(list<User> userList) {// notify id.usersList}
}
```

7. **Calender** : This is a different application which will stick to individual rooms and display a room's data-wise availability;

```
Calender
{
  //TODO - need to discuss with the client on its requirement
}
```

## Design an Online Printer Website

**Problem Statement** 
1. User want to get some print-outs which includes set of low quality and high quality prints
2. He requests quotation from the Printer's website entering the number of low quality and high quality prints to get the price quotation along with the delivery date and time
3. If user is okay with the time taken then he is proceed further to place the request by payment
4. The Printer's shop consists of m HQ printers and n LQ printers. They take the order and optimize the job by putting maximum printers on work and block the printers as per calculated time

**High Level Design**
1. Since this is not a data intesive job, there by only few application servers can do the job with each server having x users handling capacity at a time
2. The other servers can also servers can also serve as backup servers. The traffic deviation in case of one server's power outtage can be handled by a Load Balancer
3. Again, since the job is not data intensive we will have few database servers, in this case one Master database and one Slave database. We will write to Master database, it will be replicated to Slave database. In case of Master server's outtage, the slave Server will turn into the Master database.
4. In this design we do not require any Cache system too as there woulnd't me much traffic at a time
5. The Application can have Admin Portal too to check the availability of the printers and to check the orders

**Quotation Generation**
Our quotation generation depends upon the status of printers and depends upon these factors:
1. If printers are fully available : Easy to estimate
2. If printers are partially available : Check how printers are getting free with time to put them on job 
3. If none of the printers are available : Check when they would be available to put them on job

**Object Oriented Analysis**
1. We need **User** and order details in the website to generate the quotation
   
```
class User
{
  string userName;
  int userId;
  struc Address
  {
    string city;
    int pincode;
    int flatno;
  } address;
}
```
2. We need to have the **Order-Request** object which will have a variable to flag it to be processed or unprocessed

```
class Order {
public:
  int id;
  int num_high_quality_prints;
  int num_low_quality_prints;
  bool processed = false;
  Time order_placed_time;
  Time estimated_completion_time;

  // Function to calculate estimated completion time based on printer availability and print times
  void calculate_estimated_completion_time();

  // Other functions like update database with order details, etc. (Implement based on your needs)
};
```
3. **PrinterClass** : Has printer Interface and printerType

```
struct Printer
{
  int printerId;
  bool isBusy;
  time bookedStartTime;
  time bookedEndTime;
}
```

4. **PrinterManager** : It is a singleton class. Manages the printing job and taken into account the printer's busy schedule.

```
class PrinterManager {
private:
  vector<Printer> low_quality_printers;
  vector<Printer> high_quality_printers;
  int total_high_quality_printers;
  int total_low_quality_printers;
  int high_quality_print_time; // in minutes
  int low_quality_print_time;  // in minutes

public:
  PrinterManager(int total_lq_printers, int total_hq_printers, int hq_print_time, int lq_print_time)
      : total_low_quality_printers(total_lq_printers),
        total_high_quality_printers(total_hq_printers),
        high_quality_print_time(hq_print_time),
        low_quality_print_time(lq_print_time) {}

  int get_available_high_quality_printers();
  int get_available_low_quality_printers();

```

5. **OrderProcessor** : Takes in the incoming request and updates the duration of the order, converts the order request to processed order.

```
class OrderProcessor {
private:
  PrinterManager* printer_manager;

public:
  OrderProcessor(PrinterManager* manager) : printer_manager(manager) {}
  void process_order(Order& order); // Function to process an order
  // Other functions for database interaction and notification (Implement based on your needs)
};
```

6. **Main Operation**

```
// User details
  User user;
  user.name = "Prashant";
  user.id = 232;
  user.address = "Pune, 411057, 1903";

  // Order details
  Order order;
  order.id = 1; // Generate actual order ID here
  order.num_high_quality_prints = 6;
  order.num_low_quality_prints = 4;
  order.order_placed_time.set_current_time(); // Set current time when order is placed

  // Order processing
  PrinterManager printer_manager(12, 8, 5, 3); // Initialize with printer details
  OrderProcessor order_processor(&printer_manager);

  if (order.processed) {
    order_processor.process_order(order);

    // Update database with order details, estimated completion time, etc. (Implement based on your needs)
  }

  return 0;
```
   



