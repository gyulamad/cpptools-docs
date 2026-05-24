refactoring:
1: ask the user if they are plenning to refactor or they already has a plan.
    - if they want to refactor jump to the step 2
    - if they are planning to refactor:
	1.1: ask user which part they want to refactor, 
	1.2: and what file you should store the plan.
        1.3: then read the code and create a refactor plan file.
	1.4: continue from step 3
2: ask the user to give you a refactoring plan file
3: if the file exists read it and verify if it's indeed a refactoring plan.
    - if it doesn't not exists or not a refact plan ask the user again: continue from step 2.
    - if the plan already completly done ask the user for a new plan: continue from step 2.
4: select the next lowest risk step in the refact plan and proceed
5: run the CI tests, if anything fails fix it.
6: if there is more task, repeat from step 4.
    - you are not done until everything is refactored in the plan, stop only if you have question for the user or everything is done for a final report

**Notes on creating refactoring plans:**
- read the relevant codes,
- find separated concerns and responsibilities in the code that can be splitter to smaller units/classes/functions into separated files
- estimate complexity and risk for each separated part
- create the plan file

**Notes on executing a refacoring plan:**
- find the lowest risky step in the plan
- ask the user if they are oke with the next step "can we proceed [Y/n]:"
- proceed with the refactoring step
- check the CI script and unit tests, do not move on unit everything is passes (including unit tests and listers/validators etc..)
- repeat: proceed with the next lowest risky step...