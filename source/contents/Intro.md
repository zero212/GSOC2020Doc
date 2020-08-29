# Function introduction

## Function inside the class `NPandPRTA` and its usage:

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

## Function inside the class `Blocking` and its usage:

```Java
private void setUpFlagArrayAndHashMap(final int[] iap):
```
This function is used to to figure out whether any task assigned to CPU trigger GPU task or not. 
And if it does, then the shooting task (cpu task) will have more labels (comes from shot task, or GPU task)
Rather complicated for this part. 
If there aren't any of those behavior then it will return nothing. 
So don't worry here

```Java
private List<Task> groupTask(final Task thisTask) 

private List<Task> groupTaskWithIA(final Task thisTask, int[] ia) 

private List<Task> groupOtherTask(final Task thisTask) {

private List<Task> groupTaskFromOtherCore(final Task thisTask, int[] ia) 

```
Functions work as its name, return a list of task that located inside the same core or other cores.
Reason behind 2 function groupTask and groupTaskWithIa is because groupTaskWithIA will derive from an input integer array, the other use input integer array when we initialize the class.
Result is same, but I will use the groupTaskWithIA when I want to multi thread it.
```Java
private List<Task> taskSorting(final List<Task> taskList) 

private List<Label> labelSortingBySize(List<Label> labelList) 
```
task sorting will sort tasks by Rate Monotonic Scheduling (RMS) method, highest priority go first.

labelSortingBySize will rearrange label in the list by its size, biggest go first.

```Java
private HashMap<Task, List<Label>[]> getReadWriteLabelHash(Task thisTask) 
```
put a task in, return a hashmap contain task as key and 2 list as value. 
Should look like this

Hash = task, [readList,WriteList]

```Java
private List<Label> getAccessedLabelListWithDuplicate(Task thisTask) 
```
Same with getReadWriteLabelHash, but instead of a hash map with 2 list value. 
I put everything in a list, don't care whether there is duplicate values or not (read and write the same label)

```Java
private List<Label> getDuplicateBetweenListAndSet(List<Label> thisList, Set<Label> thisSet) 
```

Simple tool for me to get whatever a list and a set have in common, similar with list.retainAll()

```Java
private List<Task> getLabelCalledTaskSet(Label thisLabel) 
```
Get a list of task that called this specific label, use when we need to identify which task is accessing the same label when finding global/local blocking time. 

```Java
private Time getLocalCrossingLabelAccessTime(Task thisTask, List<Task> sameCoreTaskList) 
```
get local label access by lower priority tasks in the same core. 
`CrossingLabel` means several task access same label, visually crossing each other. Just my intepretation here. Not a technical term

```Java
private Time getGlobalCrossingLabelsAccessTime(Task thisTask, List<Task> diffCoreTaskList) 
```
Same with getLocalCrossingLabelAccessTime, only this time we concern about task located in other cores

```Java
private Time getLabelAccessTimeWithTask(Task thisTask, final Label thisLabel, TimeType executionCase) 
```
Mainly use for localCrossingLabelAccessTime
Check label access time for thisLabel with thisTask
Automatically check whether it is read/write access from thisTask to thisLabel, take the longer one if both occured

```Java
private Time getLabelAccessTime(final Task thisTask, final Label thisLabel, final LabelAccessEnum thisType, final TimeType executionCase) 
```
calculate read/write label access of thisTask on thisLabel
Use for globalCrossing and getLabelAccessWithTask above. 
Pretty basic function in RTA

```Java
public List<Semaphore> getSemaphore(final Task thisTask) 
```
Use to locate semaphore in task, and put them in a list to cross checking later when calculate global/local blocking 

```Java
public Time getSemaphoreTime(final Task thisTask, final Semaphore thisSema, final TimeType executionCase) 
```
get critical section length that guarded by Semaphore thisSema for thisTask
Calculated by get labelAccess time + ticks (if avaiable) between request and release semaphore.

```Java
private List<Task> groupSemaphoreLinkedTask(final Task thisTask) 
```
Get a list of task that have same semaphore(s) with thisTask, use for global blocking since we may have hundreds of cores 

```Java
public Time maxPriorTaskBlockingTime(final Task thisTask, final TimeType executionCase) 
public Time otherPriorTaskBlockingTime(final Task thisTask, final TimeType executionCase) 
public Time otherPriorTaskBlockingTime(final Task thisTask, final TimeType executionCase) 
private Time normalTaskBlockingTime(final Task thisTask, final TimeType executionCase) 

```
normal prior task PCP calculation, for reference and testing. Didn't use for any of the implementation.
Not important.

```Java
public Time getLocalBlockingTime(final Task thisTask, final TimeType executionCase) {
```
Calculate task's global blocking time ( time blocked by task from other cores) due to semaphore lock.
If there is no semaphore, the function will calculate blocking time due to resource being read/write by other task. 
The blocking policy is Priority Ceiling Protocol FYI
```Java
public Time getGlobalBlockingTime(final Task thisTask, final TimeType executionCase) {
```
Same with getGlobalBlockingTime, but this time we calculate blocking time due to local task (task within same core)

```Java
private int getGPUindex(Amalthea modelp) {
```
This is a generic approach on how we locate GPU task in the integer array used for mapping.
Need to be modify if task failed when executed.

Done
=====================================================================================