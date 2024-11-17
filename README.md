# Design a parking lot

## System Requirements :

1. The system should support parking for different types of vehicles like car, truck, van, motorcycle, etc.

2. The parking lot should have multiple floors where customers can park their cars.

3. Each parking floor will have many parking spots. The system should support multiple types of parking spots such as Compact, Large, Handicapped, Motorcycle, etc.

4. The parking lot should have multiple entry and exit points.

5. Customers can collect a parking ticket from the entry points and can pay the parking fee at the exit points on their way out.

6. The system should not allow more vehicles than the maximum capacity of the parking lot.

7. The Parking lot should have some parking spots specified for electric cars. These spots should have electric sockets through which customers can charge their vehicles.


# VehicleType and ParkingSpotType
```java
public enum VehicleType {
  CAR, TRUCK, ELECTRIC, VAN, MOTORBIKE
}

public enum ParkingSpotType {
  MOTORBIKE, HANDICAP, MEDIUM, LARGE, OVERSIZE, ELECTRIC
}
```

# VehicleType with hourly rate and required parking spot
```java
public enum VehicleType {
   MOTORBIKE(15.0, ParkingSpotType.MOTORBIKE), 
   CAR(25.0, ParkingSpotType.MEDIUM), 
   VAN(30.0, ParkingSpotType.LARGE), 
   TRUCK(40.0, ParkingSpotType.OVERSIZE), 
   ELECTRIC(25.0, ParkingSpotType.ELECTRIC);
	
   private final double hourlyRate;
   private final ParkingSpotType requiredParkingSpot;

   VehicleType(double hourlyRate, ParkingSpotType requiredParkingSpot) {
       this.hourlyRate = hourlyRate;
       this.requiredParkingSpot = requiredParkingSpot;
   }

   public double getHourlyRate() {
       return hourlyRate;
   }
	
   public ParkingSpotType getRequiredParkingSpot() {
       return requiredParkingSpot;
   } 
}
```

# Vehicle class
```java
public class Vehicle {
    private String licenseNumber;
    private VehicleType vehicleType;
	
    public Vehicle(String licenseNumber, VehicleType vehicleType) {
        this.licenseNumber = licenseNumber;
        this.vehicleType = vehicleType;
    }
    public String getLicenseNumber() {
        return licenseNumber;
    }
    public VehicleType getVehicleType() {
        return vehicleType;
    }
}
```

# ParkingSpot
```java
class ParkingSpot {
	private String parkingSpotId;
	private final ParkingSpotType parkingSpotType;
    private boolean isFree;
    private Vehicle vehicle;

    public ParkingSpot(ParkingSpotType spotType) {
        this.parkingSpotType = spotType;
        this.isFree = true;
        this.vehicle = null;
    }
    public ParkingSpot(String parkingSpotId, ParkingSpotType spotType) {
    	this.parkingSpotId = parkingSpotId;
        this.parkingSpotType = spotType;
        this.isFree = true;
        this.vehicle = null;
    }
    public boolean park(Vehicle vehicle) {
        if (!isFree || vehicle.getVehicleType().getRequiredParkingSpot() != parkingSpotType) {
            return false; // Can't park if occupied or vehicle type doesn't match the spot type
        }
        this.isFree = false;
        this.vehicle = vehicle;
        return true;
    }
    public void freeParkingSpot() {
        isFree = true;
        vehicle = null;
    }
    public String getParkingSpotId() {
		return parkingSpotId;
	}
    public boolean isFree() {
        return isFree;
    }
    public ParkingSpotType getParkingSpotType() {
        return parkingSpotType;
    }
    public Vehicle getVehicle() {
        return vehicle;
    }
}
```

# ParkingFloor
```java
public class ParkingFloor {
  private String name;
  private HashMap<String, HandicappedSpot> handicappedSpots;
  private HashMap<String, CompactSpot> compactSpots;
  private HashMap<String, LargeSpot> largeSpots;
  private HashMap<String, MotorbikeSpot> motorbikeSpots;
  private HashMap<String, ElectricSpot> electricSpots;

  public ParkingFloor(String name) {
    this.name = name;
  }
}  
```

# ParkingLot
```java
public class ParkingLot {
    private String name;
    private HashMap<String, ParkingFloor> parkingFloors;
  
    // private constructor to restrict for singleton
    private ParkingLot() {
    
    }

    // singleton ParkingLot to ensure only one object of ParkingLot in the system
    private static class ParkingLotHolder {
        private static final ParkingLot INSTANCE = new ParkingLot();
    }
  
    // static method to get the singleton instance of ParkingLot
    public static ParkingLot getInstance() {
        return ParkingLotHolder.INSTANCE;
    }
  }
  
```

# References :
https://www.educative.io/courses/grokking-the-object-oriented-design-interview/gxM3gRxmr8Z
