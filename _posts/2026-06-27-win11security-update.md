---
layout: post
title: "Windows Privacy Toolkit — The Update That Changed Everything"
date: 2026-06-27
author: Will Johnson
tags: [windows, privacy, powershell, winforms, gui, ci-cd, development]
excerpt: "A real GUI, new privacy features, and a proper CI/CD pipeline. Here's what's changed since the initial release."
---

> Notes, ideas, and experiments — a place to ramble about what I'm building and learning.

---

## 🔄 Where We Left Off

Back in November, I wrote about the **Windows 11 Privacy Toolkit** — a PowerShell script that helps users lock down their privacy settings without needing to touch the registry manually.  
The plan was to eventually wrap it in a Python desktop app and make it accessible to everyone.

Well — plans change. And honestly? This change was better.

🔗 [View the project on GitHub →](https://github.com/willj4945/windows_privacy)

---

## 🖥️ The GUI Is Here (Just Not the One I Planned)

Instead of a Python frontend, I built the interface directly in PowerShell using **WinForms**.  

The result is a tabbed graphical interface — no terminal required, no extra runtime, no installer dependencies. Just a script that launches a proper window.

Why WinForms over Python?

- **No extra dependencies** — PowerShell ships with Windows, WinForms is already there
- **Direct access to system APIs** — no subprocess wrappers or permission gymnastics
- **Faster to iterate** — I could test and tweak without switching languages
- **Single file distribution** — one `.ps1` file, run as Administrator, done

The interface is built around checkboxes grouped by category, a progress log, and an "Apply Selected" button that creates a restore point before touching anything. Clean, functional, and still totally transparent — you can read every line of what it does.

---

## 🔒 New Privacy & Security Features

This update added several settings I'd been meaning to get to:

**Windows Recall**  
Microsoft's AI-powered screenshot history feature. It's disabled by default in the toolkit now — it's the kind of feature most privacy-conscious users will want off immediately.

**Cortana & Bing Search**  
Start menu Bing search integration and Cortana are both toggleable. These have been a friction point for a while, and it felt wrong not to have them in the list.

**Suggested Content**  
Lock screen promotions, suggested apps, and tips — all gone with a single checkbox.

**SMBv1**  
Already in the previous version conceptually, but the GUI label handling was fixed this round. SMBv1 is a well-known attack surface (EternalBlue, WannaCry) and there's rarely a reason to leave it enabled on a modern system.

**Network Protection & Controlled Folder Access**  
Both now properly surfaced in the UI with accurate labels.

---

## 🧪 CI/CD — Because "It Works on My Machine" Isn't Good Enough

The biggest infrastructure change this update: a **GitHub Actions pipeline** running on a real Windows runner.

It does two things:

1. **PSScriptAnalyzer** — PowerShell's official linter. Catches style issues, deprecated syntax, and common mistakes before they land in main.
2. **Pester** — PowerShell's unit testing framework. Tests for the new functions (Windows Recall, Cortana, Bing Search, bloat removal) run automatically on every push.

Setting this up on Windows required a bit of work — the Linux-specific command stubs I'd started with didn't translate, so I rewrote the workflow to run entirely on `windows-latest`. It's a small thing, but having CI catch issues before I push feels like a meaningful step up for a solo project.

---

## 🛠️ Under the Hood

A few code quality improvements alongside the new features:

- **Consistent function naming** — everything follows the same `Verb-Noun` PowerShell convention now
- **`CmdletBinding` added** — to `New-RestorePoint`, `Remove-BloatApp`, and other core functions, enabling proper pipeline and `-WhatIf` support
- **Streamlined prompts** — the GUI startup flow is cleaner, fewer unnecessary confirmation dialogs

These aren't glamorous changes, but they make the codebase easier to maintain and extend.

---

## 🧠 Lessons from This Round

- **Stay in the same ecosystem when you can.** WinForms felt like a compromise at first, but it's been faster and more reliable than bridging to Python would have been.
- **CI on the right OS matters.** Running PowerShell tests on Linux with stubs was fragile. Running them on Windows is just correct.
- **Small UX details matter.** A checkbox label with garbled encoding or wrong text erodes trust in a privacy tool more than almost anything else.

---

## 🚀 What's Next

The core feature set is in a solid place. From here I'm looking at:

- **Preset profiles** — a "Safe" mode for cautious users, an "Aggressive" mode for those who want everything locked down
- **Better restore/rollback UI** — surfacing the audit log more prominently
- **Testing coverage** — more Pester tests for the registry-touching functions

If you're on Windows 11 and want to clean up your privacy settings, give it a try:  
👉 [https://github.com/willj4945/windows_privacy](https://github.com/willj4945/windows_privacy)

As always — thanks for reading.  
Stay curious ✨  
— Will
