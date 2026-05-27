---
name: shell-retry-on-failure
description: How to retry run_shell commands that fail with unknown errors using stderr redirection and explicit timeouts
---

# Shell Retry on Intermittent Failure

## When to Apply

When a `run_shell` (or equivalent shell execution tool) invocation fails on a **simple, correct command** with an unexpected or unknown error, the failure may be intermittent — caused by environment instability, timing issues, or missing error stream capture.

## Pattern

### Step 1: Identify the failure

You executed a straightforward shell command and received an uninformative error (e.g., "unknown error", exit code 1 with no useful output, or an empty error message).

### Step 2: Retry with two modifications

Re-run the same (or nearly identical) command with **both** of the following changes:

1. **Append `2>&1`** — merges stderr into stdout so the full error output is captured and returned.
2. **Set an explicit timeout** — pass an explicit `timeout` parameter (e.g., 30 or 60 seconds) instead of relying on defaults. This can prevent timing-related failures.

### Example

Original failing call (conceptual):

```
Command:    wc -l /path/to/file
Timeout:    (default)
Error:      unknown error / no output
```

Retry:

```
Command:    wc -l /path/to/file 2>&1
Timeout:    60
```

### Step 3: Interpret the result

- If the retry succeeds, continue with the task — the failure was intermittent.
- If the retry fails again **with captured error output**, diagnose using the new information.
- If the retry fails again **without useful output**, consider:
  - Running a simpler diagnostic command first (e.g., `echo "test" 2>&1` or `ls /path 2>&1`) to check environment health.
  - Trying the command via `shell_agent` which has its own retry logic.

## Key Rules

- **Only retry once** after the initial failure. If two attempts fail, switch to diagnostic mode — do not loop endlessly.
- The `2>&1` appendage must not break commands that pipe output carefully. If the command uses pipes, place `2>&1` at the very end or redirect specific stages as needed:
  ```
  some_command | grep "pattern" 2>&1
  ```
- Keep the retry command **identical** to the original except for the `2>&1` suffix and timeout change. Do not restructure or reformulate the command on retry — that makes it harder to determine if the failure was truly intermittent.

## Why This Works

- **`2>&1`**: Many shell tools only return stdout by default. Error messages written to stderr are lost, making failures look "unknown." Merging streams reveals the actual problem or, in intermittent cases, allows the tool to process output normally.
- **Explicit timeout**: Some environments have restrictive or misconfigured default timeouts. Setting one explicitly can bypass tool-level timeout mismatches.