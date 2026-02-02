# PsychoPy Builder Template — How PsychoPy Task Should Be Structured

This repository provides a **standard PsychoPy Builder template** that we recommend as the starting point for many reaction-time and cognitive tasks (e.g., N-back, flanker, go/no-go, stop-signal).

The goal of this template is to answer three questions for new users:

- **What routines should my task have?**
- **What goes in each routine?**
- **Why is the task organized this way?**



## Basic Principles
There are a few principles that we adhere to:
- Use purely Python code as much as possible. Only add necessary files to be loaded under the `resource/` directory. This ensures maximal version control of the task.
- Re-use routines and avoid copying and pasting code as much as possible.
- Check every corner and every component for all tabs of settings. Details matter.
- Be very consistent in code styling. Lint before committing.



## Template Builder Workflow (High Level)

A PsychoPy task experiment typically follows this structure:

```text
__start__
→ [block loop]
    → welcome
    → practiceIntro
    → [practice loop: trial → feedback]
        → trial
        → feedback
        → ITI
    → trialIntro
    → [trials loop: trial]
        → trial
        → ITI
__end__
```

This entire sequence is wrapped inside an **outer block loop**, so the same block sequence of routines can be used to implement multiple conditions **without duplicating code**.

This structure separates:

- **Learning** (practice)
- **Measurement** (main trials)
- **Bookkeeping** (setup and cleanup)



## What Each Part Does (Plain English)

### `__start__` — initialization / setup

- Runs **once** at the very beginning
- Sets up task variables and counters
- Loads condition files (only as necessary)
- Defines block structure
- Initializes timers or data structures

#### What runs inside `__start__` routine:
> The `__start__` routine contains several Code components that handle
> EEG triggers and task initialization. Some of the components should **be modified**
> to suit each specific task.
>
> #### `trigger_table` (update based on your task)
>
> This section defines the **numeric trigger codes** used throughout the task
> (e.g., task start, task ID, block start/end, and trial events like ITI,
> stimulus onset, and feedback).
>
> Think of this as the **master legend** for EEG triggers so that everyone uses the
> same consistent code conventions across experiments.
>
> #### `task_id` (don’t change)
>
> This sends two triggers at the very beginning of the experiment:
>
> 1. A trigger indicating **the task has started**
> 2. A trigger indicating **which specific task is running**
>
> A short delay between the two triggers prevents the EEG system from missing
> or merging them.
>
> #### `eeg` (don’t change)
>
> This is a boilerplate Code component used to connect to the **Cedrus C-POD / StimTracker** device and prepares
> it to send triggers to ANT-Neuro amplifiers.
>
> It:
> - Detects the connected trigger device
> - Sends a trigger to **start EEG recording** (code 126)
> - Waits for the EEG system to spin up
> - Runs a short **“marching lights” test** to confirm trigger lines are working
>
> If no device is detected, a dummy device is used so the task can still run
> without EEG.
>
> #### `condition_setup` (develop for your task)
>
> This is where **your experiment-specific code lives**.
>
> Typical things defined here include:
> - Trial lists and condition files
> - Block order and counterbalancing
> - Task-specific variables and logic
>
> In short, this section defines **what your experiment actually does**.

Note: minimize loading external files, construct variables from scratch in Python as much as possible to help with version control.

Nothing is shown to the participant here. Think of this as **preparing the experiment before the participant sees anything**.



### `welcome` — global instructions

- First screen the participant sees
- Explains the task at a high level
- Tells the participant what they will be doing
- Usually waits for a keypress (e.g., `space`) to continue

#### What runs inside `welcome` routine:
> #### `blockSetup`
> This Code component determines which block is running, selects the correct condition lists, and skips the welcome screen after the first block.
>
> #### `welcomeText`
> This Text component displays the initial task instructions so participants understand what they will see and what they should do.
>
> #### `welcomeKey`
> This Keyboard component waits for a valid keypress and then ends the routine so the experiment can continue.



### `practiceIntro` — block-specific instructions

- Runs once per block, before practice
- Explains what kind of block this is
- Can change instructions depending on the block type
- Good place to remind participants of response rules

#### What runs inside `practiceIntro` routine:
> #### `setPracInstruct`
> This Code component determines whether block condition for the current block and dynamically builds the practice instruction text accordingly. It also sends the block-start trigger so EEG marking begins before practice trials.
>
> #### `pracInstruct`
> This Text component displays the practice instructions generated in `setPracInstruct`, including task rules and response mappings for the current block.
>
> #### `pracInstructKey`
> This Keyboard component waits for the participant to press the allowed key and then ends the routine to begin practice trials.



### `practice` loop — learning phase

- Short loop with feedback
- Usually has fewer trials (e.g., 10–20).
- Contains three routines:

    - **`trial`** routine
        - Presents one stimulus
        - Collects the participant’s response and reaction time
        - Uses the same logic as real trials

    - **`feedback`** routine
        - Tells the participant if they were correct or incorrect
        - Helps them learn the task rules

    - **`ITI`** routine
        - Inter-trial-interval, often jittered duration
        - Provides cadence to trials



### `trialIntro` — transition to real trials

- Runs once per block, after practice
- Tells the participant practice is over
- Explains that feedback will no longer be shown
- Signals that the “real” task is about to begin

#### What runs inside `trialIntro` routine:
> #### `setInstruct`
> This Code component switches the task into main mode, builds the correct instruction text for the current block, and sends EEG triggers marking the end of practice and the start of the main block.
>
> #### `instruct`
> This Text component displays the main-task instructions, emphasizing that feedback is no longer shown and reminding participants of the response rules.
>
> #### `instructKey`
> This Keyboard component waits for the participant to press the allowed key and then ends the routine so the main trials can begin.

This helps reset expectations and refresh instructions before data collection for the main block of trials.



### `trials` loop — main experiment

- This is where real data is collected
- Usually contains many more trials (e.g., 60–200+)
- Uses the same `trial` and 'ITI' routines as in the practice loop. **SEE ABOVE**
- Feedback routine is removed

All behavioral data used for analysis (accuracy, RT, condition labels) comes from this loop.



### `__end__` — task cleanup

- Runs once at the very end
- Thanks the participant and tells them the task is finished
- Includes cleanup code or final data saves

#### What runs inside `__end__` routine:
> #### `text_thank_you`
> This Text component displays a brief message letting the participant know the task is complete.
>
> #### `read_thank_you`
> This Sound component plays an optional audio message confirming the end of the task.
>
> #### `trigger_trial_block_end`
> This Code component sends the final block-end trigger so the EEG recording is properly marked before the experiment terminates.
