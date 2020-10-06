# A Guide for LTE (FDD) Capacity Dimensioning

---



## Introduction 
**(_Please read this section to understand if this guide is relevant to you_)**

---




- This text is aimed to understand the **LTE-FDD** capacity calculations.
- The text shall try to explain how a future Traffic forecast can be translated into required resources based on **Statistics** and Observations from current Live Network.
- Essentially, this is a **practical dimensioning** exercise and **not** a theoretical one. Difference in both approaches is that for theoretical discussion, we assume a certain overhead for all our channels and work towards building the right model, whereas for practical dimensioning we shall get all these inputs via the available Statistics and plugin to our model to get desired results.

---


- I shall try to divide this text into **three** parts.
    1. Theoretical background and calculations **Read if you want to extend this documentation and want to gain a deeper understanding on the rationale**
    2. Defining required input statistics **Read if you are planning to use this model for your own network**
    3. Practical Modelling **Just read directly if you dont want to go theoretical and would like to check the practical impact only**


---

## Theoretical Background

In simplest term, we shall work our way to list down the overall available resources for a LTE cell and then negate all the overheads we can think of. Later we shall see how to combine this with the certain efficiencies / inefficiencies introduced by the Scheduler to gain an understanding of the overall capacity. 

    Capacity = f_thr(AvailableRBs) - ControlOverheads - CustomizedConfigs

    where,
        f_thr is a function of available RBs which provides what is the expected throughput considering all RF Conditions, Users, Resources etc.
        
        ControlOverheads are the overheads due to all control channels and bits like PCFICH, PSS, SSS, PBCH, DMRS, SRS etc.

        CustomizedConfigs are some additional configurations which may reduce capacity further by allocating more control resources or enabling other features such as PRS

## Time Domain

| Parameter              | Normal Cyclic   Prefix | Extended Cyclic   Prefix | Comments                                                            |
|------------------------|------------------------|--------------------------|---------------------------------------------------------------------|
| Frame (ms)             | 10                     | 10                       | Our main unit for   time                                            |
| SubFrame (ms)          | 1                      | 1                        |                                                                     |
| Slot (ms)              | 0.5                    | 0.5                      | 2 slots in a subframe                                               |
| Symbol per   Slot (nb) | 7                      | 6                        |                                                                     |
| Symbol (us)            | 71.429                 | 83.333                   | 7 symbols in a slot   (Normal CP), 6 symbols per slot (Extended CP) |
| Sampling Time   (ns)   | 32.55208333            | 32.55208333              | Sampling time   considering 2048 sample size                        |

## Frequency Domain


| Parameter                  | Value       | Comments                 | Comments                                                            |
|----------------------------|-------------|--------------------------|---------------------------------------------------------------------|
| Subcarriers   per RB       | 12          |                          | Our main unit for   time                                            |
| Subcarrier   Spacing (KHz) | 15          |                          |                                                                     |
| Total RB BW   (KHz)        | 180         | 12 subcarriers *   15KHz | 2 slots in a subframe                                               |
| Symbol per   Slot (nb)     | 7           | 6                        |                                                                     |
| Symbol (us)                | 71.429      | 83.333                   | 7 symbols in a slot   (Normal CP), 6 symbols per slot (Extended CP) |
| Sampling Time   (ns)       | 32.55208333 | 32.55208333              | Sampling time   considering 2048 sample size                        |

## RB vs. RE

A Resource Block (RB) is the smallest unit which can be allocated to a user. A Resource Element is the smallest unit representing physical layer data. Our overheads are generally calculated in terms of Resource Elements and then overall number of wasted RBs are gathered to understand overall system throughput capacity.

| Parameter          | Frequency   Domain (Khz) | Time Domain   Symbols (Normal vs. Extended CP) | Time Domain   (ms) (Normal vs. Extended) |
|--------------------|--------------------------|------------------------------------------------|------------------------------------------|
| Resource Block     | 180                      | 7 or 6                                         | 0.5 for both                             |
| Resource   Element | 15                       | 1 or 1                                         | 71.4us or 83.33us                        |

## Bandwidth vs. RBs

Another table just for the sake of completeness. Below refers to the number of RBs vs. LTE total bandwidth. As you can see that the number of RBs does not cover the entire bandwidth and the rest of the frequency is mainly used as the guard band on the edge of the spectrum.

| Bandwidth | Resource Blocks |
|:---------:|:---------------:|
| 1.4 MHz   | 6               |
| 3 MHz     | 15              |
| 5 MHz     | 25              |
| 10 MHz    | 50              |
| 15 MHz    | 75              |
| 20 MHz    | 100             |

## Overhead Calculations

### CSI-RS (Cell specific reference signals)

These symbols are the easiest to undestand as they provide the reference mathematical equations for the user. These are used by the UE to determine the RSRP and channel conditions. A specific number of REs are transmitted as CSI-RS and based on number of Antenna ports, there are certain REs on certain Antenna ports which remain as unused. The below table can be used to calculate this overhead.

| Bandwidth | Antenna Port | Number of CSR-RS RE(or unused) per SubFrame   per RB | Total RBs | Number of CSR-RS RE per Frame per RB | Total Number of CSI-RS(or unused)  per Frame |
|-----------|--------------|------------------------------------------------------|-----------|--------------------------------------|----------------------------------------------|
| 1.4 MHz   | 1            | 8                                                    | 6         | 80                                   | 480                                          |
| 1.4 MHz   | 2            | 16                                                   | 6         | 160                                  | 960                                          |
| 1.4 MHz   | 4            | 24                                                   | 6         | 240                                  | 1440                                         |
| 3 MHz     | 1            | 8                                                    | 15        | 80                                   | 1200                                         |
| 3 MHz     | 2            | 16                                                   | 15        | 160                                  | 2400                                         |
| 3 MHz     | 4            | 24                                                   | 15        | 240                                  | 3600                                         |
| 5 MHz     | 1            | 8                                                    | 25        | 80                                   | 2000                                         |
| 5 MHz     | 2            | 16                                                   | 25        | 160                                  | 4000                                         |
| 5 MHz     | 4            | 24                                                   | 25        | 240                                  | 6000                                         |
| 10 MHz    | 1            | 8                                                    | 50        | 80                                   | 4000                                         |
| 10 MHz    | 2            | 16                                                   | 50        | 160                                  | 8000                                         |
| 10 MHz    | 4            | 24                                                   | 50        | 240                                  | 12000                                        |
| 15 MHz    | 1            | 8                                                    | 75        | 80                                   | 6000                                         |
| 15 MHz    | 2            | 16                                                   | 75        | 160                                  | 12000                                        |
| 15 MHz    | 4            | 24                                                   | 75        | 240                                  | 18000                                        |
| 20 MHz    | 1            | 8                                                    | 100       | 80                                   | 8000                                         |
| 20 MHz    | 2            | 16                                                   | 100       | 160                                  | 16000                                        |
| 20 MHz    | 4            | 24                                                   | 100       | 240                                  | 24000                                        |

### PBCH (Physical Broadcast Channel)

PBCH will use 72 subcarriers and will utilize slot1 symbol 0, 1, 2, 3. For the REs which should transmit CSI-RS in these areas, they shall not be mapped for PBCH. So, that overhead should be subtracted to not count it twice (once as CSI-RS and then as PBCH).


| Item | Frequency   Domain (subcarriers) | Time domain   (symbols per Frame) | Total RE for   PBCH | CSI-RS RE in the same region (4ports)| Only PBCH RE   Overhead (per Frame) |
|------|----------------------------------|-----------------------------------|---------------------|--------------------------------------|-------------------------|
| PBCH | 72                               | 4                                 | 288                 | 48                                   | 240                     |

### PSS/SSS (Synchronization Signals)

They are the PCI carriers. Everyone knows about them, other signals want to be them. They also occupy 72 subcarriers (they use 62 and keep 10 as their buffer of unused RE) and in time domain they occur once in a half-frame. (5ms period).

| Item | Overhead RE in 1 radio Frame (includes   unused unused RE on the edge) |
|------|------------------------------------------------------------------------|
| PSS  | 144                                                                    |
| SSS  | 144                                                                    |

### PCFICH (Physical Control Format Indicator Channel)

It uses 16 REs on symbol 0 for 1st slot of the subframe. It treats the CSI-RS space as unused but treats the CSI symbol locations as if used Antenna ports were greater than 1. So an additional overhead per frame should be considered for this case as mentioned in below table.

| Item   | Total RE in a SubFrame | Total RE in a Frame | Increased unused RE in case of Antenna Port   = 1 per Frame | Comments                                                                                                                                                                                                                                         |
|--------|------------------------|---------------------|-------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PCFICH | 16                     | 160                 | 40                                                          | the additional 40 wastage should be only considered if Antenna ports =1.   As PCIFCH interleaves the CSR-RS symbols as if Antenna ports used were 2 or 4   and does not use those RE positions. These then remain unused for the PCIFH   symbols |


### PHICH (Physical HARQ Indicator Channel)

This is the guy which indicates ACK / NACK and is for individual user. It uses symbol 0 slot 0 for all subframes and uses subcarriers depending on the system bandwidth.

| Item  | Bandwidth | PHICH REGs per subframe | PHICH RE per subframe | PHICH RE per Radio Frame |
|-------|-----------|-------------------------|-----------------------|--------------------------|
| PHICH | 1.4 MHz   | 3                       | 12                    | 120                      |
| PHICH | 3 MHz     | 6                       | 24                    | 240                      |
| PHICH | 5 MHz     | 12                      | 48                    | 480                      |
| PHICH | 10 MHz    | 21                      | 84                    | 840                      |
| PHICH | 15 MHz    | 30                      | 120                   | 1200                     |
| PHICH | 20MHz     | 39                      | 156                   | 1560                     |

### PDCCH (Physical Downlink Control Channel)

This is where things get interesting. The PDCCH is dynamic and varies based on cell load, selected aggregation level etc. So we have a tough choice to make while assuming the overheads of PDCCH if we want to limit our analysis on theoretical settings alone. One way of doing this can be to select the number of CFIs and then calculate the PDCCH overheads. Another way can be to get the available statistics from the Network and interpolate based on CFI usage and aggregation levels used in the sites/areas.

