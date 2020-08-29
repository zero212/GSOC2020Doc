
GSOC 2020 - Non-Preemptive / Limited preemptive in Response Time Analysis 
=========================================================================

.. figure:: /gsoc_logo.png 
	:width: 400
	:align: center

Motivation
^^^^^^^^^^
I have been working on RTA related field for the past 1.5 years during my master program, and this time GSoC provides me an opportunity to extend that knowledge into more detail and complicated with a great mentor/supervisor.
Prior to this GSoC, I only know 1 method to calculate response time of a task in unicore using recurrence relation.

However, that method is even older than me, and not very optimal in my opinion. I want to find a faster, more optimal way to calculate response time where all task would meet it deadline by configure its preemptive type and its environment.
Furthermore, APP4MC with Amalthea model already have those preemption properties needed for the implementation, this reduced greatly the orientation time needed to get familiar with new platform/software since I have already used it since 2019

This led me to my decision choosing this topic: **Non-Preemptive / Limited preemptive in Response Time Analysis**

With all the software related material sorted out, all I needed to do left is to find how to calculate response time in 2 different environments: non-preemptive, and limited preemptive.


Content 
^^^^^^^
.. toctree::
   :maxdepth: 2
   :caption: Contents:

   How to use <contents/Tut>
   Implementation functions <contents/Intro>
   Theory <contents/Theory>
   Plan and timeline <contents/Plan>
   Contribute to the community <contents/Contribute>
   Repo <contents/Repo>
   Future work <contents/Futurework>
   References <contents/References>

If you have any question, contact me via : the.bui003@stud.fh-dortmund.de

Happy coding 