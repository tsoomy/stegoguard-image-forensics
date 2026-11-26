# Lesson 3 · StegoGuard Image Forensics with GitHub Actions :)

Detect appended data in a PNG file, interpret the scanner’s findings, and test a new keyword rule. Complete the lab in your own GitHub fork using the web browser.

## What you will learn

- Use the PNG signature and the `IEND` chunk to explain where a PNG datastream begins and ends.
- Identify which step failed in a GitHub Actions run and explain what the scanner found.
- Compare two scans of the same image to show the effect of adding a detection rule.

## How the lab works

`lab_mode.txt` controls which image `make_demo_image.py` generates. In `clean` mode the image ends at the `IEND` chunk. In `suspicious` mode the generator adds harmless demo text after that chunk. Each workflow run generates a fresh `images/student_image.png` and runs `scanner.py`.

The scanner checks the file signature and looks for the expected ending marker, trailing data, and configured keywords. It searches file bytes for keywords without regard to ASCII letter case. These are basic checks. A keyword can occur in legitimate content, and a passing scan means the configured checks found no issue in the file that was scanned. Use the generated demo image and harmless text throughout the lab.

| Repository path | Purpose |
| --- | --- |
| `lab_mode.txt` | Selects `clean` or `suspicious` mode |
| `make_demo_image.py` | Generates the demo PNG |
| `scanner.py` | Scans the image and reports each finding |
| `images/` | Holds the generated image in the workflow |
| `.github/workflows/stegoguard.yml` | Defines when and how GitHub Actions runs the lab |

## 1. Run a clean baseline

1. Open the [starter repository](https://github.com/RafikHamza/stegoguard-image-forensics), select **Fork**, choose your own account, and select **Create fork**.
2. In your fork, confirm that `lab_mode.txt` contains `clean`. If you are using an earlier fork with a blank file, enter `clean` and commit directly to `main`.
3. Open **Actions** and enable workflows if GitHub prompts you. In the sidebar, select **StegoGuard Image Forensics**. Select **Run workflow**, choose `main`, and start the run.
4. Open the run, select **image-forensics**, and expand **Run StegoGuard scanner**. Confirm that it scanned `images/student_image.png` and printed `Status: CLEAN`. Save the run link.

For the rest of the lab, use the pencil icon to edit files and commit directly to `main` in your fork. Each commit starts a new workflow run. Wait for it to finish and match the run to the commit you just made.

## 2. Inspect a suspicious scan

1. Change `lab_mode.txt` to `suspicious` and commit.
2. Open the run for that commit and expand **Run StegoGuard scanner**. Confirm that the step failed and reported both `Extra data found after PNG ending marker` and `Suspicious keyword found inside file: hidden_message`. Record the run link and both findings.
3. Change `lab_mode.txt` back to `clean` and commit. Confirm that the workflow passes after generating and scanning a fresh image.

An error in **Create demo image** means the scanner step did not run. Check that `lab_mode.txt` contains one of the two allowed values before interpreting a red run as detection evidence.

## 3. Test a new keyword rule

Use `confidential` or choose another harmless ASCII keyword outside the starter list that is absent from the rest of the demo text.

1. Open `make_demo_image.py`. In the demo message near the bottom, replace `HIDDEN_MESSAGE` with `CONFIDENTIAL` or the uppercase spelling of your chosen keyword. Keep the rest of the message unchanged and commit.
2. Set `lab_mode.txt` to `suspicious` and commit. In this run, the original scanner should report the trailing data. Verify that the log has no finding for your new keyword and save the run link.
3. In `scanner.py`, add your keyword to `SUSPICIOUS_WORDS` as lowercase bytes. For example, add `b"confidential",` as a new entry. Leave the generator and mode as they are. Commit the scanner change.
4. Open the run for the scanner commit. The trailing-data finding should still appear, and the scanner should now print a new line such as `Suspicious keyword found inside file: confidential`. Save the run link and the matching log line.
5. Restore `clean` in `lab_mode.txt` and commit. Confirm the scanner prints `Status: CLEAN` and save this final run link.

## Submit your work

Submit the link to your fork and the five workflow-run links below with the relevant scanner log lines. Add screenshots if required by your course.

| Run | Scanner evidence |
| --- | --- |
| Initial clean scan | File name and `Status: CLEAN` |
| Original suspicious scan | Trailing-data and `hidden_message` findings |
| New demo keyword with the original scanner | Trailing-data finding |
| Same demo image with the updated scanner | Trailing-data and new keyword findings |
| Final clean scan | File name and `Status: CLEAN` |

In two or three sentences, explain why both keyword-test runs failed and identify the log evidence that shows your new rule worked.

**Optional.** Describe one situation where a legitimate image could match a configured keyword. Explain what evidence you would check before treating it as suspicious.
