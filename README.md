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

# ParkingTicket
```java
class ParkingTicket {
    private final Vehicle vehicle;
    private final long entryTime;
    private long exitTime;

    public ParkingTicket(Vehicle vehicle) {
        this.vehicle = vehicle;
        this.entryTime = System.currentTimeMillis();
    }

    public double calculateCharge() {
        long duration = ((exitTime - entryTime) / 1000) / 60; // in minutes
        double hours = duration / 60.0;
        return hours * vehicle.getVehicleType().getHourlyRate();
    }

    public void exit() {
        this.exitTime = System.currentTimeMillis();
    }
}
```

# ParkingLevel
```java
import java.util.ArrayList;
import java.util.EnumMap;
import java.util.List;
import java.util.Map;

class ParkingLevel {
    private String name;
    private int levelNumber;
    private Map<ParkingSpotType, List<ParkingSpot>> parkingSpots;

    public ParkingLevel(String name, int levelNumber) {
    	this(levelNumber);    	
    	this.name = name;
    }
    public ParkingLevel(int levelNumber) {
        this.levelNumber = levelNumber;
        parkingSpots = new EnumMap<>(ParkingSpotType.class);
        // Initialize parking spots for each type
        for (ParkingSpotType type : ParkingSpotType.values()) {
            parkingSpots.put(type, new ArrayList<>());
        }
    }
    public String getName() {
        return name;
    }
    public int getLevelNumber() {
        return levelNumber;
    }
    public void addParkingSpot(ParkingSpot parkingSpot) {
        parkingSpots.get(parkingSpot.getParkingSpotType()).add(parkingSpot);
    }
    public boolean parkVehicle(Vehicle vehicle) {
        List<ParkingSpot> spots = parkingSpots.get(vehicle.getVehicleType().getRequiredParkingSpot());
        for (ParkingSpot spot : spots) {
            if (spot.isFree()) {
                return spot.park(vehicle);
            }
        }
        return false; // No available spots for this vehicle
    }
    public boolean removeVehicle(Vehicle vehicle) {
        for (List<ParkingSpot> spots : parkingSpots.values()) {
            for (ParkingSpot spot : spots) {
                if (!spot.isFree() && spot.getVehicle().equals(vehicle)) {
                    spot.freeParkingSpot();
                    return true;
                }
            }
        }
        return false;
    }
    public List<ParkingSpot> getFreeParkingSpots(ParkingSpotType type) {
        List<ParkingSpot> availableSpots = new ArrayList<>();
        for (ParkingSpot parkingSpot : parkingSpots.get(type)) {
            if (parkingSpot.isFree()) {
                availableSpots.add(parkingSpot);
            }
        }
        return availableSpots;
    }
}
```

# ParkingLot
```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class ParkingLot {
    private String id;
    private String name;
    private Map<Integer, ParkingLevel> parkingLevels;
	
    public ParkingLot(int numLevels) {
        parkingLevels = new HashMap<>();
        for (int i = 1; i <= numLevels; i++) {
            parkingLevels.put(i, new ParkingLevel(i));
        }
    }
    public String getId() {
		return id;
    }
    public String getName() {
		return name;
    }
    public Map<Integer, ParkingLevel> getParkingLevels() {
		return parkingLevels;
    }
	
    public boolean parkVehicle(Vehicle vehicle) {
        for (ParkingLevel level : parkingLevels.values()) {
            if (level.parkVehicle(vehicle)) {
                return true; // Vehicle parked successfully
            }
        }
        return false; // No available spots
    }
    public boolean removeVehicle(Vehicle vehicle) {
        for (ParkingLevel level : parkingLevels.values()) {
            if (level.removeVehicle(vehicle)) {
                return true; // Vehicle removed successfully
            }
        }
        return false; // Vehicle not found
    }
    public List<ParkingSpot> getFreeParkingSpots(ParkingSpotType type) {
        List<ParkingSpot> freeParkingSpots = new ArrayList<>();
        for (ParkingLevel level : parkingLevels.values()) {
        	freeParkingSpots.addAll(level.getFreeParkingSpots(type));
        }
        return freeParkingSpots;
    }
    public void addParkingSpot(int levelNumber, ParkingSpot spot) {
        ParkingLevel level = parkingLevels.get(levelNumber);
        if (level != null) {
            level.addParkingSpot(spot);
        } else {
            System.out.println("Invalid level number: " + levelNumber);
        }
    }
}
```

## Testing the Code
```java
import java.util.List;
import net.mahtabalam.entity.ParkingLevel;
import net.mahtabalam.entity.ParkingLot;
import net.mahtabalam.entity.ParkingSpot;
import net.mahtabalam.entity.ParkingSpotType;
import net.mahtabalam.entity.Vehicle;
import net.mahtabalam.entity.VehicleType;

public class Main {
    public static void main(String[] args) {
        ParkingLot lot = new ParkingLot(3); // 3 levels in the parking lot

        // Add some parking spots
        lot.addParkingSpot(1, new ParkingSpot(ParkingSpotType.MOTORBIKE));
        lot.addParkingSpot(1, new ParkingSpot(ParkingSpotType.MEDIUM));
        lot.addParkingSpot(2, new ParkingSpot(ParkingSpotType.LARGE));
        lot.addParkingSpot(3, new ParkingSpot(ParkingSpotType.OVERSIZE));

        Vehicle car = new Vehicle(VehicleType.CAR);
        Vehicle truck = new Vehicle(VehicleType.TRUCK);

        // Try to park a car
        boolean parkedCar = lot.parkVehicle(car);
        System.out.println("Car parked: " + parkedCar);

        // Try to park a truck
        boolean parkedTruck = lot.parkVehicle(truck);
        System.out.println("Truck parked: " + parkedTruck);

        // Get available spots for a MOTORBIKE
        List<ParkingSpot> availableMotorbikeSpots = lot.getFreeParkingSpots(ParkingSpotType.MOTORBIKE);
        System.out.println("Available MOTORBIKE spots: " + availableMotorbikeSpots.size());

        // Access a specific level
        ParkingLevel level2 = lot.getParkingLevels().get(2);
        System.out.println("Level 2 available spots for LARGE vehicles: " + level2.getFreeParkingSpots(ParkingSpotType.LARGE).size());

        // Remove the truck
        boolean removedTruck = lot.removeVehicle(truck);
        System.out.println("Truck removed: " + removedTruck);
    }
}
```

# References :
https://www.educative.io/courses/grokking-the-object-oriented-design-interview/gxM3gRxmr8Z
