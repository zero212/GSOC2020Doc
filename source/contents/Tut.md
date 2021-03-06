# How to use

First of all, you will need to clone the big repo from this link 

---> [https://git.eclipse.org/c/app4mc/org.eclipse.app4mc.tools.git/](https://git.eclipse.org/c/app4mc/org.eclipse.app4mc.tools.git/) <---

Here is the ssh html location if you can't find it
![](Gitpull.PNG)

You need to sign up an eclipse account to be able to clone it though.

Check out the tag gsoc20MPRTAFinal. 

This is the tag that contains the GSoC2020 implementation by the time the Project is submitted. It may be merged to master soon, too.

The content of this tag maybe updated after GSoC submission, I will keep this document updated if more work is done regard this method.

1. Under responseTime-analyzer > plugins > src >...> gsoc_rta folder

You will find `NPandPRta` class. This is the implementation source file. One can calculate task's response time in different environment using this.

2. Under responseTime-analyzer > plugins > src >...> gsoc_rta >'test' folder, there is `NPandPNumerical` class. 

This is a numerical example on how functions/equations used in 'NPandPRta' work.

3. Under responseTime-analyzer > plugins > src >...> gsoc_rta folder

`Blocking` is also located here, using this will allow you to calculate local and global blocking time of task

Check out the **Implementation functions** part if you want to know what functions is used for

## NPandPRTA

**Important remarks:**

- In this implementation, the function allow all higher priority task in the same core to be able to preemp lower priority task at runnable  bound.
No preemption threshold is implemented here.
The implementation for cooperative part used the equation 12 in this [ref](http://algo.ing.unimo.it/people/marko/papers/WATERS16.pdf), but with the change of j:P_j > P_i, not j:P_j > \theta_i
Preemption threshold did prove to be superior in several research in response time analysis. 
However the main focus of this project is the cooperative preemption, not threshold. 
This is one of the main future work if I ever return to this topic.

There is something need to be mentioned before you try using this class, it is created based on WATERS2019 model, the functions are tested using that model. 
But you should be able to ultilize this class without many problem as long as you provided 3 input parameters:
* Amalthea model - The model that you will use to ultilize this method, this should be given when you create the class object
* Integer array (ia) - This is a representation of how task is allocated to cores, the location of each element represent task, and the value represent core. I.e. {0,2,3,1,1,2} : first task is assigned to first core of the model, 2nd task is assigned to 3rd core, 3rd task is assigned to 4th core and so on 
* Task - the task you want to calculate its response time

Also additionally there are 3 other parameters:
* executionCase : TimeType.WCET  - just put this like this, since you would want to calculate worst case response time most of the time, change to BCET if you want something different.
* pTypeArray: a customized array that you can use to define tasks' preemption type. 
Leave null if don't use. 
Check javadoc for more info.
* usePtypeArray: boolean variable to announce whether you want to use pTypeArray or not. 
Leave false if don't use. 

Below are the important functions that you will probably use most of the time. 
For the list of all function, refer to the javadoc, Implementation functions or open the class, I left lots of comment there

**getRTAinMixedPreemptiveEnvironment**

Calculate response time of task in mixed environment. 
Drop the task, the integer mapping array, and the model (again this class is made mainly for WATERS2019 derived model, but it should work on other as well) and you get your response time.
I also opt in an option where you can input your own preemptive type array.
Where you can change task's preemptive type without changing it in the model.

**setSchedubilityCheck**

This function set a boolean variable where you can enable/disable schedubility check.
Which means if you set to false. Every RTA functions will return the value without checking whether that response time bigger than task's period or not.

**getResponseTimeViaLevelI**

Calculate resposne time of task in preemptive environment via level-i busy window technique.
Pretty vanilla/basic implementation. 

**getResponseTimeViaRecurrenceRelation**

Calculate resposne time of task in preemptive environment via recurrence relation. 
Again very basic execution of how response time is calculated
Should give the same result as response time level-i. 

**getPureExecutionTime**

Using the well-known semantics, where task is run as follow:  READ -> EXECUTION -> WRITE
Calculate all of the element from each step, sum all of them and we have task's execution time.

## Blocking

<`Blocking.java`>

Blocking analysis, calculate blocking time of semaphore(critical section) when they are exist, or else calculate time other tasks have to wait due to global resource occupancy (task had to wait because other task is reading/writing label) )
Same as the `NPandPRta.java`, this class also created based on WATER 2019 model. 

Again I only listed important/useful function. For more info, please refer functions' javadoc and the implementation functions part for more details

**getGlobalBlockingTime**

Calculate task's global blocking time ( time blocked by task from other cores) due to semaphore lock.
If there is no semaphore, the function will calculate blocking time due to resource being read/write by other task. 
The blocking policy is Priority Ceiling Protocol FYI

**getLocalBlockingTime**

Same with getGlobalBlockingTime, but this time we calculate blocking time due to local task (task within same core)










