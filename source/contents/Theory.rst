Theory and equation for response time analysis in different environment
=======================================================================


* Table of Notation for **Basic RTA**

=================  =============================
Description 	   Symbol
=================  =============================
Task i 			   :math:`\tau_i`
WC Response time   :math:`R_i^+`
WC Execution time  :math:`C_i^+`
Period 			   :math:`T_i`
Priority 		   :math:`P_i`
=================  =============================


Fully preemptive environment
############################

* Response time analysis using recurrence relation

Equations of this part comes from [4]. Check Reference for the paper/research

*Equation 1:*
:math:`R_i^+ = C_i^+ + \sum_{j:P_j \geq P_i} \left\lceil \frac {R_{i-1}^+} {T_j} \right\rceil C_j^+`
|

**Numerical example**: Assume a task (:math:`C_i^+`, :math:`T_i`) set  {(1,3), (1,5), (1,6), (2,10)}

:math:`R_1^0` = :math:`R_1^1` = :math:`C1` = 1 (task with highest priority, hence response time = execution time)

:math:`R_2^0` = :math:`C_2` + :math:`C1` = 2 

:math:`R_2^1` = :math:`C_2` + :math:`\left\lceil \frac {R_2^0} {T_1} \right\rceil C_1^+` = :math:`1 + \left\lceil \frac {2} {3} \right\rceil 1 = 2`

:math:`R_2^1` = :math:`R_2^0` = 2. No further exploration can be done here, :math:`\tau_2` response time :math:`R_2` = 2. This mean, we stop when previous iteration value equal the current iteration value, this value will be the response time of task.

|

* Response time analysis using level-i busy window, 

In preemptive environment, beside recurrence relation method, there is another way to calculate task response time (RT) by calculate response time of several instance of
task during its busy period, and pick the longest one among them. 
Equation from this part comes from [2].

*Equation 2:* Level-i active period (busy window)

:math:`L_i =  \sum_{j:P_j \geq P_i} \left\lceil \frac {L_{i}} {T_j} \right\rceil C_j`


*Equation 3:* The number of :math:`\tau_2`'s instances to check are:

:math:`K_i = \left\lceil \frac {L_i} {T_i} \right\rceil`

*Equation 4:* The finishing time of *k*-instance

:math:`f_i^k = \sum_{j:P_j > P_i} \lceil \frac {f_i^k} {T_j} \rceil C_j + (k - 1) C_i`

*Equation 5:* From finishing time, we can calculate response time simply since: Response time = finishing time - arrival time. Where arrival time = k-instance's period.

:math:`R_i^k = f_i^k - (k-1)T_i`

Response time of :math:`\tau_i` is the maximum among all k-instances.

For numerical example, I recommend you check "tutorial on real-time scheduling" by Emmanual Grolleau, his papered is where I check and back test the code flow.
First 4 pages is enough to understand how to use level-i busy window. 

Non-preemptive environment
##########################

For non-preemptive environment, recurrence relation method is not doable, only busy window is usable as far as I know.

We reuse equation 2, 3 to get total number of k instance, only different is we will calculate arrival time, then find finishing time by arrival time + execution time, and use equation 5 for response time:

*Equation 6*: release time :math:`s_i^k` of :math:`\tau_i^k` 

:math:`s_i^k =  \sum_{j:P_j > P_i} (\lfloor \frac {s_i^k} {T_j}\rfloor + 1) C_j + (k - 1) C_i`

Then simply finishing time :math:`f_i^k = s_i^k + C_i` since it is non-preemptive, response time can be calculate again by equation 5.

Limitted preemptive (or cooperative) environment
################################################

Window technique again proved to be useful in this scenario, this time we need to introduce blocking time, which is the largest execution time of lower priority runnable (since cooperative is runnable bound).
All equation in from this part is from paper [1], check out references tab for more info.

Blocking time 

:math:`B_i = max_{j:P_j < P_i} (C_j,r)`

Reuse equation 2,3 again, but include blocking time :math:`B_i` into equation 2 to find out proper busy period

:math:`L_i = B_i + \sum_{j:P_j \geq P_i} \left\lceil \frac {L_{i}} {T_j} \right\rceil C_j`

Task :math:`\tau_i` finished time can be derived from equation 4, with a touch of blocking time

:math:`f_i^k = B_i + \sum_{j:P_j > P_i} \lceil \frac {f_i^k} {T_j} \rceil C_j + (k - 1) C_i`

And here we can apply equation 5 for response time, pretty simple right?

**Notice here:** This is true iff we assume all higher priority task can preempt lower priority task as soon as its arrival time come. 
If you want to introduce some preemption threshold :math:`\theta_i`, follow the below equations.

*Equation 7:* get task's release time:

:math:`s_i^k = B_i + \sum_{j:P_j > P_i} (\lfloor \frac {s_i^k} {T_j}\rfloor + 1) C_j + (k - 1) C_i`

*Equation 8*: get task's finshing time:

:math:`f_i^k = s_i^k + \sum_{j:P_j > \theta_i} (\lceil \frac {f_i^k} {T_j} \rceil - (\lfloor \frac {s_i^k} {T_j} \rfloor + 1)) C_j`

And then applied the same equation 5 for response time.

developer note: sorry I didn't include any numerical example in these case, it's rather lengthy to do so, but if you understand how level-i work, these equation wouldn't be difficult at all