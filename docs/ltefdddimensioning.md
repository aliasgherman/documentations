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

### Time Domain

| Parameter                | Normal   Cyclic Prefix | Extended   Cyclic Prefix | Comments                                                            |
|--------------------------|------------------------|--------------------------|---------------------------------------------------------------------|
| Frame   (ms)             | 10                     | 10                       | Our main   unit for time                                            |
| SubFrame   (ms)          | 1                      | 1                        |                                                                     |
| SubFrame   in Frame (nb) | 10                     | 10                       |                                                                     |
| Slot   (ms)              | 0.5                    | 0.5                      | 2 slots in   a subframe                                             |
| Slot   in Frame (nb)     | 20                     | 20                       |                                                                     |
| Symbol   per Slot (nb)   | 7                      | 6                        |                                                                     |
| Symbol   per Frame (nb)  | 140                    | 120                      |                                                                     |
| Symbol   (us)            | 71.429                 | 83.333                   | 7 symbols   in a slot (Normal CP), 6 symbols per slot (Extended CP) |
| Sampling   Time (ns)     | 32.55208333            | 32.55208333              | Sampling   time considering 2048 sample size                        |

### Frequency Domain


| Parameter                  | Value       | Comments                 | Comments                                                            |
|----------------------------|-------------|--------------------------|---------------------------------------------------------------------|
| Subcarriers   per RB       | 12          |                          | Our main unit for   time                                            |
| Subcarrier   Spacing (KHz) | 15          |                          |                                                                     |
| Total RB BW   (KHz)        | 180         | 12 subcarriers *   15KHz | 2 slots in a subframe                                               |


### RB vs. RE

A Resource Block (RB) is the smallest unit which can be allocated to a user. A Resource Element is the smallest unit representing physical layer data. Our overheads are generally calculated in terms of Resource Elements and then overall number of wasted RBs are gathered to understand overall system throughput capacity.

| Parameter          | Frequency   Domain (Khz) | Time   Domain Symbols (Normal vs. Extended CP) | Time   Domain (ms) (Normal vs. Extended)   |
|--------------------|--------------------------|------------------------------------------------|--------------------------------------------|
| Resource   Block   | 180                      | 7 or 6                                         | 0.5 for   both                             |
| Resource   Element | 15                       | 1 or 1                                         | 71.4us or   83.33us                        |
| RE in 1 RB         | 12                       | 7 or 6                                         | 1RB = 84RE (Normal CP), 72RE (Extended CP) |

Additionally, control channels are grouped together as CCEs which have the following mapping

| Item                          | Number of REs | Comments              |
|-------------------------------|---------------|-----------------------|
| Resource Element Group (REG)  | 4             | 1 REG = 4 RE          |
| Control Channel Element (CCE) | 36            | 1 CCE = 9 REG = 36 RE |

### Bandwidth vs. RBs

Another table just for the sake of completeness. Below refers to the number of RBs vs. LTE total bandwidth. As you can see that the number of RBs does not cover the entire bandwidth and the rest of the frequency is mainly used as the guard band on the edge of the spectrum.

| Bandwidth | Resource Blocks |
|:---------:|:---------------:|
| 1.4 MHz   | 6               |
| 3 MHz     | 15              |
| 5 MHz     | 25              |
| 10 MHz    | 50              |
| 15 MHz    | 75              |
| 20 MHz    | 100             |

### Overhead Calculations

#### CSI-RS (Cell specific reference signals)

These symbols are the easiest to undestand as they provide the reference mathematical equations for the user. These are used by the UE to determine the RSRP and channel conditions. A specific number of REs are transmitted as CSI-RS and based on number of Antenna ports, there are certain REs on certain Antenna ports which remain as unused. The below table can be used to calculate this overhead.

| Bandwidth (Mhz) | Antenna Port | CSIRS/Subframe/RB | Total RBs | CSIRS/Frame/RB | Total CSIRS/Unused RE in Frame |
|-----------|--------------|------------------------------------------------------|-----------|--------------------------------------|----------------------------------------------|
| 1.4   | 1            | 8                                                    | 6         | 80                                   | 480                                          |
| 1.4   | 2            | 16                                                   | 6         | 160                                  | 960                                          |
| 1.4   | 4            | 24                                                   | 6         | 240                                  | 1440                                         |
| 3      | 1            | 8                                                    | 15        | 80                                   | 1200                                         |
| 3      | 2            | 16                                                   | 15        | 160                                  | 2400                                         |
| 3      | 4            | 24                                                   | 15        | 240                                  | 3600                                         |
| 5      | 1            | 8                                                    | 25        | 80                                   | 2000                                         |
| 5      | 2            | 16                                                   | 25        | 160                                  | 4000                                         |
| 5      | 4            | 24                                                   | 25        | 240                                  | 6000                                         |
| 10     | 1            | 8                                                    | 50        | 80                                   | 4000                                         |
| 10     | 2            | 16                                                   | 50        | 160                                  | 8000                                         |
| 10     | 4            | 24                                                   | 50        | 240                                  | 12000                                        |
| 15     | 1            | 8                                                    | 75        | 80                                   | 6000                                         |
| 15     | 2            | 16                                                   | 75        | 160                                  | 12000                                        |
| 15     | 4            | 24                                                   | 75        | 240                                  | 18000                                        |
| 20     | 1            | 8                                                    | 100       | 80                                   | 8000                                         |
| 20     | 2            | 16                                                   | 100       | 160                                  | 16000                                        |
| 20     | 4            | 24                                                   | 100       | 240                                  | 24000                                        |

#### PBCH (Physical Broadcast Channel)

PBCH will use 72 subcarriers and will utilize slot1 symbol 0, 1, 2, 3. For the REs which should transmit CSI-RS in these areas, they shall not be mapped for PBCH. So, that overhead should be subtracted to not count it twice (once as CSI-RS and then as PBCH).


| Item | Frequency (subcarriers) | Time (symbols per Frame) | Total RE for   PBCH | CSI-RS RE overlaps (4ports)| Only PBCH RE   Overhead (per Frame) |
|------|----------------------------------|-----------------------------------|---------------------|--------------------------------------|-------------------------------------|
| PBCH | 72                               | 4                                 | 288                 | 48                                   | 240                                 |

#### PSS/SSS (Synchronization Signals)

They are the PCI carriers. Everyone knows about them, other signals want to be them. They also occupy 72 subcarriers (they use 62 and keep 10 as their buffer of unused RE) and in time domain they occur once in a half-frame. (5ms period).

| Item | Overhead RE in 1 radio Frame (includes   unused unused RE on the edge) |
|------|------------------------------------------------------------------------|
| PSS  | 144                                                                    |
| SSS  | 144                                                                    |

### PCFICH (Physical Control Format Indicator Channel)

It uses 16 REs on symbol 0 for 1st slot of the subframe. It treats the CSI-RS space as unused but treats the CSI symbol locations as if used Antenna ports were greater than 1. So an additional overhead per frame should be considered for this case as mentioned in below table.

**The additional 40 wastage should be only considered if Antenna ports =1.   As PCIFCH interleaves the CSR-RS symbols as if Antenna ports used were 2 or 4   and does not use those RE positions. These then remain unused for the PCIFH   symbols**

| Item   | Total RE/SubFrame | Total RE/Frame | Added unused RE if SISO/Frame |
|--------|------------------------|---------------------|-------------------------------------------------------------|
| PCFICH | 16                     | 160                 | 40                                                          |


#### PHICH (Physical HARQ Indicator Channel)

This is the guy which indicates ACK / NACK and is for individual user. It uses symbol 0 slot 0 for all subframes and uses subcarriers depending on the system bandwidth.

| Item  | Bandwidth | PHICH REGs per subframe | PHICH RE per subframe | PHICH RE per Radio Frame |
|-------|-----------|-------------------------|-----------------------|--------------------------|
| PHICH | 1.4 MHz   | 3                       | 12                    | 120                      |
| PHICH | 3 MHz     | 6                       | 24                    | 240                      |
| PHICH | 5 MHz     | 12                      | 48                    | 480                      |
| PHICH | 10 MHz    | 21                      | 84                    | 840                      |
| PHICH | 15 MHz    | 30                      | 120                   | 1200                     |
| PHICH | 20MHz     | 39                      | 156                   | 1560                     |

#### PDCCH (Physical Downlink Control Channel)

This is where things get interesting. The PDCCH is dynamic and varies based on cell load, selected aggregation level etc. So we have a tough choice to make while assuming the overheads of PDCCH if we want to limit our analysis on theoretical settings alone. One way of doing this can be to select the number of CFIs and then calculate the PDCCH overheads. Another way can be to get the available statistics from the Network and interpolate based on CFI usage and aggregation levels used in the sites/areas.

As we are still in the theoretical section, lets see the PDCCH REs based on CFI. Please note that the REGs and CCEs are not directly convertable as some part of REs are not sufficient to form a complete CCE and remain unused. We should consider this as a resource wastage in our calculation as well.

**Overhead for 1.4/3/5MHz for CFI=1 is mentioned as zero. As per my understanding it is not possible to use CFI=1 due to insufficient Common search space CFIs. Feedback welcome for this point (aliasgherman at gmail)**

| Bandwidth   (MHz) | CFI | PDCCH REGs in a   Frame | PDCCH CCEs in a   Frame | Unuseable RE | Final   PDCCH+Unused RE Overheads in Frame |
|-------------------|-----|-------------------------|-------------------------|--------------|--------------------------------------------|
| 1.4               | 1   | 0                       | 0                       | 0            | 0                                          |
| 1.4               | 2   | 410                     | 40                      | 200          | 1640                                       |
| 1.4               | 3   | 590                     | 60                      | 200          | 2360                                       |
| 3                 | 1   | 0                       | 0                       | 0            | 0                                          |
| 3                 | 2   | 650                     | 70                      | 80           | 2600                                       |
| 3                 | 3   | 1100                    | 120                     | 80           | 4400                                       |
| 5                 | 1   | 0                       | 0                       | 0            | 0                                          |
| 5                 | 2   | 1090                    | 120                     | 40           | 4360                                       |
| 5                 | 3   | 1840                    | 200                     | 160          | 7360                                       |
| 10                | 1   | 750                     | 80                      | 120          | 3000                                       |
| 10                | 2   | 2250                    | 250                     | 0            | 9000                                       |
| 10                | 3   | 3750                    | 410                     | 240          | 15000                                      |
| 15                | 1   | 1160                    | 120                     | 320          | 4640                                       |
| 15                | 2   | 3410                    | 370                     | 320          | 13640                                      |
| 15                | 3   | 5660                    | 620                     | 320          | 22640                                      |
| 20                | 1   | 1570                    | 170                     | 160          | 6280                                       |
| 20                | 2   | 4570                    | 500                     | 280          | 18280                                      |
| 20                | 3   | 7570                    | 840                     | 40           | 30280                                      |


---



## Conclusion to the Phase 1 Calculations

Till this point, we have collected pretty much all downlink control channel overheads and we can now estimate remaining PDSCH resources. **Beware**, this is only part 1 of the exercise as we will now need to incorporate the **scheduling** parameters into available PDSCH resources to compute available capacity.

With the available informations, we can work for Phase 1 Control Channel capacity calculations via below table. The only dynamic formula in this sheet is for the CFI. In almost all networks, CFI is dynamically modified based on PDCCH demand and for our calculations, we can incorporate this factor as well. The way to do it can be to get the CFI Usage Ratio of CFI1, CFI2 and CFI3. 

_Assume, CFI1 was used 30% of the times, CFI2 was used 30% and CFI3 was used 40% of the time, then the ratios would be 0.3, 0.3, 0.4 and PDCCH overheads would be calculated by using the PDCCH table above for _

**PDCCH Overhead = (PDCCH overhead for CFI1 * 0.3) + (PDCCH Overhead for CFI2 * 0.3) +  (PDCCH Overhead for CFI3 * 0.4) **



| Bandwidth   (Mhz) | Antenna Ports |       RBs       |        Total RE in a   Frame       |                      CFI1 Ratio (0-1)                      | CFI2 Ratio (0 -   1) | CFI3 Ratio (0-1) | Average CFI | CRS Overheads | PDCCH Overheads | PSS/SSS Overheads | PBCH Overheads | PCFICH Overheads | Remaining PDSCH   RE in Frame |                             Important Notes                             |
|:-----------------:|:-------------:|:---------------:|:----------------------------------:|:----------------------------------------------------------:|:--------------------:|:----------------:|:-----------:|:-------------:|:---------------:|:-----------------:|:--------------:|:----------------:|:-----------------------------:|:-----------------------------------------------------------------------:|
|       in MHz      |  MIMO Config  | 1 Slot RB Count | RE per Symbol *   Symbol per Frame | For dynamic CFI usage, sum   of these should be equal to 1 |                      |                  |             |               |                 |                   |                |                  |                               |                                                                         |
| 1.4               | 1             | 6               | 10080                              | 0                                                          | 1                    | 0                | 2           | 480           | 1640            | 288               | 240            | 200              | 7232                          | CFI =1 for   1.4 MHz means 2 symbols. CFI =1 not used for 1.4 till 5MHz |
| 1.4               | 2             | 6               | 10080                              | 0                                                          | 1                    | 0                | 2           | 960           | 1640            | 288               | 240            | 160              | 6792                          | CFI =1 for   1.4 MHz means 2 symbols. CFI =1 not used for 1.4 till 5MHz |
| 1.4               | 4             | 6               | 10080                              | 0                                                          | 1                    | 0                | 2           | 1440          | 1640            | 288               | 240            | 160              | 6312                          | CFI =1 for   1.4 MHz means 2 symbols. CFI =1 not used for 1.4 till 5MHz |
| 3                 | 1             | 15              | 25200                              | 0                                                          | 1                    | 0                | 2           | 1200          | 2600            | 288               | 240            | 200              | 20672                         | CFI =1 for   1.4 MHz means 2 symbols. CFI =1 not used for 1.4 till 5MHz |
| 3                 | 2             | 15              | 25200                              | 0                                                          | 1                    | 0                | 2           | 2400          | 2600            | 288               | 240            | 160              | 19512                         | CFI =1 for   1.4 MHz means 2 symbols. CFI =1 not used for 1.4 till 5MHz |
| 3                 | 4             | 15              | 25200                              | 0                                                          | 1                    | 0                | 2           | 3600          | 2600            | 288               | 240            | 160              | 18312                         | CFI =1 for   1.4 MHz means 2 symbols. CFI =1 not used for 1.4 till 5MHz |
| 5                 | 1             | 25              | 42000                              | 0                                                          | 1                    | 0                | 2           | 2000          | 4360            | 288               | 240            | 200              | 34912                         | CFI =1 for   1.4 MHz means 2 symbols. CFI =1 not used for 1.4 till 5MHz |
| 5                 | 2             | 25              | 42000                              | 0                                                          | 1                    | 0                | 2           | 4000          | 4360            | 288               | 240            | 160              | 32952                         | CFI =1 for   1.4 MHz means 2 symbols. CFI =1 not used for 1.4 till 5MHz |
| 5                 | 4             | 25              | 42000                              | 0                                                          | 1                    | 0                | 2           | 6000          | 4360            | 288               | 240            | 160              | 30952                         | CFI =1 for   1.4 MHz means 2 symbols. CFI =1 not used for 1.4 till 5MHz |
| 10                | 1             | 50              | 84000                              | 0.8                                                        | 0.2                  | 0                | 1.2         | 4000          | 4200            | 288               | 240            | 200              | 75072                         |                                                                         |
| 10                | 2             | 50              | 84000                              | 0.8                                                        | 0.2                  | 0                | 1.2         | 8000          | 4200            | 288               | 240            | 160              | 71112                         |                                                                         |
| 10                | 4             | 50              | 84000                              | 0.8                                                        | 0.2                  | 0                | 1.2         | 12000         | 4200            | 288               | 240            | 160              | 67112                         |                                                                         |
| 15                | 1             | 75              | 126000                             | 0.8                                                        | 0.2                  | 0                | 1.2         | 6000          | 6440            | 288               | 240            | 200              | 112832                        |                                                                         |
| 15                | 2             | 75              | 126000                             | 0.8                                                        | 0.2                  | 0                | 1.2         | 12000         | 6440            | 288               | 240            | 160              | 106872                        |                                                                         |
| 15                | 4             | 75              | 126000                             | 0.8                                                        | 0.2                  | 0                | 1.2         | 18000         | 6440            | 288               | 240            | 160              | 100872                        |                                                                         |
| 20                | 1             | 100             | 168000                             | 0.8                                                        | 0.2                  | 0                | 1.2         | 8000          | 8680            | 288               | 240            | 200              | 150592                        |                                                                         |
| 20                | 2             | 100             | 168000                             | 0.8                                                        | 0.2                  | 0                | 1.2         | 16000         | 8680            | 288               | 240            | 160              | 142632                        |                                                                         |
| 20                | 4             | 100             | 168000                             | 0.8                                                        | 0.2                  | 0                | 1.2         | 24000         | 8680            | 288               | 240            | 160              | 134632                        |                                                                         |



---



<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/materialize/1.0.0/css/materialize.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/materialize/1.0.0/js/materialize.min.js"></script>



<div class="row">
    <div class="input-field col s4">
          <input id="cfi1_usage" type="number" class="validate" min="0.0" max="1.0" step="0.01" value="0.6">
          <label for="cfi1_usage">CFI 1 Usage (0.0 - 1.0)</label>
    </div>
    <div class="input-field col s4">
          <input id="cfi2_usage" type="number" class="validate" min="0.0" max="1.0" step="0.01" value="0.3">
          <label for="cfi2_usage">CFI 2 Usage (0.0 - 1.0)</label>
    </div>
    <div class="input-field col s4">
          <input id="cfi3_usage" type="number" class="validate" min="0.0" max="1.0" step="0.01" value="0.1">
          <label for="cfi3_usage">CFI 3 Usage (0.0 - 1.0)</label>
    </div>    
</div>

<div class="row">
    <div class="input-field col s4">
    <select>
      <option value="1.4">1.4</option>
      <option value="3">3</option>
      <option value="5">5</option>
      <option value="10">10</option>
      <option value="15">15</option>
      <option value="20">20</option>        
    </select>
    <label>Select Bandwidth (MHz)</label>
    </div>

    <div class="input-field col s4">
    <select>
      <option value="1">1</option>
      <option value="2">2</option>
      <option value="4">4</option>
    </select>
    <label>Select Antenna Ports</label>
    </div>

    <div class="input-field col s4">
        <a class="waves-effect waves-light btn" id="calculate_control_overheads" onclick="calc_overheads"">Calculate Overheads</a>
    </div>

</div>



<script>

window.onload = function() {
  M.AutoInit();
}

function calc_overheads() {
    var message_call = "<b>Overheads Calculated</b><br/>CRS<br/><PDCCH></br>";
    M.toast({html: message_call});
}
  
</script>
