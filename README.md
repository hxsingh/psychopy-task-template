# PsychoPy Builder Template — How This Task Is Structured

This repository provides a **standard PsychoPy Builder workflow** that we recommend for most reaction-time and cognitive tasks (e.g., N-back, flanker, go/no-go, stop-signal).

The goal of this template is to answer three questions for new users:

- **What routines should my task have?**
- **What goes in each routine?**
- **Why is the task organized this way?**

Below we explain the workflow in **plain English**, assuming **no prior experience** with PsychoPy.

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

### What runs inside `__start__`

> The `__start__` routine contains several Code components that handle
> EEG triggers and task initialization. These components should **not be modified**
> unless you know exactly what you are doing.
>
> ---
>
> #### `trigger_table` (don’t change)
>
> This section defines the **numeric trigger codes** used throughout the task
> (e.g., task start, task ID, block start/end, and trial events like ITI,
> stimulus onset, and feedback).
>
> Think of this as the **master legend** for EEG triggers so everyone uses the
> same consistent codes across experiments.
>
> ---
>
> #### `task_id` (don’t change)
>
> This sends two triggers at the very beginning of the experiment:
>
> 1. A trigger indicating **the task has started**
> 2. A trigger indicating **which specific task is running** (e.g., ID 108)
>
> A short delay between the two triggers prevents the EEG system from missing
> or merging them.
>
> ---
>
> #### `eeg` (don’t change)
>
> This block connects to the **Cedrus C-POD / StimTracker** device and prepares
> it to send triggers reliably.
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
> ---
>
> #### `condition_setup`
>
> This is where **your experiment-specific code lives**.
>
> Typical things defined here include:
> - Trial lists and condition files
> - Block order and counterbalancing
> - Task-specific variables and logic
>
> In short, this section defines **what your experiment actually does**.

Nothing is shown to the participant here.  
Think of this as **preparing the experiment before the participant sees anything**.

* * * * *

#### `welcome` — global instructions

- First screen the participant sees
- Explains the task at a high level
- Tells the participant what they will be doing
- Usually waits for a keypress (e.g., `space`) to continue

### What runs inside `welcome`

> #### `blockSetup`
> This Code component determines which block is running (e.g., 0-back vs 1-back), selects the correct condition lists, and skips the welcome screen after the first block.
>
> #### `welcomeText`
> This Text component displays the initial task instructions so participants understand what they will see and what they should do.
>
> #### `welcomeKey`
> This Keyboard component waits for a valid keypress and then ends the routine so the experiment can continue.

This runs only once, even if there are multiple blocks.

* * * * *

#### `practiceIntro` — block-specific instructions

- Runs once per block, before practice
- Explains what kind of block this is (e.g., “This is the 0-back block”)
- Can change instructions depending on the block type
- Good place to remind participants of response rules

### What runs inside `practiceIntro`

> #### `setPracInstruct`
> This Code component determines whether the current block is 0-back or 1-back and dynamically builds the practice instruction text accordingly. It also sends the block-start trigger so EEG marking begins before practice trials.
>
> #### `pracInstruct`
> This Text component displays the practice instructions generated in `setPracInstruct`, including task rules and response mappings for the current block.
>
> #### `pracInstructKey`
> This Keyboard component waits for the participant to press the allowed key and then ends the routine to begin practice trials.

This is inside the outer block loop, so instructions update automatically when the block changes.

* * * * *

#### `practice` loop — learning phase

- Short loop with feedback
- Contains two routines:

    - **`trial`**
        - Presents one stimulus
        - Collects the participant’s response and reaction time
        - Uses the same logic as real trials
      - ### What runs inside `trial`

        > #### `trialSetup`
        > This Code component selects the correct trial row depending on whether the task is in practice or main mode and prepares all timing variables for the trial. It also computes a jittered response-cue onset so participants must wait before responding.
        >
        > #### `leftFlank`, `central`, `rightFlank`
        > These Text components display the flanker letters and the central target letter for a fixed stimulus duration.
        >
        > #### `respCue`
        > This Text component displays the response cue (e.g., `< >`) after the stimulus disappears, signaling when responses are allowed.
        >
        > #### `earlyResp`
        > This Keyboard component monitors for premature keypresses before the response cue appears.
        >
        > #### `resp`
        > This Keyboard component collects the participant’s actual response and reaction time after the response cue is shown.
        >
        > #### `trigger_trial`
        > This Code component tracks whether stimulus and cue triggers have already been sent so each event is marked exactly once in the EEG recording.

    - **`feedback`**
        - Tells the participant if they were correct or incorrect
        - Helps them learn the task rules
      - ### What runs inside `feedback`

        > #### `setFbText`
        > This Code component evaluates the participant’s response on the previous trial and decides which feedback message to show (too fast, correct/incorrect, or too slow).
        >
        > #### `fbText`
        > This Text component displays the feedback message generated in `setFbText` for a short, fixed duration.
        >
        > #### `trigger_fb`
        > This Code component tracks whether the feedback trigger has been sent so the feedback event is marked exactly once in the EEG recording.

Practice usually has fewer trials (e.g., 10–20).

* * * * *

#### `trialIntro` — transition to real trials

- Runs once per block, after practice
- Tells the participant practice is over
- Explains that feedback will no longer be shown
- Signals that the “real” task is about to begin

### What runs inside `trialIntro`

> #### `setInstruct`
> This Code component switches the task into main mode, builds the correct instruction text for the current block (0-back or 1-back), and sends EEG triggers marking the end of practice and the start of the main block.
>
> #### `instruct`
> This Text component displays the main-task instructions, emphasizing that feedback is no longer shown and reminding participants of the response rules.
>
> #### `instructKey`
> This Keyboard component waits for the participant to press the allowed key and then ends the routine so the main trials can begin.

This helps reset expectations before data collection.

* * * * *

#### `trials` loop — main experiment

- This is where real data is collected
- Uses the same `trial` routine as practice. 🚩**SEE ABOVE**
- No feedback is shown
- Usually contains many more trials (e.g., 60–200+)

All behavioral data used for analysis (accuracy, RT, condition labels) comes from this loop.

* * * * *

#### `__end__` — task wrap-up

- Runs once at the very end
- Thanks the participant
- Tells them the task is finished
- Can include cleanup code or final data saves

### What runs inside `__end__`

> #### `text_thank_you`
> This Text component displays a brief message letting the participant know the task is complete.
>
> #### `read_thank_you`
> This Sound component plays an optional audio message confirming the end of the task.
>
> #### `trigger_trial_block_end`
> This Code component sends the final block-end trigger so the EEG recording is properly marked before the experiment terminates.
