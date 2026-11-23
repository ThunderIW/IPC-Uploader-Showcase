<div align="center">


<img src="docs/readme/hero.svg" width="100%" alt="IPC Uploader: one form that packages calculation reports for Wiecon's hourly AI proof check and brings the finished checks back. Beside it, the zip the agent receives and its path from the Inbox through the AI check to the Outbox.">

<a href="https://youtu.be/4Phy4q6Dfmw"><img src="docs/video/video_thumbnail_play.png" alt="Play the IPC Uploader promo video" width="840"></a>

<sub>A 48-second promo: one upload from form to finished check, and the queue dashboard on the office screen. Click the image to play.<br>Music: <i>Upbeat Energetic Corporate</i> by Fretbound (<a href="https://hypeddit.com/v8zrnu">download</a>).<br>Every engineer, project and file name in this README is invented.</sub>

</div>

> [!NOTE]
> **This is a showcase repository.** It holds the README, screenshots and the promo video so you can see what the program does. The source code and the installers are private.

<br>

## About this project

An internal tool for Wiecon Consultants Taiwan, a structural engineering consultancy with an office of 17 engineering and CAD staff. The first version shipped on 28 October 2026, and it reached 3.0.0 three weeks later after 27 releases, each one shaped by what the engineers using it ran into.

Built by [**@ThunderIW**](https://github.com/ThunderIW).

<div align="center">

<table>
<tr><td><b>Problem</b></td><td>Engineers proof-checked every calculation report by hand, redoing the calculations and comparing them with the design codes</td><td>slow, repetitive work</td></tr>
<tr><td><b>Solution</b></td><td>An AI agent makes a first pass every hour; one form packages the reports for it and brings the finished checks back</td><td>plus an office-screen queue dashboard</td></tr>
<tr><td><b>Stack</b></td><td><a href="https://nicegui.io/">NiceGUI</a>, <a href="https://fastapi.tiangolo.com/">FastAPI</a>, <a href="https://sqlmodel.tiangolo.com/">SQLModel</a> on PostgreSQL and SQLite, <a href="https://filesystem-spec.readthedocs.io/">fsspec</a> over FTP</td><td><a href="https://www.python.org/">Python</a> 3.14 with <a href="https://docs.astral.sh/uv/">uv</a>, Docker, GitHub Actions, NSIS</td></tr>
<tr><td><b>Platform</b></td><td>Windows PCs across the firm's offices</td><td>runs locally, opens in the browser</td></tr>
</table>

</div>

<br>

| # | Section | What's in it |
| :---: | --- | --- |
| 01 | [Why this exists](#01-why-this-exists) | Proof checks done by hand, and what replaced them |
| 02 | [Features](#02-features) | The eight things the program gives you |
| 03 | [Screenshots](#03-screenshots) | The form, the Uploads list and the Outbox |
| 04 | [Queue dashboard](#04-queue-dashboard) | The office screen that shows who is in line |
| 05 | [Architecture](#05-architecture) | Front end, file layer, back end, dashboard, CI and release |
| 06 | [Design decisions and lessons](#06-design-decisions-and-lessons) | What went wrong in real use, and how the design changed because of it |
| 07 | [Example workflow](#07-example-workflow) | A complete run on invented data, from the form to the downloaded result |
| 08 | [Scope and known issues](#08-scope-and-known-issues) | What it doesn't do, and two behaviours worth knowing |
| 09 | [Milestones](#09-milestones) | How the program has grown |
| 10 | [Copyright and license](#10-copyright-and-license) | Proprietary software |

<br>

---

## 01. Why this exists

![Before](https://img.shields.io/badge/before-checked_by_hand_against_the_codes-64748b?style=flat-square) ![Now](https://img.shields.io/badge/now-AI_first_pass_every_hour-16235c?style=flat-square)

An Independent Proof Check (IPC) confirms that a structural design is right before it goes out. At Wiecon it used to be done entirely by hand: an engineer worked through each calculation report, redid the calculations and compared them with the design codes.

Wiecon now runs an AI agent every hour that makes a first pass of that check and writes up an IPC for the checking engineers to review.

The agent needs each submission as one complete package in the Inbox folder for its design code: the reports, the code documents to check them against, any supporting documents, and the project they belong to. The IPC Uploader is how the reports get there. The engineer fills in one form, clicks **Upload**, and the package lands in the right place in the right shape. When the check is done, the result shows up in the same app.

![On the engineer's PC, the engineer fills in one form and clicks Upload; the IPC Uploader sends one zip per submission to the IPC server's 01 - Inbox, one folder per design code. Every hour the IPC proof-check agent picks it up, makes an AI first pass and writes the IPC to 02 - Outbox, and the result comes back to the IPC Uploader, ready to download.](docs/diagrams/why-this-exists.svg)

---

## 02. Features

**1.** ![One form, one upload](https://img.shields.io/badge/One%20form%2C%20one%20upload-b45309?style=flat-square)<br>
Engineer name, project name, design code (for example IRC) and code documents, report PDFs and optional supporting documents, all on one page. Missing fields are marked in red when you click **Upload**, and the files you added are never thrown away.

**2.** ![Presets](https://img.shields.io/badge/Presets-2f6fd0?style=flat-square)<br>
Codes you use often can be saved as a **preset** and loaded in one click. Presets can be edited or deleted from Settings, and they are kept when a newer version is installed.

**3.** ![Packaged for the agent](https://img.shields.io/badge/Packaged%20for%20the%20agent-0f766e?style=flat-square)<br>
Files are zipped on the engineer's PC and named after the engineer, design code, project and time, for example `Alex Chen_check_for_IRC_Westgate Interchange_2026-11-19_10-42-36.zip`. A `Project.txt` and a `Codes_to_check.md` inside tell the agent which project the reports belong to and which codes to check against. The zip goes to the Inbox folder for its design code.

**4.** ![Results come back](https://img.shields.io/badge/Results%20come%20back-1f8a70?style=flat-square)<br>
A pill in the header counts the finished checks waiting in the Outbox, and a green dot blinks when a new one arrives. Result zips can be downloaded straight from the app.

**5.** ![Uploads list](https://img.shields.io/badge/Uploads%20list-16235c?style=flat-square)<br>
An **Uploads** button shows everything you've sent that is still being checked. An upload leaves the list once its result is in the Outbox.

**6.** ![Fails loudly, never silently](https://img.shields.io/badge/Fails%20loudly%2C%20never%20silently-dc2626?style=flat-square)<br>
The app checks that the zip arrived before it clears your files. If sending fails, it says **Upload failed** with the reason and keeps your files. If the IPC server can't be reached, it says so and closes rather than sending your reports somewhere else.

**7.** ![Works from every office](https://img.shields.io/badge/Works%20from%20every%20office-7c3aed?style=flat-square)<br>
Uploads and downloads go over FTP, so the app works from offices where the server's shared folder can't be opened directly. PDFs up to 300 MB each are accepted.

**8.** ![Install once, double-click to start](https://img.shields.io/badge/Install%20once%2C%20double--click%20to%20start-b45309?style=flat-square)<br>
Engineers install it with one `Setup.exe` from the office share: no admin rights, an icon on the desktop and in the Start menu, and their settings and presets kept on updates. The icon sets up uv on first use, then starts the app on the local machine with no console window and opens it in the browser. Closing the tab stops it.

---

## 03. Screenshots

![The app at work](https://img.shields.io/badge/the_app_at_work-16235c?style=flat-square)

**The upload form, filled in.** Engineer, project and design code at the top, the report PDFs and supporting documents below, each listed under its box, and one **Upload** button that stays at the bottom of the window.

<div align="center">
<img src="docs/screenshots/01_upload_form.jpg" alt="The upload form with an engineer, project, design code, two reports and a drawing filled in" width="760">
</div>

**The Uploads list.** Everything you've sent that is still being checked. An upload leaves the list once its result is in the Outbox.

<div align="center">
<img src="docs/screenshots/02_uploads_list.jpg" alt="The Uploads menu listing four uploads still being checked" width="760">
</div>

**The Outbox pill.** Point at it to see the finished checks, each with a download button.

<div align="center">
<img src="docs/screenshots/03_outbox_downloads.jpg" alt="The Outbox pill open, listing three finished checks with download buttons" width="760">
</div>

---

## 04. Queue dashboard

![On the office screen](https://img.shields.io/badge/on_the_office_screen-16235c?style=flat-square) ![Live from the API](https://img.shields.io/badge/live_from-the_IPC_Uploader_API-009688?style=flat-square)

The **IPC Check Queue** is a separate page that runs on its own computer, on a screen in the office, so every engineer can see the line at a glance. It doesn't need the uploader: it reads the list of uploads from the IPC Uploader's FastAPI back end every few seconds and turns it into a small bridge-site scene. Each upload is a stick figure named after its engineer.

<div align="center">
<img src="docs/screenshots/04_dashboard_waiting.jpg" alt="The IPC Check Queue: three engineers waiting on the bridge, Sam Lee's report at the checker, and the In line and Checked lists below" width="840">
</div>

The page follows the uploads through these states:

| State | What the screen shows |
| :--- | :--- |
| **Waiting in line** | Engineers stand on the bridge deck in upload order, numbered from the front. Their waiting time counts up, and they get bored: checking phones, yawning, *"Are we there yet?"* |
| **New upload** | A new figure walks in from the left and joins the back of the line. |
| **Check starts** | When the checker is free, the one at the front runs to the **GOAL** flag, rides the lift down to the drafting table, and everyone behind moves up a place. |
| **Being checked** | The checker's monitor works through the report section by section (*§ 1/7 Loads*, *§ 2/7 Bending*) and the printer prints the checked pages. |
| **Check done** | When the result is in the Outbox, the monitor says **CHECKED ✓**, the engineer walks off with the bundle of checked calculations and moves to the **Checked** list. |
| **Nobody waiting** | *No one in line, waiting for new uploads.* The monitor reads **READY** and the check engineer dozes off. |

<div align="center">

<table>
<tr>
<td align="center"><img src="docs/screenshots/05_dashboard_new_upload.jpg" alt="Priya Singh walking in across the bridge to join the line" width="410"><br><sub><b>New upload</b>: Priya walks in and joins the line.</sub></td>
<td align="center"><img src="docs/screenshots/06_dashboard_check_done.jpg" alt="The monitor reads CHECKED, Sam Lee leaves the desk and Maya Patel runs to the flag" width="410"><br><sub><b>Check done</b>: Sam leaves, Maya runs to the flag.</sub></td>
</tr>
<tr>
<td align="center"><img src="docs/screenshots/07_dashboard_next_check.jpg" alt="Maya Patel at the checker's desk, the monitor processing her report" width="410"><br><sub><b>Next check</b>: Maya's report is being checked.</sub></td>
<td align="center"><img src="docs/screenshots/08_dashboard_empty.jpg" alt="The empty queue: no one in line, the monitor reads READY" width="410"><br><sub><b>Nobody waiting</b>: the checker is ready.</sub></td>
</tr>
</table>

</div>

A real upload leaves the line when its result is in the Outbox, not after a set time. The page's Settings set how often it asks the server, and can add test names to try the animation.

---

## 05. Architecture

![NiceGUI](https://img.shields.io/badge/front_end-NiceGUI-2f6fd0?style=flat-square) ![fsspec](https://img.shields.io/badge/files-fsspec_over_FTP-0f766e?style=flat-square) ![FastAPI](https://img.shields.io/badge/back_end-FastAPI-009688?style=flat-square) ![SQLModel](https://img.shields.io/badge/ORM-SQLModel-16235c?style=flat-square) ![PostgreSQL](https://img.shields.io/badge/database-PostgreSQL-2f6fd0?style=flat-square) ![GitHub Actions](https://img.shields.io/badge/CI-GitHub_Actions-1f8a70?style=flat-square) ![NSIS](https://img.shields.io/badge/installer-NSIS-b45309?style=flat-square)

A NiceGUI front end on the engineer's PC, a file layer that talks to the IPC server over FTP, and a FastAPI back end on PostgreSQL that the queue dashboard reads, on Python 3.14.

![An engineer's upload goes from the browser through IPC_uploader_frontend.py and code_finder.py into the IPC server's Inbox over FTP, with a record in presets.db and PostgreSQL; the finished check comes back from the Outbox for download, and the IPC Check Queue page reads the uploads from BackendAPI and PostgreSQL](docs/diagrams/architecture.svg)

<sub>The app never calls the checker: it hands off through the Inbox and Outbox folders on the IPC server. The hourly AI proof check between them is a separate system and is not drawn.</sub>

### Front end

![NiceGUI · one page](https://img.shields.io/badge/NiceGUI%20%C2%B7%20one%20page-2f6fd0?style=flat-square)

NiceGUI 3.18. One page holds the whole app: the upload form, the presets, the Uploads menu and the Outbox pill, with the preset editor and the confirmations as dialogs. The parts that change are `@ui.refreshable` functions, re-rendered on demand instead of reloading the page. The look follows the Wiecon logo: a navy-to-blue header, a white form on a blue page, and one green **Upload** button pinned to the bottom of the window.

### File layer

![fsspec · FTP · one zip per submission](https://img.shields.io/badge/fsspec%20%C2%B7%20FTP%20%C2%B7%20one%20zip%20per%20submission-0f766e?style=flat-square)

`code_finder.py` finds the data root, lists the design codes and their documents, counts the Outbox and zips each submission into the right Inbox folder. The files are zipped on the engineer's PC first, then sent over FTP with fsspec and universal-pathlib, and the app checks that the zip arrived before it clears the form. Server login details ship with each release, never in the repository.

### Back end

![FastAPI · SQLModel · PostgreSQL](https://img.shields.io/badge/FastAPI%20%C2%B7%20SQLModel%20%C2%B7%20PostgreSQL-009688?style=flat-square)

A FastAPI service records the uploads in PostgreSQL (through SQLModel, run with Docker Compose) and serves them to the queue dashboard. The app keeps its presets and its own Uploads list in a small SQLite database next to the program, which is why presets survive an update.

### Queue dashboard

![One HTML page · polls the API](https://img.shields.io/badge/one%20HTML%20page%20%C2%B7%20polls%20the%20API-7c3aed?style=flat-square)

One self-contained HTML page served by the back end and shown on an office screen. It polls the uploads list and animates the queue; see [04. Queue dashboard](#04-queue-dashboard).

### CI and release

![GitHub Actions · PowerShell · NSIS Setup.exe](https://img.shields.io/badge/GitHub%20Actions%20%C2%B7%20PowerShell%20%C2%B7%20NSIS%20Setup.exe-b45309?style=flat-square)

Every pull request runs a GitHub Actions pipeline in three steps:

1. **Version bump.** The version and release date are set from the PR's labels (major, minor or patch), and the commit goes back to the branch.
2. **Lint.** Ruff lint and format checks, and a compile check.
3. **Package check.** The release package is built and its contents are checked.

A PowerShell script builds each version with the launcher, settings and release notes. An NSIS installer (`Setup.exe`) puts it on each engineer's PC: per user, no admin rights, with a desktop icon and an uninstaller, and tested with a silent install, update and uninstall before it ships. A motion-graphics video guide walks engineers through each step, and the promo was built with HyperFrames from screens of the app and the dashboard on invented data.

### Project layout

![About 2,800 lines of app code in Python · one HTML dashboard](https://img.shields.io/badge/about%202%2C800%20lines%20of%20app%20code%20in%20Python%20%C2%B7%20one%20HTML%20dashboard-0f766e?style=flat-square)

About 2,800 lines of app code in Python, more than half of it in the front end, plus the dashboard as one HTML page.

<table>
<tr><th align="left">Module</th><th align="right">Lines</th><th align="left">Share of the code in the repo</th></tr>
<tr><td><code>IPC Check Queue stick men.html</code></td><td align="right">2216</td><td><code>████████████████████</code></td></tr>
<tr><td><code>IPC_uploader_frontend.py</code></td><td align="right">1588</td><td><code>██████████████</code></td></tr>
<tr><td><code>backend/database.py</code></td><td align="right">381</td><td><code>███</code></td></tr>
<tr><td><code>code_finder.py</code></td><td align="right">367</td><td><code>███</code></td></tr>
<tr><td><code>server_database.py</code></td><td align="right">353</td><td><code>███</code></td></tr>
<tr><td><code>BackendAPI.py</code></td><td align="right">74</td><td><code>█</code></td></tr>
</table>

Where to start reading, depending on what you are looking for:

| To understand | Read | Start at |
| --- | --- | --- |
| The whole app | `IPC_uploader_frontend.py` | `main_page()`, which builds the header and the form |
| How a submission is zipped and sent | `code_finder.py` | the zip-and-send method that names the zip and writes `Project.txt` |
| How an upload leaves the Uploads list | `IPC_uploader_frontend.py` | `clear_finished_uploads` |
| What the dashboard reads | `BackendAPI.py` | the uploads endpoint |
| How a release is cut | `release/Scripts/package_release.ps1` | the version read from `pyproject.toml` |

---

## 06. Design decisions and lessons

![From real use](https://img.shields.io/badge/from-real_use-16235c?style=flat-square)

Most of the design came from problems the engineers ran into. These are the changes that shaped it most:

| Problem in real use | Decision | Why |
| --- | --- | --- |
| The app first ran straight from the network share, so everyone used one upload folder, and two engineers sending at the same time could mix their files (1.0.0). | Run on each engineer's PC, gather the files locally (1.1.0), and later install per user with a `Setup.exe` (1.8.1). | Each engineer gets their own folder, and a shared folder can no longer mix them. |
| Some offices couldn't open the server's shared folder at all. | Move uploads and downloads to FTP with fsspec (1.10.0). | One way in that works from every office, behind the same file interface. |
| A failed send left the form saying "Zipping" forever, and files could be lost. | Zip on the PC, send, then **check the zip arrived** before clearing the form (1.10.0). From 3.0.0 the files are kept on every failure. | An engineer should never have to wonder whether a check was sent. |
| When the IPC server was down, the app switched to a second server, so reports could end up where the agent never looked. | Remove the fallback: say *"Could not reach the IPC server"* and close (2.0.4). | Failing loudly is safer than succeeding in the wrong place. |
| Picking several design codes made the zip's name too long for Windows. | Keep the name short and list the codes in `Codes_to_check.md` inside the zip (1.10.1). | The name only needs to identify the upload; the agent reads the details from the file. |
| Engineers couldn't tell where their upload was in the hourly queue. | A queue dashboard on an office screen, fed by a FastAPI back end. | It answers "when is mine done?" for the whole office without anyone asking. |

---

## 07. Example workflow

![Invented data](https://img.shields.io/badge/data-invented-1f8a70?style=flat-square) ![Screenshots from a real run](https://img.shields.io/badge/screenshots-from_a_real_run-16235c?style=flat-square)

This section walks through one upload on invented data, from the form to the downloaded result.

### Step one: start the app

Double-click the **IPC Uploader** icon on the desktop. It opens in the browser; closing the tab stops it.

### Step two: fill in the form

| Field | Value |
| --- | --- |
| Engineer name | Alex Chen |
| Project name | Westgate Interchange |
| Design code | IRC |
| Additional codes | the IRC documents to check against, or a preset |

A saved preset fills in the design code and the codes in one click: <kbd>Presets</kbd> → the preset's button. New ones are made with <kbd>Add custom preset</kbd>.

### Step three: add the files

<kbd>Browse</kbd> in each box, or drop the PDFs on the **Drop PDFs here** bar. Each file is listed under its box with its size and a remove button.

| Box | File | Size |
| --- | --- | ---: |
| IPC reports | Pier P3 Design Calculation.pdf | 4.2 MB |
| IPC reports | Pile Cap PC3 Design Calculation.pdf | 2.7 MB |
| Supporting documents | General Arrangement Drawing.pdf | 1.6 MB |

Supporting documents are optional. Without them, the app asks *"No supporting documents - Upload the reports without them?"* before it sends anything.

### Step four: upload

<kbd>Upload</kbd> zips the three files on the PC, with the two text files the agent reads, and sends the zip to the IRC Inbox:

```
Alex Chen_check_for_IRC_Westgate Interchange_2026-11-19_10-42-36.zip
├── Pier P3 Design Calculation.pdf
├── Pile Cap PC3 Design Calculation.pdf
├── General Arrangement Drawing.pdf
├── Codes_to_check.md      the additional codes, numbered
└── Project.txt            Westgate Interchange
```

Once the server confirms the zip arrived, the form empties and the **Uploads** list opens with the new upload at the bottom.

### Step five: collect the result

Within the hour the agent writes its check to the Outbox. The pill in the header counts it, a green dot blinks, and <kbd>IPC checks completed in outbox</kbd> → the download arrow saves the result zip to the browser's Downloads folder. The upload leaves the Uploads list at the same time.

> [!TIP]
> The Upload button stays grey until at least one report is added, and stays at the bottom of the window while you scroll, so it is always in reach in a small window beside your drawings.

---

## 08. Scope and known issues

### What it doesn't do

| Not included | Status |
| --- | --- |
| Checking the reports | Done by the IPC proof-check agent, not the uploader |
| Non-PDF uploads | Out of scope: reports and supporting documents must be PDFs |
| A fallback server | Removed in 2.0.4: if the IPC server is unreachable, the app stops |
| Working off the office network | Not supported: the app needs the Wiecon network to reach the IPC server |

### Known issues

**The Uploads list only shows uploads sent from this PC.** Each install keeps its own Uploads list next to the program. An engineer who sends from two PCs sees each upload only on the PC it was sent from. The queue dashboard shows everyone's uploads.

**A finished check can take a few minutes to show.** The Outbox pill counts the results when the app opens and every 5 minutes after that. A result that arrives in between appears on the next count, not the moment it lands.

---

## 09. Milestones

![Latest 3.0.0](https://img.shields.io/badge/latest-3.0.0_·_Nov_2026-16235c?style=flat-square)

How the program has grown, in what it can do.

| Milestone | What it brought |
| :--- | :--- |
| **Installer with a desktop icon** | Set up like any other program, nothing to configure by hand |
| **Works from every office** | Uploads and downloads over FTP, and finished checks downloaded straight from the app |
| **Less typing** | Presets for frequently used codes, and the project name saved with every submission |
| **Uploads list and Outbox** | See what is waiting in line and pick up each result as soon as it is ready |
| **Latest** | 3.0.0, November 2026: the new Wiecon look, and files kept and listed as you add them |

<br>

## 10. Copyright and license

![Proprietary](https://img.shields.io/badge/license-proprietary-64748b?style=flat-square)

Copyright © 2026 Immanuel Wiessler and Wiecon Consultants Taiwan. All rights reserved.

This is proprietary software, not open source. The code is shared here so that it can be read and reviewed; it may not be copied, modified, distributed or used elsewhere without written permission. The full terms are in the [LICENSE](LICENSE) file.

For permission or questions: [im.wiessler@gmail.com](mailto:im.wiessler@gmail.com) · GitHub [@ThunderIW](https://github.com/ThunderIW)
