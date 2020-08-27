# Future plan

## Implementing preemption threshold 
This already mentioned in [1] while researching for cooperative preemption type and proven to be superior than without using preemption threshold.
I would like to implement a threshold variable for each task, prefered a generic/dynamic threshold where each task set (several tasks that located in 1 core)
element will have different threshold to ensure its response time alway meet its deadline. 

## Investigate different scheduling method
The current implementation use Rate Monotonic Scheduling to decide task's priority, it would be cool to see what would happen with the same task set but different scheduling method such as EDF, Fixed priority, round robin etc.

## Visualize busy window 
Resposne time analysis using busy window method is difficult and annoying just by looking at the equation, visualize the busy window and how each of the task's instances look like within it will help immensely to whoever trying to get used to with the method