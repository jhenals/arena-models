# Abstract

Traffic congestion at toll plazas is a recurring issue on major Philippine expressways. This project applies discrete-event simulation using ARENA to model, analyze, and optimize the operations of a cash-based toll gate inspired by real Philippine driving experience. Starting from a classical M/M/1 queueing model, the system is progressively extended to reflect heterogeneous vehicle behavior and peak-hour arrival patterns. Several design alternatives are evaluated using simulation, and an optimization is carried out through the Indifference Zone (Nakayama) approach to identify the most effective toll booth configuration under uncertainty.

# 1. Introduction

Traffic congestion is a major issue on Philippine expressways, where toll gate operations often contribute to delays, especially during peak periods. Delays at toll plazas not only increase travel time but also lead to fuel waste, increased emissions, and driver frustration. Improving the efficiency of toll gate systems is therfore an important operational and societal concern. 

Toll gate operations are inherently stochastic in nature. Vehicles arrive at unpredictable time, service durations vary dynamically depending on payment method and driver behavior, and queues form dynamically based on traffic demand and available toll booths. These characteristics make discrete-event simulation a powerful and appropriate tool for modeling, analyzing, and improving toll gate performance. 

This project focuses on the modeling and simulation of a Philippine toll gate system, particularly the SOuth Luzon Expressway (SLEX), using Arena simulation software. The system is represented as a queuing process in which vehicles arrive randomly, wait in line if necessary, receive service at a toll booth, and then depart. The model captures key real-world features of toll operations, including random arrivals, variable service times, and first-come-first-served discipline. 

Beyond system modeling, the project emphasizes performnace evaluation and optimization. Several alternative toll gate configurations are implemented and compared using statistically sound methods, such as, Nakayama ranking-and-selection procedure and he indifference zone approach. There methods are employed to indetify configurations that provide meaningdul improvments in system performance while accounting for randomness and simulation variablity. Performance measures such as average waiting time, queue length, system time, and resource utilization are analyzed to support decision-making. 

The objective of this study is to identify configurations that lead to meaningful performance improvements, demonstrating how simulation-based optimization can support better operational decisions for toll gate systems on SLEX.

_Toll gates play a critical role in managing traffic flow on expressways such as SLEX and NLEX. Despite infrastructure improvements, congestion at toll plazas remains common, particularly during peak hours. Traditional analytical queueing models provide insight but are often insufficient to capture the variability and operational complexity observed in real systems. Discrete-event simulation offers a flexible and powerful approach to study such systems under realistic assumptions.

The objective of this project is to model a Philippine toll gate using ARENA simulation software, analyze its performance under different operating conditions, and determine an optimal configuration using statistical ranking and selection methods._

# 2. System Description and Assumptions

The system under study is a single-direction toll plaza serving vehicles arriving from an expressway segment. The following assumptions are made:

- Vehicle arrivals follow a Poisson process
- Service times are exponentially distributed
- Vehicles are served on a First-Come First-Served (FCFS) basis
- Adequate queue space is available
- The system operates continuously with no breakdowns

These assumptions are consistent with classical queueing theory and are reasonable approximations of observed toll gate behavior in the Philippines.
