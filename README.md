# LearningLLD

# Concepts
## Networking protocol

7 layers of communication. Important ones:
1. Application Layer : HTTP
2. Transport Layer : TCP

========Application Layer==========
Client Server Protocol:
1. HTTP : Unidirectional : client sends request and server sends response
2. FTP
3. SMTP
4. Web Sockets : Bidirectionals : client and server both can send request and get response. Generally used in case of messaging apps like whatsapp. SErver also keeps on querying for messages from the client

Peer to Peer
WebRTC : Client can talk to server, server to client and client to client. Uses UDP.


========Transport Layer==========
TCP : The packets are send and acknowledgement is received. If it is not recieved then that packet is sent back. Ordering of packets is there.

UDP : No acknowledgement and ordering but it is fast. Used in case of video calling.


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

Monolithic : All end to end functionalities are available in one architecture only. Sometime such architectures are legacy softwares.
Issues with it:
1. It overloads IDE and IDE becomes slow as the size of code is too huge
2. Scaling is very hard
3. Tight coupled application
4. Even in small changes all the code needs to be retested
5. 




https://youtu.be/l1OCmsBnQ3g?list=PL6W8uoQQ2c63W58rpNFDwdrBnq5G3EfT7

# Parking Lot


/*
* Elements to work with
* 1. Vehicles : types :  Heavy vehicles, buses, SUVs, cars, bikes
* 2. Payment mode : UPI, CC, DB, Wallet
* 3. Strategy
* 4. Printers
* 5. CCTVs
* 6. Terminals
* 7. Parking ticket
*
*  =======================Class Design===========================
*	Parking lot would be a singleton class
*
*	Elements like payment processors, strategy class,
*	printers, terminals would be a coming from factory class
*
*	CCTVs would be under the observer class to notify in ase of issue
*
*	Vechiles would be coming from the factory class
*
*
*
*/


////===============================Parking lot singleton class===============================
```
class ParkingLot{
public:

	static ParkingLot& getParkingLotInstance()	{
		return parkingLot_;
	}
	
	~ParkingLot() = default;

private:
	ParkingLot() = default;
	ParkingLot(const ParkingLot& obj) = delete;

	static ParkingLot parkingLot_;
};

ParkingLot ParkingLot::parkingLot_;
```



//===============================Interface class for vehicle===============================

```
class IVehicle
{
public:

	virtual std::string getType() = 0;
};

//---------------------------------------------------

class Car : public IVehicle
{
public:
	Car() = default;

	std::string getType() override	{ return "Car";	}
};

//---------------------------------------------------

class Truck : public IVehicle
{
public:
	Truck() = default;

	std::string getType() override { return "Truck"; }
};

//---------------------------------------------------

class Bike : public IVehicle
{
public:
	Bike() = default;

	std::string getType() override { return "Bike";	}
};
```

//===============================Payment Processors===============================

```
class PaymentProcessor
{
public:

	virtual std::string getType() = 0;
	virtual void Process() = 0;
};

//---------------------------------------------------

class UPIPay : public PaymentProcessor
{
public:

	std::string getType() override	{ return "UPIPay"; }

	void Process() override { printf("Processing UPI"); }
};

//---------------------------------------------------

class CreditCard : public PaymentProcessor
{
public:

	std::string getType() override { return "CreditCard"; }

	void Process() override { printf("Processing CreditCard"); }
};

//---------------------------------------------------

class Wallet : public PaymentProcessor
{
public:

	std::string getType() override { return "Wallet"; }

	void Process() override { printf("Processing Wallet"); }
};
```
//===============================Payment Processors===============================

```
//================================================================================
int main(){
	ParkingLot &mA = ParkingLot::getParkingLotInstance();
}

```
