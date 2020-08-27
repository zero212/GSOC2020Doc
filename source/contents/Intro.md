# Function introduction

Function inside my class `NPandPRTA` and its usage:

```Java
setSchedubilityCheck(boolean schedubilityCheck)
``` 
set to false will return RTA result without checking its schedubility (only check core ultilization). This is used to avoid return 0 for task's RT that exceed its period/deadline. 
Mainly use for test class. Recommend to set it to true when partitioning

```Java
getRTAinMixedPreemptiveEnvironment(Task thisTask, int[] ia, TimeType executionCase, int[] pTypeArray, boolean usePtypeArray):
```
 calculate task's response time in different environments: fully preemptive, non-preemptive, deffered preemptive, and a mixed of all (core have tasks with different preemptive type). 
`pTypeArray` is preemptive type array for task, used when developer want to do shuffle preemptive type via code rather than edit the model. 
-Array same length with `int[]ia` array. index value meaning : 0 = preemp, 1 = non-preemptive, 2 = cooperative type
-Set to null when use model's configurated preemtive type 
`boolean usePtypeArray` set to true if use pTypeArray
Mechanism within this class
		

	  Step 1: determine what preemptive type thisTask is
      Step 2: find out whether there is blocking from lp task (cuz np/c type task)
      Step 3: choose the method by these criteria
    		   - if (taskType == preemp/cooperative) && (blockingTime == 0) 
    		  	    => use normal level-i (getResponseTimeViaLvI)                              (1)
    		   - if (taskType == preemp/cooperative) && (blockingTime !=0)
    		       => use level-i with preemptive mixed environment (getPreempCoopMixedTaskRTA)   (2)
    		      	finishing time here is different than the above
    		        finishingTime = startTime + execution time + preemption time
    		     	preemption time = time thisTask get preempted by other higher priority task in the same core.
    		   - if (taskType == non-preemp))  
    		       => use level-i with blocking (getNonPreemptaskRA)                          (3)
    		       blockingTime doesn't matter here since even if there is no blocking time, the equation still hold true
    		       important part is taskType is non-preemp which make finishingTime = startTime + execution time;
		       

```Java
getResponseTimeViaRecurrenceRelation(Task thisTask, int[] ia, final TimeType executionCase): 
```
calculate response time of task using classic recurrence relation method. 

```Java
getResponseTimeViaLvI(Task thisTask, int[] ia, TimeType executionCase): 
```
calculate response time of task using level-i method (window technique). 

```Java
getNonPreemptaskRTA(Task thisTask, int[] ia, TimeType executionCase, int[] pTypeArray, boolean usePtypeArray): 
```
when task is non-preemptive type. Response time will be calculated via level-i technique 
Since task is non-preemptive -> once it start, it doesn't stop. Our goal here is to calculate task's `start time` .Cuz task's `finishing time = start time + execution time` . We can derive response time by `finishing time - kPeriod`

```Java
getLowerPriorityBlockingTime(Task thisTask, int[] ia, TimeType executionCase, int[] pTypeArray, boolean usePtypeArray):
```

time `thisTask` would be blocked by lower priority task. Mechanism here is to scan all lower priority tasks (`lpTask`) that are allocated in the same core with `thisTask`. Then check their preemptive type, `lpTask` only block` thisTask` if they are non-preemptive or cooperative. `thisTask` only get blocked once, so we take the longest time that task could be block by `lpTask` (i.e lpTask_1 block 10ms, lpTask_2 block 20ms then return 20ms as blocking time)

```Java
getPreempCoopMixedTaskRTA(Task thisTask, int[] ia, TimeType executionCase, int[] pTypeArray, boolean usePtypeArray)
```
When task is not non-preemptive type and under a mixed or cooperative environment . Response time will be calculated via level-i method. This time our calculation is not only focus on start time but also finishing time. 
Finishing time = start time + execution time + preemption by higher priority task. And after this response time = finishing time - kPeriod.

```Java
getPureExecutionTime(Task thisTask, int[] ia, final TimeType executionCase):
```
Label access Read + ticks/executions need + Label access Write. No blocking here.

```Java
getExecutionTime (Task thisTask, int[] ia, final TimeType executionCase):
```
getPureExecutionTime + blocking. Contention, Copyengine, global blocking etc... can be added here.

```Java
getUltilizationCheck(List<Task> groupTask, int[] ia, TimeType executionCase):
```
check whether it is possible to shove a group of task together in one core. 
return true when core ultilization value is less than 100%. 
