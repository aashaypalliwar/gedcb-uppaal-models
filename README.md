<div id="top"></div>
<br />
<div align="center">

  <h1 align="center">UPPAAL Models for GEDCB</h3>

  <p align="center">
    Using Gossip Enabled Distributed Circuit Breaking for Improving Resiliency of Distributed Systems
    <br />
  </p>
</div>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
    </li>
    <li>
      <a href="#model-details">Model Details</a>
    </li>
    <li>
      <a href="#key-model-parameters">Key Model Parameters</a>
    </li>
    <li><a href="#modelling-availability-scenarios">Modelling Availability Scenarios</a></li>
    <li><a href="#obtaining-data-points">Obtaining Data Points</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
  </ol>
</details>

## About The Project

The [circuit breaker resiliency pattern](https://docs.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker) has been popularly used to improve the resiliency of distributed systems. We targetted a scope of improvement in this pattern by bringing in some modifications to the traditional circuit breaker pattern and coupling it with a gossip-based information dissemination protocol. We modelled the relevant systems using UPPAAL and provided data supporting the efficacy of our proposals. This repository holds the three UPPAAL models that were used to claim the efficacy. These proposals have been submitted and accepted for presentation at the 19th IEEE International Conference on Software Architecture ([ICSA 2022](https://icsa-conferences.org/2022/)) as a full technical paper.

## Getting Started
Running and experimenting with the models require UPPAAL v4.1.25. The experiments were conducted on a machine using Linux (Ubuntu 20.04) operating system with the 64-bit installation of UPPAAL available on their [website](https://uppaal.org/downloads/).

## Model Details
To support our claims of superiority of Gossip Enabled Distributed Circuit Breaking (GEDCB) over the traditional circuit breaker pattern, we modelled three systems - a system that uses GEDCB, one that uses the traditional circuit breakers (CB) and a baseline system that uses no resiliency pattern. We can get a good idea about the efficacy of our proposals based on the relative performance of the three models.

The system models are composed from the following components:
* Server
* Clients (using GEDCB, traditional circuit breaker, no resiliency pattern)
* Client Shuffler
* Gossiper
* Gossip Set Revisor

Systems under study:
1. [Using no resiliency pattern](https://github.com/aashaypalliwar/gedcb-uppaal-models/blob/main/no-pattern.xml) - One Server, Eight Clients (using no resiliency pattern), One Client Shuffler
2. [Using traditional CB](https://github.com/aashaypalliwar/gedcb-uppaal-models/blob/main/cb.xml) - One Server, Eight Clients (using traditional circuit breaker), One Client Shuffler
3. [Using GEDCB](https://github.com/aashaypalliwar/gedcb-uppaal-models/blob/main/gedcb.xml) - One Server, Eight Clients (using GEDCB), eight (corresponding) Gossipers, One Client Shuffler, One Gossip Set Reviser

## Key Model Parameters

|Parameter|Meaning|Value Used|
|:----------:|--------|:---------:|
| resp_time | Response Time | 4 |
| time_out | Timeout Period | 25 |
| UST | Unavailability Streak Time | 250 |
| window_size | Window Size | 10 |
| softFT | Soft Failure Threshold | 2 |
| (H)FT | (Hard) Failure Threshold | 6 |
| HOFT | Half-Open Failure Threshold | 1 |
| HOST | Half-Open Success Threshold | 2 |
| SST | Suspicion Success Threshold | 2 |
| OD | Open Duration | 100 |
| s_delay | Client Shuffling Period | 500 |
| delay | Client Gossiper Period | 4 |
| revision_time | Gossip Set Revision Period | 40 |
| MRC | Maximum Request Count | 500 |
| gossip_count | Number of clients gossiped to | 2 |
| infection_count | Number of clients receiving set revision message | 2 |

## Modelling Availability Scenarios
| Availability | ASRC | USC |
|:------------:|:----:|:---:|
| 1 | 500 | 0 |
| 0.8 | 167 | 2 |
| 0.6 | 84 | 5 |
| 0.4 | 39 | 12 |
| 0.2 | 16 | 32 |

## Obtaining Data Points
The variables of interest are `TC` and `exec` where the former hold the number of timeouts the system encounters to successfully address `MRC` number of requests and the latter one holds the total time required to do the same.

We performed the following two example [SMC queries](https://doi.org/10.1007/s10009-014-0361-y) on the three models:
```
E[<= 10500; 500](max : TC)
```
```
E[<= 10500; 500](max : exec)
```
The queries shown above provide the expected maximum value of the variable TC and the clock variable exec respectively within the time 10500 units evaluated over 500 simulations. For a given availability value configuration and the system at hand, we first run a few simulations to find the time bound by which the system halts and use this time period for the above SMC queries For example, we found that 10500 is a time bound by which all our modelled systems halted. Such simulations may be run using a query like:

```
simulate [<=10500; 1] {TC}
```

Once the system halts, the two variables of interest do not increase in value with time. Hence the queries to find the expected maximum value of the variables within this experimentally found time-bound provide the correct estimations.

<!-- LICENSE -->
## License
Distributed under the MIT License. See [LICENSE.md](https://github.com/aashaypalliwar/gedcb-uppaal-models/blob/main/LICENSE) for more information.

<!-- CONTACT -->
## Contact

Aashay Palliwar - avp10@iitbbs.ac.in

Srinivas Pinisetty - spinisetty@iitbbs.ac.in