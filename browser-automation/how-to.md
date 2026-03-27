# How to Fill In "What the Automation Must Do"

This guide walks you through how to figure out exactly what to tell Claude about the website you want to automate. You'll use Chrome's built-in DevTools (the "Inspect" panel) to look up the identity of buttons and elements on the page.

---

## Step 1: Open the Website in Chrome

Navigate to the site you want to automate. Log in if needed. Get to the exact page where the automation will start.

---

## Step 2: Open DevTools

Right-click anywhere on the page and select **Inspect**, or press `F12`.

A panel will open — usually on the right or bottom of your screen. This is DevTools.

---

## Step 3: Use the Element Picker

In the top-left corner of the DevTools panel, click the **small cursor icon** (it looks like a mouse pointer over a square). It will turn blue when active.

Now click on the button or element you want to automate on the actual webpage.

DevTools will jump to the HTML code for that element in the Elements tab.

---

## Step 4: Copy the XPath

The XPath is an exact address for the element — Claude can drop it straight into the code.

**How to copy it:**

1. The element should already be highlighted in the Elements tab from Step 3
2. Right-click the highlighted line
3. Hover over **Copy**
4. Click **Copy XPath**

Paste what you copied into your task description next to the action. It will look like:

```
//*[@id="submit-btn"]
```

or

```
//button[contains(text(),'Generate Report')]
```

> **Don't use "Copy full XPath"** — that gives a fragile path like `/html/body/div[3]/...` that breaks if the page changes slightly. Plain **Copy XPath** is better.

---

## Step 5: Note the URL

Look at your browser's address bar. Write down the full URL of the page where each action happens.

If an action takes you to a new page, note that URL too.

---

## Step 6: Check for Pop-Ups or Modals

Before automating a click, watch what happens when you do it manually. Ask yourself:

- Does a modal/popup appear first that needs to be dismissed?
- Is there a confirmation dialog ("Are you sure?")?
- Does the page redirect?

If yes, note those steps too — they each need to be handled in the automation.

---

## Step 7: Identify Conditional Logic

If the automation should do different things depending on what's on the page, note the condition clearly.

Example: "If there's a red banner at the top of the page that says 'Pending Review', click the 'Approve' button. Otherwise, skip."

To identify the conditional element, use the element picker (Step 3) on the element that may or may not appear.

---

## Step 8: Write Your Task Description

Now write your task as a numbered list of plain-English steps. Be specific. Here's a template:

```
URL to start at: https://example.com/page

Steps:
1. Navigate to the URL above
2. Wait for the page to finish loading
3. [Optional] If [describe a visible element] is present, click it / dismiss it
   XPath: [paste XPath here]
4. Click [describe the button]
   XPath: [paste XPath here]
5. [If you need to type something] Type "[text]" into the [describe the input field]
   XPath: [paste XPath here]
6. Click [next button]
   XPath: [paste XPath here]
7. Wait for [describe what success looks like — a message, a new page, etc.]
8. Close the browser
```

---

## Step 9: Find Your Chrome Profile (for config.py)

The automation reuses your real Chrome profile so you're already logged in.

**To find your profile path and name:**

1. Open Chrome
2. In the address bar, type: `chrome://version` and press Enter
3. Look for the line that says **Profile Path**

It will look like:

```
C:\Users\YourName\AppData\Local\Google\Chrome\User Data\Default
```

- **CHROME_PROFILE_PATH** = everything up to (but not including) the last folder:
  `C:\Users\YourName\AppData\Local\Google\Chrome\User Data`
- **PROFILE_DIRECTORY** = the last folder name:
  `Default` (or `Profile 1`, `Profile 2`, etc.)

Copy these into `config.py`.

> **Important:** Chrome must be completely closed when you run the automation. If Chrome is open, the profile is locked and Selenium will fail to load it. You can close Chrome manually before starting the script, or just make sure to do it before the scheduled run time.

---

## Quick Reference: What to Include Per Element

For every element you want to interact with, provide:

1. **A plain-English description** of what it is (e.g., "the Submit button", "the email input field") — this helps Claude understand the intent
2. **The XPath** copied from DevTools (Step 4) — this is what Claude actually uses in the code

That's it. You don't need to manually read `id`, `class`, or any other attributes.

---

## Example: Fully Filled-In Task Description

```
URL to start at: https://myapp.com/reports

Steps:
1. Navigate to https://myapp.com/reports
2. Wait for the page to load
3. If a "What's new" modal is visible, click the X button to close it
   XPath: //*[@id="modal-close"]
4. Click the "Generate Report" button
   XPath: //button[contains(@class,'btn-generate')]
5. Wait up to 30 seconds for a green success banner that says "Report generated"
6. Close the browser
```
