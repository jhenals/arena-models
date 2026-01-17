# 4. Version 2: Extended Platoons and Vehicles (Extended Model with Heterogeneous Vehicles and Compound Arrival Model)

## 4.1 Heterogenous Vehicles

In real tollways, vehicles differ in type and service requirements. We can categorize them as:

| Class    | Vehicle Type        | Proportion | Service Time Distribution | Notes            |
| -------- | ------------------- | ---------- | ------------------------- | ---------------- |
| Class 0  | Motorcycle (<400cc) | 5%         | Exponential, mean 1.5 min | Quick service    |
| Class 1  | Car, SUVs, Jeepneys | 70%        | Exponential, mean 3 min   | Standard service |
| Class  2 | Buses, Light Trucks | 10%        | Exponential, mean 5 min   | Longer service   |
| Class 3  | Heavy Trucks        | 15%        | Exponential, mean 6 min   | Longer service   |

![image.png](attachment:56ab0fd4-673a-44d1-9f98-a62cceba4d83:image.png)

Implementation Notes in Arena

- Define a **vehicle entity** attribute `VehicleType`.
- When an entity arrives, assign type randomly based on proportions.
    
    ![image.png](attachment:e3d24363-79dd-4b59-8a78-3ef4f6c06f70:image.png)
    
- Set service time according to vehicle type.
    
    ![image.png](attachment:ce1d748c-178c-4e16-b207-066f6155c603:image.png)
    
    An ASSIGN module is used to attribute each vehicle a type based on a discrete probability distribution. Service time in the PROCESS module is conditioned on vehicle type using indexed variables.
    
    ![image.png](attachment:018b9213-572a-4b6a-ad84-eb2c25fc8a39:image.png)
    
    Replication Parameters
    
    ![image.png](attachment:da14293d-767f-46b3-bc6f-e2a8673fedbf:image.png)
    
    Graph
    
    ![image.png](attachment:de851db7-314b-4bc9-86df-301f54ad9fe9:image.png)
    

### Result

## 4.2 Compound Arrival Model

Vehicle arrivals were modeled using a compound arrival process to reflect platooned traffic conditions commonly observed on SLEX. Each arrival event generates a random-sized batch of vehicles, which is subsequently separated into individual entities before entering the toll booth queue. This approach increases arrival variability while preserving the average flow rate.

**Steps to model:**

1. **Batch arrivals**: Define a batch entity that represents a group of vehicles.
2. **Batch size distribution**: Use a discrete distribution for number of vehicles per batch:
    - 1 vehicle → 40%
    - 2 vehicles → 30%
    - 3 vehicles → 20%
    - 4 vehicles → 10%
    
    Arena Translation: 
    
    ![image.png](attachment:127702cf-a5c3-4d26-bad1-b6350cd99e3f:image.png)
    
3. **Inter-batch arrival time**: Keep as exponential (Poisson process) with mean adjusted for expected total vehicle arrival rate.
    
    ![image.png](attachment:2703d262-6d4e-49a8-845e-fe0a558e9c65:image.png)
    

**Benefits of this approach:**

- Captures **rush hour platoons** (vehicles arriving in groups).
- More realistic queue dynamics and toll booth utilization.
