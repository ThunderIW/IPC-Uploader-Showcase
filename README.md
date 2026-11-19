<div align="center">

<img src="Media/Logos/Wiecon_logo-removebg-preview.png" alt="Wiecon logo" width="160">

# IPC Uploader

**One form that sends calculation reports to Wiecon's hourly AI proof-check agent, and brings the finished checks back.**

![Version 3.0.0](https://img.shields.io/badge/version-3.0.0-16235c?style=for-the-badge) ![Python 3.14](https://img.shields.io/badge/python-3.14-2f6fd0?style=for-the-badge&logo=python&logoColor=white) ![NiceGUI 3.18](https://img.shields.io/badge/NiceGUI-3.18-1f8a70?style=for-the-badge) ![uv](https://img.shields.io/badge/uv-packaged-b45309?style=for-the-badge)

<a href="https://youtu.be/4Phy4q6Dfmw"><img src="docs/video/video_thumbnail_play.png" alt="Play the IPC Uploader promo video" width="840"></a>

<sub>A 48-second promo: one upload from form to finished check, and the queue dashboard on the office screen. Click the image to play.<br>Music: <i>Upbeat Energetic Corporate</i> by Fretbound (<a href="https://hypeddit.com/v8zrnu">download</a>).</sub>

<sub>This is a showcase repository. It describes the app; the source code stays private. Every engineer, project and file name in the screenshots is invented.</sub>

</div>

> [!CAUTION]
> ### Proprietary software. Not open source.
>
> **Copyright © 2026 Immanuel Wiessler and Wiecon Consultants Taiwan. All rights reserved.**
>
> This repository holds documentation only. The IPC Uploader's source code, builds and data are private. To ask about the project, email **[im.wiessler@gmail.com](mailto:im.wiessler@gmail.com)**.

<br>

<div align="center">

<table>
<tr><td><b>Input</b></td><td>Calculation report PDFs, the design-code documents to check against, and optional supporting documents</td><td>entered by the engineer in one form</td></tr>
<tr><td><b>Output</b></td><td>One zip per submission in the IPC Inbox for that design code</td><td>finished checks come back from the Outbox</td></tr>
<tr><td><b>Stack</b></td><td>Python 3.14, <a href="https://nicegui.io">NiceGUI</a>, <a href="https://fastapi.tiangolo.com">FastAPI</a>, <a href="https://sqlmodel.tiangolo.com">SQLModel</a>, fsspec over FTP</td><td>run and packaged with <a href="https://docs.astral.sh/uv/">uv</a></td></tr>
<tr><td><b>Platform</b></td><td>Windows PCs across the firm's offices</td><td>runs locally, opens in the browser</td></tr>
</table>

</div>

---

## 01. Why this exists

![Part of](https://img.shields.io/badge/part_of-the_IPC_proof--check_pipeline-16235c?style=flat-square)

Wiecon runs an AI agent every hour that works through a queue of calculation reports and writes up an Independent Proof Check (IPC) for the checking engineers. The agent reads its work from an Inbox on the IPC server, and every submission has to arrive as one complete package: the reports, the code documents to check them against, any supporting documents, and the project they belong to.

The IPC Uploader turns that into a single form. The engineer fills it in, clicks **Upload**, and the package lands in the right place in the right shape. When the check is done, the result shows up in the same app.

<!-- TODO before/after: how long assembling and filing one check took by hand vs with the app, and how many checks have gone through since 28 October 2026. Real figures only. -->

```mermaid
flowchart LR
    A[Engineer] --> B[IPC Uploader<br/>on the engineer's PC]
    B -- "one zip per submission" --> C[(IPC server<br/>01 - Inbox / design code)]
    C --> D[IPC proof-check agent<br/>runs every hour]
    D --> E[(IPC server<br/>02 - Outbox)]
    E -- "finished checks" --> B
```

---

## 02. Testimonials

![From the engineers using it](https://img.shields.io/badge/from-the_engineers_using_it-16235c?style=flat-square)

<!-- TODO: a short quote from an engineer who uses the app, with their name and role, used with their permission. -->

---

## 03. Features

**1.** ![One form, one upload](https://img.shields.io/badge/One_form,_one_upload-b45309?style=flat-square)
Engineer name, project name, design code (for example IRC) and code documents, report PDFs and optional supporting documents, all on one page. Codes you use often can be saved as a **preset** and loaded in one click.

**2.** ![Packaged for the agent](https://img.shields.io/badge/Packaged_for_the_agent-b45309?style=flat-square)
Files are zipped on the engineer's PC and named after the engineer, design code, project and time, for example `Alex Chen_check_for_IRC_Wadi_2026-11-12_12-55-39.zip`. A `Project.txt` inside tells the agent which project the reports belong to. The zip goes to the Inbox folder for its design code.

**3.** ![Results come back](https://img.shields.io/badge/Results_come_back-b45309?style=flat-square)
A pill in the header counts the finished checks waiting in the Outbox, and a green dot blinks when a new one arrives. Result zips can be downloaded straight from the app.

**4.** ![Uploads list](https://img.shields.io/badge/Uploads_list-b45309?style=flat-square)
An **Uploads** button shows everything you've sent that is still being checked. An upload leaves the list once its result is in the Outbox.

**5.** ![Fails loudly](https://img.shields.io/badge/Fails_loudly,_never_silently-b45309?style=flat-square)
The app checks that the zip arrived before it clears your files. If sending fails, it says **Upload failed** with the reason and keeps your files. If the IPC server can't be reached, it says so and closes rather than sending your reports somewhere else.

**6.** ![Works from every office](https://img.shields.io/badge/Works_from_every_office-b45309?style=flat-square)
Uploads and downloads go over FTP, so the app works from offices where the server's shared folder can't be opened directly. PDFs up to 300 MB each are accepted.

**7.** ![Double-click to start](https://img.shields.io/badge/Double--click_to_start-b45309?style=flat-square)
Engineers start it with one `.bat` file. On first use it sets up uv, then starts the app on the local machine with no console window and opens it in the browser. Closing the tab stops it. The desktop icon shows the installed version.

**8.** ![Queue dashboard](https://img.shields.io/badge/Queue_dashboard-b45309?style=flat-square)
A separate **IPC Check Queue** page runs on its own computer as an office screen, so the whole team can see who is waiting, whose report is being checked and what has finished. See [05. Queue dashboard](#05-queue-dashboard).

---

## 04. Screenshots

![The app at work](https://img.shields.io/badge/the_app_at_work-16235c?style=flat-square)

**The upload form, filled in.** Engineer, project and design code at the top, the report PDFs and supporting documents below, one **Upload** button.

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

## 05. Queue dashboard

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

## 06. Architecture

![NiceGUI](https://img.shields.io/badge/NiceGUI-front_end-1f8a70?style=flat-square) ![FastAPI](https://img.shields.io/badge/FastAPI-back_end-009688?style=flat-square) ![SQLModel](https://img.shields.io/badge/SQLModel-database-b45309?style=flat-square) ![fsspec](https://img.shields.io/badge/fsspec-FTP-2f6fd0?style=flat-square)

| Part | What it does |
| :--- | :--- |
| **Front end** | A NiceGUI page in pure Python: the upload form, presets, the Uploads list and the Outbox pill. |
| **File layer** | Finds the data root, lists the design codes, counts the Outbox, and zips each submission into the right Inbox folder over FTP (fsspec and universal-pathlib). |
| **Back end** | A FastAPI service with a SQLModel database that records uploads and presets. |
| **Queue dashboard** | One self-contained HTML page served by the back end and shown on an office screen; it polls the uploads list and animates the queue. |
| **Release** | A PowerShell script builds a versioned release zip with the launcher, settings and release notes. Server login details ship with the release, never in the repository. |
| **Video guide** | A short motion-graphics guide that walks engineers through each step of an upload. |
| **Promo video** | A 48-second promo built with HyperFrames from screens of the app and the dashboard on invented data. |

---

## 07. Example workflow

![Submitting a report for checking](https://img.shields.io/badge/submitting_a_report_for_checking-16235c?style=flat-square)

1. **Start the app.** Double-click `IPC Uploader.bat`; it opens in the browser.
2. **Fill in the form.** Engineer name, project name, design code and the code documents, or load a preset.
3. **Add the files.** Drop in the report PDFs and, if there are any, the supporting documents (drawings, client specification).
4. **Upload.** The app zips everything, sends it to the Inbox, confirms it arrived and opens the Uploads list.
5. **Collect the result.** When the agent finishes, the Outbox pill updates and the result zip can be downloaded from the app.

---

## 08. What it doesn't do

| Not included | Status |
| :--- | :--- |
| Checking the reports | Done by the IPC proof-check agent, not the uploader |
| Non-PDF uploads | Out of scope: reports and supporting documents must be PDFs |
| A fallback server | Removed in 2.0.4: if the IPC server is unreachable, the app stops |

---

## 09. Changelog

![Latest 3.0.0](https://img.shields.io/badge/latest-3.0.0-16235c?style=flat-square) ![27 releases](https://img.shields.io/badge/releases-27-64748b?style=flat-square) ![First release](https://img.shields.io/badge/first_release-2026--10--28-64748b?style=flat-square)

The main milestones, newest first.

| Version | Released | Headline |
| :--- | :--- | :--- |
| **3.0.0** | 2026-11-19 | New Wiecon-blue look; files are kept and listed as you add them, and missing fields are marked |
| **2.0.7** | 2026-11-16 | Uploads leave the list once their result is in the Outbox |
| **2.0.5** | 2026-11-13 | New Uploads list in the top bar |
| **2.0.3** | 2026-11-12 | PDFs up to 300 MB each |
| **2.0.2** | 2026-11-12 | Project name on every submission, saved inside the zip |
| **2.0.0** | 2026-11-11 | Reports can be sent without supporting documents |
| **1.10.2** | 2026-11-10 | Presets for frequently used codes |
| **1.10.0** | 2026-11-09 | Uploads and downloads over FTP, so every office can use it |
| **1.9.0** | 2026-11-06 | Download finished checks straight from the app |
| **1.4.0** | 2026-11-02 | One-file launcher: extract and double-click |
| **1.0.0** | 2026-10-28 | First release |

---

## 10. Copyright

> [!IMPORTANT]
> **Proprietary and confidential. This is not open-source software.**

Copyright © 2026 Immanuel Wiessler and Wiecon Consultants Taiwan. All rights reserved.

This repository is published to describe the IPC Uploader. It contains no source code, and publishing it grants no licence to the software. For questions or permission requests, email **[im.wiessler@gmail.com](mailto:im.wiessler@gmail.com)**.
