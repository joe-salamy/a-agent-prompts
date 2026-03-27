# Selenium Browser Automation — Claude Code Prompt

Paste this prompt into Claude Code to generate a new automation script.

---

## Prompt

Enter planning mode and create a plan to build a Python Selenium browser automation using the tech stack and task description below. Do not write any code until the plan is approved.

---

## Tech Stack

**Language:** Python 3.13

**Browser automation:** Selenium 4.x

- Use Selenium's built-in driver manager (no `webdriver-manager` package needed — Selenium 4.6+ handles this automatically)
- Browser: Chrome

**Anti-detection (minimal):**

- Launch Chrome with `--disable-blink-features=AutomationControlled`
- Set `excludeSwitches: ["enable-automation"]` and `useAutomationExtension: false` via ChromeOptions
- Reuse the user's existing Chrome profile so the session looks like a real logged-in user (no programmatic login)
- Add small random delays (0.5–2 seconds) between actions using `random.uniform(0.5, 2.0)` with `time.sleep()`

**Chrome profile reuse:**

- Load the user's real Chrome profile via `user-data-dir` and `profile-directory` options
- Profile path and profile name are defined in `config.py` so the user can adjust without touching the main script
- Chrome must be fully closed before the script launches, or the profile will fail to load

**Headless mode:**

- Controlled by a `HEADLESS` boolean in `config.py` (default: `True`)
- When `True`, add `--headless=new` to ChromeOptions

**Scheduling:**

- Native Python only — no external scheduling libraries, no OS task scheduler
- Use `datetime` + `time.sleep()` to wait until a user-specified run time
- The run time is set in `config.py` as a string in `"HH:MM"` 24-hour format (e.g., `"00:00"` for midnight)
- If the target time has already passed today, schedule for the same time tomorrow
- Print a human-readable countdown message before sleeping (e.g., "Waiting 6.3 hours until 00:00...")

**Retry logic:**

- Wrap each action (click, find element, etc.) in a retry helper
- Retry config lives in `config.py`:
  - `MAX_RETRIES = 5` (default)
  - `RETRY_DELAY_SECONDS = 60` (default, delay between retries)
- On failure, log the attempt number and exception, wait `RETRY_DELAY_SECONDS`, then retry
- After all retries exhausted, raise the exception so the script fails clearly

**Wait strategy:**

- Use Selenium's `WebDriverWait` with `expected_conditions` (explicit waits) instead of `time.sleep()` for page/element loading
- Default wait timeout configurable in `config.py` as `ELEMENT_WAIT_SECONDS = 15`

---

## File Structure

Produce exactly these files:

```
<project-folder>/
  config.py        # All user-configurable settings
  main.py          # Entry point: scheduling + runs the automation
  automation.py    # Core browser logic (setup, actions, teardown)
  requirements.txt # Just: selenium
  README.md        # Setup and usage instructions
```

After writing all files, run the following commands in the project folder:

```
python -m venv venv
pip install -r requirements.txt
```

---

## config.py Contents

The plan must include a `config.py` with at least these fields (with sensible defaults and comments):

```python
# Scheduling
RUN_TIME = "00:00"  # 24-hour HH:MM format

# Chrome profile (find yours using how-to.md)
CHROME_PROFILE_PATH = r"C:\Users\YOUR_USERNAME\AppData\Local\Google\Chrome\User Data"
PROFILE_DIRECTORY = "Default"  # or "Profile 1", "Profile 2", etc.

# Display
HEADLESS = True  # Set to False to watch the browser run (useful for debugging)

# Retry settings
MAX_RETRIES = 5
RETRY_DELAY_SECONDS = 60

# Waits
ELEMENT_WAIT_SECONDS = 15
```

---

## README.md Contents

The README must include:

1. **What this script does** — a one-paragraph plain-English description of the automation
2. **Setup** — exact commands to create the venv and install dependencies:
   ```
   python -m venv venv
   venv\Scripts\activate      # Windows
   pip install -r requirements.txt
   ```
3. **Configuration** — instruct the user to open `config.py` and fill in:
   - `CHROME_PROFILE_PATH` and `PROFILE_DIRECTORY` (with a note to check `chrome://version`)
   - `RUN_TIME` (24-hour format)
   - `HEADLESS` (True to run invisibly, False to watch)
4. **Usage** — how to run the script:
   ```
   venv\Scripts\activate
   python main.py
   ```
5. **Important note** — Chrome must be fully closed before running

---

## What the Automation Must Do

> **[FILL THIS IN BEFORE PASTING THE PROMPT]**
>
> Replace this section with a plain-English description of what the automation should do. Be specific. Include:
>
> - The URL to visit
> - The exact sequence of actions (click, type, wait, etc.)
> - Any conditional logic (if X is visible, do Y; otherwise do Z)
> - Anything to verify or confirm before/after an action
>
> **Example:**
>
> ```
> 1. Navigate to https://example.com/dashboard
> 2. Wait for the page to fully load
> 3. If a "Dismiss" modal is visible, click it
> 4. Click the button with the text "Submit Daily Report"
> 5. Wait for a success message to appear on screen
> 6. Close the browser
> ```
>
> See `how-to.md` for step-by-step instructions on how to identify the right elements to click.

---

## Notes for Claude

- Keep the code simple and readable — this is a low-scale, single-site automation
- Do not over-engineer; avoid unnecessary abstractions
- Use explicit waits (`WebDriverWait`) everywhere — never bare `time.sleep()` for element loading
- The random delays between actions are for anti-detection only, not for waiting on elements
- All configurable values must live in `config.py`, not hardcoded in other files
- After planning, wait for approval before writing any code
