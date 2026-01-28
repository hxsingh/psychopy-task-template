# PsychoPy Builder Template — How This Task Is Structured

This repository provides a **standard PsychoPy Builder workflow** that we recommend for most reaction-time and cognitive tasks (e.g., N-back, flanker, go/no-go, stop-signal).

The goal of this template is to answer three questions for new users:

- **What routines should my task have?**
- **What goes in each routine?**
- **Why is the task organized this way?**

Below we explain the workflow in **plain English**, assuming **no prior experience** with PsychoPy or CyclePipe.

---

## Standard Builder Workflow (High Level)

The task follows this structure:

```text
__start__
 → welcome
 → practiceIntro
 → [practice loop: trial → feedback]
 → trialIntro
 → [trials loop: trial]
 → __end__
```

This entire sequence is wrapped inside an **outer block loop** (e.g., `nbackBlocks`) so the same task can run multiple conditions (0-back, 1-back, etc.) **without duplicating code**.

This structure separates:

- **Learning** (practice)
- **Measurement** (main trials)
- **Bookkeeping** (setup and cleanup)

---

## What Each Part Does (Plain English)

#### `__start__` — initialization / setup

- Runs **once** at the very beginning
- Sets up task variables and counters
- Loads condition files
- Defines block structure (e.g., 0-back vs 1-back)
- Initializes timers or data structures

Nothing is shown to the participant here.  
Think of this as **preparing the experiment before the participant sees anything**.

* * * * *

#### `welcome` — global instructions

- First screen the participant sees
- Explains the task at a high level
- Tells the participant what they will be doing
- Usually waits for a keypress (e.g., `space`) to continue

This runs only once, even if there are multiple blocks.

* * * * *

#### `practiceIntro` — block-specific instructions

- Runs once per block, before practice
- Explains what kind of block this is (e.g., “This is the 0-back block”)
- Can change instructions depending on the block type
- Good place to remind participants of response rules

This is inside the outer block loop, so instructions update automatically when the block changes.

* * * * *

#### `practice` loop — learning phase

- Short loop with feedback
- Contains two routines:

    - **`trial`**
        - Presents one stimulus
        - Collects the participant’s response and reaction time
        - Uses the same logic as real trials

    - **`feedback`**
        - Tells the participant if they were correct or incorrect
        - Helps them learn the task rules

Practice usually has fewer trials (e.g., 10–20).

* * * * *

#### `trialIntro` — transition to real trials

- Runs once per block, after practice
- Tells the participant practice is over
- Explains that feedback will no longer be shown
- Signals that the “real” task is about to begin

This helps reset expectations before data collection.

* * * * *

#### `trials` loop — main experiment

- This is where real data is collected
- Uses the same `trial` routine as practice
- No feedback is shown
- Usually contains many more trials (e.g., 60–200+)

All behavioral data used for analysis (accuracy, RT, condition labels) comes from this loop.

* * * * *

#### `__end__` — task wrap-up

- Runs once at the very end
- Thanks the participant
- Tells them the task is finished
- Can include cleanup code or final data saves
