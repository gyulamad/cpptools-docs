**Prerquist:**

1. NEXT_ISSUE_FILE - Ask the user for the next issue file. 
    If the user is not specifies read the next (oldest) issue task in the issues/ folder,
    Otherwise use the user specified issue file.

**Steps:**

1. Read the NEXT_ISSUE_FILE task. And make sure noone else working on it at the moment (use git diff).
2. Check if the issue is too complex to solve in one go or can be splitted to smaller subtasks:
    2.1 - EPIC SUBTASKS (tightly coupled big tasks): If the task has multiple parts that are tightly 
        coupled together, split them into atomic sub-tasks but place them under a shared epic folder:
        `issues/epic/<main-task-name>/01_first-subtask.md`, `issues/epic/<main-task-name>/02_second-subtask.md`, etc.
        Each subtask should cross-reference the main task and contain all relevant context so they can be 
        picked up independently in upcoming turns but still understood as part of the same larger effort.
        Number them sequentially (01, 02...) to indicate implementation order/dependencies.
        Touch or modify the original issue file so next turn skips it, then proceed with step 6.
    2.2 - INDEPENDENT SUBTASKS: If subtasks are loosely independent and don't need shared context 
        (e.g., separate features that touch different modules), create them directly in `issues/` as normal issues.
    you are DONE at this point proceed with step 6.
    2.2 - if the task can not be splitted to smaller atomic sub-task then proceed to step 3.
3. Check if the issue is already done and fix it if not.
    3.3 - When you start to working on an issue move it to the issues/progress/ folder or 
	if it's an epic's subtask move it to the issues/progress/<epic-name>/ folder
4. Check if the issue is completly covered with unit tests and write unit tests if necessary.
    (Including your code changes if there was any) 
    - use the ci.sh script to confirm and verify all your changes.
    - if ci.sh fails by any reason the task can not be done, you have to fix the errors.
    NOTE: see more on how to deal with CI in the ci-fix.md
5. Once the issue is fixed and fully covered with tests, **append a final solution report** to the bottom 
    of the issue file (a brief summary describing: what was done, files modified, key changes, and how it 
    was verified). Then move the updated issue file to the issues/done/ folder.
    NOTE: if the task was an epic or a subtask of an epic then move it to the issues/done/<epic-name>/ folder.
6. Read the prompts/retrospect.md and act accordingly by following the instructions there...
7. Once you finish and all tests passes commit it to git if you have permission.
8. Notify the user with a nice summary of what have you done.