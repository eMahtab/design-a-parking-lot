# Design a parking lot

## System Requirements :

1. The parking lot should have multiple floors where customers can park their cars.

2. The parking lot should have multiple entry and exit points.

3. Customers can collect a parking ticket from the entry points and can pay the parking fee at the exit points on their way out.

4. The system should not allow more vehicles than the maximum capacity of the parking lot.

5. Each parking floor will have many parking spots. The system should support multiple types of parking spots such as Compact, Large, Handicapped, Motorcycle, etc.

6. The Parking lot should have some parking spots specified for electric cars. These spots should have electric sockets through which customers can charge their vehicles.

7. The system should support parking for different types of vehicles like car, truck, van, motorcycle, etc.



# VehicleType and ParkingSpotType
```java
public enum VehicleType {
  CAR, TRUCK, ELECTRIC, VAN, MOTORBIKE
}

public enum ParkingSpotType {
  HANDICAPPED, COMPACT, LARGE, MOTORBIKE, ELECTRIC
}
```

# Vehicle class and its subtypes
```java
public abstract class Vehicle {
  private String licenseNumber;
  private final VehicleType type;
  private ParkingTicket ticket;

  public Vehicle(VehicleType type) {
    this.type = type;
  }

  public void assignTicket(ParkingTicket ticket) {
    this.ticket = ticket;
  }
}

public class Car extends Vehicle {
  public Car() {
    super(VehicleType.CAR);
  }
}

public class Van extends Vehicle {
  public Van() {
    super(VehicleType.VAN);
  }
}

public class Truck extends Vehicle {
  public Truck() {
    super(VehicleType.TRUCK);
  }
}

// Similarly we can define classes for Motorcycle and Electric vehicles etc.

```

# ParkingSpot
```java
public abstract class ParkingSpot {
  private String number;
  private boolean free;
  private final ParkingSpotType type;
  private Vehicle vehicle;

  public boolean isFree() {
      return free;
  }

  public ParkingSpot(ParkingSpotType type) {
    this.type = type;
  }

  public boolean assignSpot(Vehicle vehicle) {
    this.vehicle = vehicle;
    free = false;
  }

  public boolean freeSpot() {
    this.vehicle = null;
    free = true;
  }
}

public class HandicappedSpot extends ParkingSpot {
  public HandicappedSpot() {
    super(ParkingSpotType.HANDICAPPED);
  }
}

public class CompactSpot extends ParkingSpot {
  public CompactSpot() {
    super(ParkingSpotType.COMPACT);
  }
}

public class LargeSpot extends ParkingSpot {
  public LargeSpot() {
    super(ParkingSpotType.LARGE);
  }
}

public class MotorbikeSpot extends ParkingSpot {
  public MotorbikeSpot() {
    super(ParkingSpotType.MOTORBIKE);
  }
}

public class ElectricSpot extends ParkingSpot {
  public ElectricSpot() {
    super(ParkingSpotType.ELECTRIC);
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
