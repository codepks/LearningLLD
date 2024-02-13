# LearningLLD

# Parking Lot

```
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


////===============================Parking lot singleton class//===============================

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


//===============================Interface class for vehicle===============================

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


//===============================Payment Processors===============================
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

//===============================Payment Processors===============================

//================================================================================
int main(){
	ParkingLot &mA = ParkingLot::getParkingLotInstance();
}

```
