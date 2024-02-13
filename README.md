# LearningLLD

# Parking Lot

```
#include <iostream>

/*
* Elements to work with
* 1. Vehicles : types :  Heavy vehicles, buses, SUVs, cars, bikes
* 2. Payment mode : UPI, CC, DB, Wallet
* 3. Strategy
* 4. Printers
* 5. CCTVs
* 6. Terminal
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


//Parking lot singleton class

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


int main(){
	ParkingLot &mA = ParkingLot::getParkingLotInstance();
}


```
