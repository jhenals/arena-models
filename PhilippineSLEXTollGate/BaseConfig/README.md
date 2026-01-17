# 3. Version 1: Single Cash Booth (Base Simulation Model)

## 3.1 Model Description

The baseline model represents a single cash toll booth. Vehicles arrive at an average rate of 10 vehicles per hour, corresponding to a mean interarrival time of 6 minutes. The toll collector serves one vehicle at a time, with a mean service time of 3 minutes.

## 3.2 Arena Implementation

- Entity: Vehicle
    
    ![image.png](attachment:e6cf2e6b-b305-4f6d-bfb5-3e4584b830f7:image.png)
    
- Resource: Toll Booth Collector
    
    ![image.png](attachment:59768d42-37ae-49b2-9ab8-506b84d22103:image.png)
    
- CREATE module: Exponential interarrival time (mean = 6 minutes)
    
    ![image.png](attachment:92b0ac85-5790-4f22-8f98-1f082b517a57:image.png)
    
- PROCESS module: Seize–Delay–Release with exponential service time (mean = 3 minutes)
    
    ![image.png](attachment:6e8f1bee-f135-4a26-9706-d66a2e3aa671:image.png)
    
- DISPOSE module: Vehicle exits the system
    
    ![image.png](attachment:85f41e2a-99ce-47fc-99dc-90a11ef72d5b:image.png)
    

## 3.4 Warm Up Identification

In reality, a toll-plaza is a non-terminating system. This means:

- Vehicles arrive continously
- No natural “end” of a day in a steady-state analysis
- The system does not start empty in reality

However, in ARENA:

- Simulation always start empty
- This creates an initialization bias

Early results underestimate:

- Queue length
- Waiting time
- Resource utilization

Therefore, we must discard the transient period

### What the Warm-Up period means

The warm-up period is the initial portion of the simulation during which: 

- The system is still “warming up”
- Statistics are not representative of steady state

We must find a time $T_0$ such that:

> After $T_0$, perfomance measures stabilize.
> 

### Performance Measure to Use

For our SLEX toll syste, we want to choose a slowly stabilizing metric

> Average Number of Vehicles in the Queue
> 

![image.png](attachment:9afcde76-348d-4fd1-8649-2c04ff8a25c5:image.png)

### Initial Replication Parameters

- Replications: 1
- Length 10000 minutes
- No Warm up
    
    ![image.png](attachment:d0b1c524-ef9d-405d-b1a4-ffedbde50e2e:image.png)
    

### Graph Result

![image.png](attachment:3ba39f51-b5c6-4c10-80b2-34e26d893245:image.png)

Observed Behavior



|      Time (minutes)                | Observation                                |
| ---------------------------------  | ------------------------------------------ |
| 0 → ~500-600 (Initial Rise)        | Strong upward trend                        |
| ~600 → ~5440 (Fluctuating plateau) | Lq rises then fluctuates                   |
| ~5440 onward (Gradual settling)    | The curve flattens and oscillates slightly |

> Rough estimate: $T_0=5440 mins$ is the warm up period. After this, Lq fluctutates around a steady mean.

## 3.5 Experimental Design with Warm Up Period

![image.png](attachment:fea97343-bfb5-4487-b8ed-8914f71b9ba6:image.png)

### Replication Parameter

![image.png](attachment:0ba29371-83d4-4a2a-96d3-bef96bfa5f60:image.png)

### Graph

![image.png](attachment:b47d608b-8d51-47e5-93db-b93512500667:image.png)

## 3.3 Performance Measures

- Average waiting time in queue
- Average total time in system
- Average number of vehicles in queue
- Resource utilization


## 3.4 Result Analysis
