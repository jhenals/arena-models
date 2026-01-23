
# Introduction
Traffic congestion is a persistent problem on Philippine expressways, where toll gate operations often contribute significantly to delays, especially during peak travel periods. Congestion at toll plazas increases travel time, fuel consumption, emissions, and driver frustration. Consequently, improving the operational efficiency of toll gate systems is an important engineering and societal concern.

Toll gate operations are inherently stochastic. Vehicle arrivals are random, service times vary depending on payment method and driver behavior, and queues form dynamically as a function of traffic demand and available service capacity. These characteristics make discrete-event simulation a suitable and powerful tool for modeling, analyzing, and improving toll gate performance.

This project focuses on the modeling, simulation, and optimization of a Philippine toll gate system, with particular emphasis on the South Luzon Expressway (SLEX), using the ARENA simulation software. The toll gate is modeled as a queuing system in which vehicles arrive randomly, wait in queue if necessary, receive service at a toll booth, and then depart. The model captures key real-world features, including random arrivals, heterogeneous service times, and a first-come, first-served (FCFS) service discipline.

Beyond system modeling, this study emphasizes performance evaluation and optimization. Several alternative toll gate configurations are implemented and compared using statistically rigorous methods, including the Indifference Zone (IZ)–based ranking-and-selection procedure proposed by Bechhofer and Nakayama. These methods identify system configurations that produce meaningful performance improvements while accounting for randomness and simulation variability. Performance measures such as average waiting time, queue length, system time, and resource utilization are analyzed to support data-driven decision-making.

The primary objective of this project is to identify toll gate configurations that significantly improve operational performance, demonstrating how simulation-based optimization can support better operational decisions for SLEX toll plaza management.

# System Description and Assumptions
The system under study is a single-direction toll plaza serving vehicles arriving from an expressway segment. The following assumptions are made:

- Vehicle arrivals follow a Poisson process
- Service times are exponentially distributed
- Vehicles are served on a First-Come First-Served (FCFS) basis
- Adequate queue space is available
- The system operates continuously with no equipment breakdowns

These assumptions are consistent with classical queueing theory and are reasonable approximations of observed toll gate behavior in the Philippines.

# Baseline Configuration
## Model Description
The baseline configuration captures the essential operational characteristics of a Philippine toll plaza while maintaining sufficient simplicity for performance evaluation and comparison. The system consists of a toll plaza with **two cash toll collectors**.

Vehicle arrivals follow a Poisson process with an average arrival rate of **10 vehicles per hour**, corresponding to an exponential interarrival time with a mean of **6 minutes**. To reflect realistic SLEX traffic conditions, the model incorporates heterogeneous vehicle classes, each with distinct service-time distributions, while preserving FCFS service discipline.
| Class   | Vehicle Type        | Proportion | Service Time Distribution | Notes            |
| ------- | ------------------- | ---------- | ------------------------- | --------------   |
| Class 0 | Motorcycle (<400cc) | 5%         | Exponential, mean 1.5 min | Quick service    |
| Class 1 | Car, SUVs, Jeepneys | 70%        | Exponential, mean 3 min   | Standard service |
| Class 2 | Buses, Light Trucks | 10%        | Exponential, mean 5 min   | Longer service   |
| Class 3 | Heavy Trucks        | 15%        | Exponential, mean 6 min   | Longer service   |

## Arena Implementation
* Entity: Vehicle
* Resource: Toll Collector,
    Capacity: 1
* Create: Create Vehicle Arrival,
    Entities per Arrival: 1
* Prcoess: Process Pay Toll,
    Logic Action: Seize Delay Release,
    Delay Type: Expression
    Expression: DISC(0.05, 0, 0.75, 1, 0.85, 2, 1, 3 )
* Dispose: Exit Toll Plaza
* Decide and Record Module (to record the number of each type of vehicles that exits the toll plaza)

# Enhanced Model
To better represent real-world SLEX conditions, the baseline model is extended with three major features:

1. Compound Arrival
2. Time-dependent staffing schedules
    NB: This means that vehicle arrival should follow a time-dependent arrival schedule to capture daily traffic fluctuations.
3. Multiple toll lanes (Cash and RFID)

## Compound Arrival
Vehicle arrivals are modeled using a compound arrival process to reflect platooned traffic conditions commonly observed on SLEX. Each arrival event generates a random-sized batch of vehicles, which is subsequently separated into individual entities before entering the toll booth queue. This approach increases arrival variability while preserving the average flow rate.

### Batch Size distribution
- 1 vehicle: 60%
- 2 vehicles: 25%
- 3 vehicles: 15%

## Time-Dependent Staffing and Arrival Schedule
### Peak vs Off-Peak Periods

- **Peak hours:**
    - Morning: 5:00 – 9:00
    - Evening: 16:00 – 20:00
- **Off-peak hours:**
    - All other times

### Time-Varying Vehicle Arrival Schedule
| Period | Time (hrs) | Mean Interarrival Time (min) |
| --- | --- | --- |
| Late Night | 0–5 | 8.0 |
| Morning Peak | 5–9 | 3.0 |
| Midday | 9–16 | 7.0 |
| Evening Peak | 16–20 | 3.0 |
| Night | 20–24 | 9.0 |

### Number of Staffs

- **Peak season:** 2 cash toll collectors
- **Off-peak:** 1 cash collector

### Staffing Schedule 
| Time (hour) | Period | Cash Collectors |
| --- | --- | --- |
| 0–5 | Late night (Off-peak) | 1 |
| 5–9 | Morning peak (peak) | 2 |
| 9–16 | Midday (off-peak) | 1 |
| 16–20 | Evening peak (peak) | 2 |
| 20–24 | Night (off-peak) | 1 |

## Multiple Lanes
Two types of toll lanes are modeled:

1. **Cash Lane**
    - Resource: Cash Toll Collector
    - Capacity: Time-dependent schedule
2. **RFID Lane**
    - Resource: RFID Lane
    - Capacity: 1
    - Shorter service time than cash transactions

At arrival, vehicles are assigned a payment type:

- 70% Cash
- 30% RFID

## Arena Implementation
** Compound Arrival **
1. Create: Create Vehicle Batch Arrival
   Time Between Arrivals Time: Schedule
   Schedule Name; Vehicle Arrival Schedule
** Staff Scheduling **
2. Schedule Name: Cash Toll Collector Schedule

** Multiple Lane**
3. Resource Name: Cash Toll Collector
    Type: Based on Schedule
    Schedule Name: Cash Toll Collector Schedule
4. Resource Name: RFID Lane
    Type: Fixed Capacity
    Capacity: 1
5. Decide
    Name: Decide Lane Routing Based on Payment Method
    TRUE: Cash Toll path
    False: RFID Lane path
6. Record (to record the number of vehicles in Cash Lane and in RFID lanes)

# Warm Up Identification
In reality, a toll-plaza is a non-terminating system. This means:

- Vehicles arrive continously
- No natural “end” of a day in a steady-state analysis
- The system does not start empty in reality

However, ARENA simulations always start empty, introducing initialization bias that underestimates waiting times, queue lengths, and utilization during early simulation periods.
To mitigate this bias, a warm-up period is discarded. The warm-up duration was identified using the **average waiting time in queue** as the performance metric, since it stabilizes relatively slowly.

### Initial Replication Parameters

- Replications: 1
- Length 10000 minutes
- No Warm up

## Graph
<img width="1064" height="424" alt="4" src="https://github.com/user-attachments/assets/bcdb2633-ddf5-44a1-bb95-cbde2c846a9a" />

Rough estimate: $T_0≈6000-6400$ minutes is the warm-up period. After this, Wq fluctutates around a steady mean.

## Final Experimantal Parameters
Replication Parameters
<img width="647" height="296" alt="5" src="https://github.com/user-attachments/assets/83e10020-d8ff-473b-b0ff-2fdf578438f5" />

Graph
<img width="1069" height="420" alt="6" src="https://github.com/user-attachments/assets/6d409912-ffc4-4a0a-8cba-08ac30a18dbd" />

## Result
Overall system performance improvement
| Metric | Baseline  | Enhanced |
| --- | --- | --- |
| Avg vehicle waiting time | 5.6646 min | 0.31373 min |
| Avg total time in system | 9.2426 min | 1.8971 min |
| Avg queue length | 0.9194 veh | 0.00849(RFID)+ 0.00367(Cash) **≈ 0.01 veh** |
| Max Waiting time | 55.789  min | 8.4933 min |

- The enhanced model achieves a dramatic reduction in waiting time, indicating that the intervention is highly effective.
- Service-time variability is neutralized through electronic tolling. RFID implementation significantly improves toll plaza efficiency under low-to-moderate traffic demand.
- RFID vehicles experience near free-flow conditions (Avg Waiting time: 0.32 minutes (~19 seconds), Avg Service time: 1.10  minutes, Avg total time: 1.42 min)
- Even cash users benefit significantly due to reduced competition from RFID- equipped vehicles. (Avg Waiting Time: 0.30 minutes, Avg service Time: 2.61 min, Avg Total Time: 2.91min)

# Optimization Using Indifference Zone Approach
Due to the stochastic nature of vehicle arrivals, batch behavior, heterogeneous service times, and mixed payment modes, it is not possible to determine the best toll plaza configuration analytically. Multiple operational policies—such as adding an RFID lane or increasing the number of cash toll collectors—produce different performance outcomes under uncertainty. Therefore, a simulation-based optimization approach was implemented to identify the most operationally efficient configuration with statistical confidence.

## Problem Formulation
The optimization problem consists of selecting the toll plaza configuration that minimizes average vehicle waiting time while maintaining efficient utilization of available resources. The decision variables include the number of RFID lanes and the number of active cash toll collectors, subject to a maximum of three collectors.

## Performance Measure 
The primary performance measure used for optimization is the **average vehicle waiting time** in the system. This metric directly reflects congestion and overall service quality experienced by drivers. Secondary performance indicators, such as resource utilization and throughput, were used to support the interpretation of results but not for system selection.

Because we want to **minimize waiting time**, but the algorithm selects the **maximum**, define:

<aside>
💡

$X_i=$-(Average Waiting Time of System $i$)

</aside>

## Competing ALternatives
Three alternatives were evaluated:

-  **Alternative A (Baseline)**: 1 RFID lane, 2 cash collectors
-  **Alternative B**: 2 RFID lanes, 2 cash collectors
-  **Alternative C**: 1 RFID lane, 3 cash collectors

## Simulation Experiment Design
### Step 1: Parameter Selection
**Probability of correct selection** $P_c = 0.95$
**Indifference Zone Parameter:**
\delta is the minimum practically significant difference between the best system’s mean performance (e.g. average Wait time $W_q$ and the second-best. The gaps/differences below this are considered insignificant. 

I selected $n_0=10$ pilot runs to estimate reliably before sampling.
The key gap $\mu_3-\mu_1=1.406358-1.281516=0.124842$ falls in the preference zone if $\delta \le 0.124842$. 

**Critical Constant**
Based on the table from Bechhofer (1954), c_{3,0.95}=2.7101

**Number of alternative systems** $k=3$
### 2. Determine Sample Size N
Bechhofer’s formula

$$
N=\dfrac{c^2 \sigma^2}{\delta^2}
$$

where:

- $c$ is a constant depending on $k$  and $P_c$ (from Bechhofer tables)
- $\sigma$ is the variance of the performance measure (Waiting Time)

$$
N=\dfrac{(2.7101)^2(1.4512)^2}{(0.1248)^2}=123.9043=124
$$

### Step 3: Run N Replications for each System
In Arena:

- Set **replications = N=124**
- Keep **all parameters identical**
- Use same warm-up and run length

### Step 4: Compute Sample Mean
For each system 

$$
\bar X_i=\dfrac{1}{N}\sum^N_{j=1}X_{ij}
$$

NB: This is the negative waiting time

In our Arena model,
<img width="436" height="43" alt="image" src="https://github.com/user-attachments/assets/b69c0efa-be8a-471c-b9f7-0f18f3e25738" />

### Step 5: Select Best System

$$
i^*=argmax\{ \bar X_i\}
$$

$$
i^*=argmax\{1.02,0.79,1.35\}
$$

Because we are looking for the

> Minimum average waiting time
> 

$$
i^*=argmin\{-1.02,-0.79,-1.35\}
$$

Thus,

$$
i^*=3
$$

Alternative 3 yields the minimum average waiting time

# Result
Alternative C shows stable, low-congestion performance across 124 replications. 
The Cash Toll processed 45 vehicles on average (95% CI: 39.63-50.28) with high wait time variability (accumulative wait 287.1 min total). Meanwhile, RFID Lane handles 105 vehicles efficiently (95% CI: 92.41-117.5) with low utilization (10.55%) and minimal accumulated wait (92.8 min total).
Despite the accumulated wait variability (Cash: 287.1 min, HW=219 min; RFID: 92.8 min, HW=29 min), a per-vehicle analysis confirms System C's preference zone superiority through the Vehicle.WaitTime:

System C( X3=1.35) vs System A (X1=1.02):
X3-X1=0.33 > δ=0.12 ✓ Preference Zone

System C( X3=1.35) vs System B (X2=0.79): 
X3-X2=0.56 >> δ=0.12 ✓ Strong Preference 

# Conclusion
The results indicate that increasing the number of cash toll collectors during peak hours provides the most significant reduction in congestion under the assumed traffic composition. While additional RFID lanes improve performance, congestion in the cash lane remains the dominant contributor to system delay. 
Through the application of the Indifference Zone (IZ) ranking-and-selection procedure, this selection is validated with the specified confidence level, accounting for stochastic variability across replications. 


