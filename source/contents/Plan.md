# Plan and milestone

## Phase 1

**May 4 - June 23, 2020 (Community Bonding Period, Coding part 1)**

* Structuring the implementation process:
    1. Non-preemptive response time analysis (RTA)
    2. limited/cooperative RTA 
    3. a mixed of non-preemptive, limited/cooperative and preemptive RTA

* Retesting level-i and recurrence relation method in calculating response time
* Implementing non-preemptive RTA in APP4MC

This phase is mainly for getting familiar with level-i busy window technique in RTA which contribute quite a lots in further exploration regard to RTA, at least non-preemptive RTA was completed during this phase
## Phase 2

**June 24 - July 21 (Coding - Part 2)**

* Implement cooperative RTA in APP4MC
* Create test classes for both non-preemptive and cooperative
* Attempt on mixed preemptive environment RTA

After this phase, the skeleton or outline of how data flow and how response time analysis in different cases is formed.
From getting info from model and mapping array to source out what environment input task's in, and finally decide what equation/function is used to give out the best solution. 

## Phase 3
**July 22 - August 18 (Coding - Part 3)**

* Implement mixed preemptive environment RTA
* Adding getting preemptive type from an customized array, similar with mapping array for the class.
This will remove unnecessary time for mouse clicking in order to change preemption type in model explorer
* Implement blocking analysis, based on semaphore and Priority Ceiling Protocol
* Adding blocking due to shared resources if no semaphore/critical section is available within model 
* Create test classes, adding javaDoc, documentation

Implementation done here, tough time with all the additioning stuff but was fun and interesting.






