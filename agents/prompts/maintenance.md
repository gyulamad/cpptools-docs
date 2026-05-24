1. Read all the sourcefile and check if it's following the best practices and coding stenders, 
    clean code and clean architect, separation of concerns, no code duplication, proper error handlings, etc.
2. Once you reviewed the files and found any issue raise it in the issues/ folder by creating a task.
3. Then see if everything is covered with unit tests properly and if not raise an issue in 
    the issues/ folder by creating a task.
4. Then see if everything is added to the readme or if there is anything missing you should 
    update the README accordingly.
    TIP: issues are going to the done/ folder once they are done. 
    To see if the README acually up to date you probably first 
    want to check the tasks that have moved to the done folder after the README last modified.
    An other thing that may can help you identify relevant changes is the git log history!
5. Run CI tests (bash ci.sh) to check current code coverage and ensure all linting/type-checking 
    passes with zero warnings treated as errors. If the ACTUAL code coverage exceeds the configured 
    coverage threshold in ci.sh, increase the threshold by a small increment (e.g., +1-2%) 
    so that over time coverage always increases but never declines. Update ci.sh accordingly if you raised it.
    NOTE: see more on how to deal with CI task in the ci-fix.md file!
6. After you done, read the prompt/retrospect.md and act accordingly by following the instructions there...