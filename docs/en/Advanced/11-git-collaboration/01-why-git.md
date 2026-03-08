---
title: "11.1 Why You Need Git"
description: "Understanding what problem Git solves, starting from the 'final version' dilemma"
---

# 11.1 Why You Need Git

> **Goal of this section**: Understand Git's core value—what problem it solves, and why you might already be using it.

---

## The "Final Version" Dilemma

You've definitely experienced this scenario.

When writing a thesis, your desktop ends up with files like these:

```
thesis.docx
thesis-revised.docx
thesis-final.docx
thesis-final2.docx
thesis-final-final.docx
thesis-advisor-wants-changes.docx
```

Making presentations, editing designs, writing proposals—any work involving "repeated revisions" leads you to instinctively use filenames to mark versions. This method works, but the problems are obvious: Which one is the latest? Filenames lie. Want to go back to a version from three days ago? It might already be overwritten. What exactly changed between two versions? You have to compare them manually. Multiple people editing the same file? Complete chaos.

You might think "I have a good memory, I know which is the latest." But this confidence usually lasts about three days. What you changed three days ago, why you changed it, what it looked like before—these details have a very short shelf life in your brain. Filenames can carry extremely limited information. What exactly does "final2" have that "final" doesn't? You have to open both files and compare line by line to find out.

And when you really need to return to a historical version—like when your advisor says "the version from last week was better"—you'll discover that last week's version might already be overwritten, or you're not even sure which file corresponds to last week's state.

These problems are tolerable when writing a thesis—after all, there are only a few files, maybe dozens of pages at most. But writing code is completely different.

A web project might have hundreds of files, and changing one feature might involve coordinated modifications across a dozen files. Adding a search box to the homepage might require changes to page components, API endpoints, route configurations, and style files. Managing versions with filenames? You'd have to copy the entire project folder. Comparing differences manually? With hundreds of files, how long would that take?

What's worse, code files have dependencies—file A calls a function from file B, and file B reads configuration from file C. You can't roll back just one file; you have to revert all related files to the same point in time, or the program won't run.

Xiaoming once had folders named `project-backup`, `project-v2`, and `project-works` in his project directory. He spent a day coding, realized he was going in the wrong direction, and wanted to return to yesterday's state—but yesterday's code had already been overwritten by today's.

Even worse, he wasn't sure what "yesterday's state" actually looked like—because he hadn't recorded each step of his changes, only remembering "it worked yesterday, it doesn't work today." He tried to reconstruct yesterday's code from memory, spent two hours on it, and introduced new bugs. If he had had a tool that could record each state like a game save, he could simply "load game" to return to yesterday—instead of manually reconstructing from memory.

This is the problem Git solves: **a reliable way to record every modification, allowing you to return to any historical version at any time.**

No marking versions with filenames, no manual copying for backups, no manual comparison of differences. Git remembers all of this for you. It doesn't record "a certain version of a certain file," but rather "a complete snapshot of the entire project at a certain point in time"—the state of all files is saved together and rolled back together, preventing mismatches where "file A went back to yesterday but file B is still from today."

---

## You Might Already Be Using Git

Here's an interesting fact: if you've been following this book and developing projects with Claude Code, your project directory likely already has a `.git` folder.

When Claude Code creates a new project, it typically runs `git init` automatically to initialize a repository, and helps you commit code during development. In other words, you've been using Git all along—you just didn't realize it.

Open your terminal, navigate to your project directory, and run `git log --oneline -5`. If you see a series of commit records, that's it—Claude Code has been "saving your game" for you. This discovery itself is an awakening moment: **the tool you've been using, now it's time to understand it.**

This is like using your phone's automatic backup feature—photos sync to the cloud by default, but you never opened the cloud album to look. One day your phone breaks, and you discover—all your photos are in the cloud, not a single one lost. Git does something similar for your code, except it records not just the "latest version," but the complete history of every modification.

If your project doesn't have a Git repository yet, no problem—just tell Claude Code to initialize one for you. Windows users note: `.git` is a hidden folder. If you can't see it in File Explorer, simply run `git status`—if there's output, the repository exists.

---

## Commit: Save Your Code

Git's core operation is the **commit**. Each commit is like a manual save in a game—it records the current state of all files, accompanied by a description and timestamp. Save is commit, load is rollback, and the save list is `git log`.

Unlike game saves, Git's saves are **incremental**—it only records the changed parts, not the entire project. A project might have hundreds of commits, but the `.git` folder is typically only a few dozen MB, far smaller than copying hundreds of project folders. So you can commit frequently without worrying about taking up too much space.

This is also where Git outshines "copying folders"—it achieves complete historical records at minimal storage cost.

Xiaoming had Claude Code help him commit code. He doesn't need to remember the syntax for `git add` and `git commit`—he just needs to tell the AI "commit this" after completing a feature or fixing a bug. Claude Code automatically stages the changes, generates a commit message, and completes the commit. The entire process is just one sentence for Xiaoming, but behind the scenes Git saves a complete project snapshot for him, which he can return to anytime.

When should you commit? There's no hard rule, but there's a simple guideline: **whenever you complete something "that can be described in one sentence," it's worth committing.**

For example: "added rating component," "fixed empty search bug," "adjusted homepage layout." Don't accumulate a day's worth of changes and commit them all at once—that makes rollback too coarse-grained, like only saving at "level 1" and "final level," losing everything in between. Imagine playing a difficult game level without saving in the middle—if you die, you start over. You'd curse yourself for not saving more often. Code works the same way.

---

## The Three-Zone Model: Where Your Changes Are

Git manages code in three areas. Understanding them helps you answer a key question: **"Where are my changes right now?"**

<GitFlowDiagram />

An analogy: the **working directory** is your desk—files spread out, you're actively editing. The **staging area** is a neatly organized stack of papers—"these are what I'm submitting." The **local repository** is the filing cabinet—submitted items are permanently stored. You scribble on your desk (working directory), when satisfied you organize the papers into a stack beside you (staging area), and finally file that stack in the cabinet (local repository). These three steps correspond to "edit files → git add → git commit."

In daily development, Claude Code handles these steps for you—you don't need to manually run `git add` and `git commit`. But when you encounter confusion like "where did my changes go" or "why isn't this file in the commit," the three-zone model helps you locate the problem.

Files are modified but `git status` shows "not staged"—changes are still in the working directory, haven't been `add`ed. `add`ed but not `commit`ted—changes are in the staging area, not yet saved. `commit`ted but not `push`ed—save is local, not yet synced to the cloud—more on this in the next section.

You don't need to memorize these commands. What's important is building a mental model: changes start from the working directory, pass through the staging area, and finally enter the local repository. Each step is controllable—you can always ask Claude Code "what stage are my changes at."

Like glancing at your phone's battery before leaving the house—you don't always need to charge, but checking gives you peace of mind. `git status` is that glance: it tells you which files are modified but not staged, which are staged but not committed, and which branch you're on. Claude Code usually checks status automatically before Git operations, but when you're confused, knowing this tool exists helps you locate problems.

<ThreeZoneModel />

---

## Rollback: Unlimited Ctrl+Z

When you use Ctrl+Z to undo, there's an implicit limitation: **close the file and reopen it, and Ctrl+Z stops working.** It can only undo operations in the current editing session. You edit a file in VS Code, save it, close it, open it the next day—Ctrl+Z won't take you back. Let alone returning to a state from three days ago or a week ago. Git doesn't have this limitation. As long as you've committed, you can return to that point in time—even a version from a month ago. It's a Ctrl+Z that never expires and has unlimited layers.

Three common rollback scenarios, with three different approaches.

**Edited for a while, realized the direction was wrong, haven't committed yet.** This is the simplest case. Tell Claude Code which files' changes you want to undo, and it will restore them to the last committed state. Working directory changes are discarded; staging area and local repository are unaffected. Like scribbling on your desk for a while, then crumpling the draft and throwing it away—the filing cabinet's archives remain untouched. This operation carries no risk because you're only discarding unsaved drafts.

**Already committed, want to go back to the previous version.** Tell Claude Code "go back to the last working version." It will use `git reset` to move the local repository pointer back. This rewrites commit history—that "wrong commit" disappears from history. If the code hasn't been pushed to remote, this is the cleanest approach—like pulling the latest archive from the filing cabinet and tearing it up, pretending it never existed. But note: the torn-up archive is really gone—if you later want that commit's content, you can't get it back. So before resetting, confirm you really don't need that code.

**Already pushed to remote.** At this point, you can't simply "delete" that commit, because teammates may have already pulled your code. If you delete it, their history won't match yours, causing confusion. The correct approach is `git revert`—it doesn't delete history, but creates a new commit to "reverse" the changes, undoing that modification's effects. The history clearly shows "commit 10 did X, commit 11 undid X." This is like making a wrong entry in a ledger—not using correction fluid to erase it, but writing a "reversal" on the next line. The original record remains, but the final result is correct.

Xiaoming encountered this situation. He spent an afternoon adding a complex sorting algorithm to the recommendation feature, committed and pushed it. The next day he discovered fundamental flaws in the sorting logic—the entire direction was wrong. He told Claude Code "go back to before the sorting algorithm was added." Claude Code used `revert` to create a new commit, cleanly undoing that modification without affecting teammates when they pulled.

The core insight of Git rollback is: **realizing you can go back.** Many people's first reaction when code breaks is to keep trying to fix it, making it worse and worse. If you know Git can take you back to any historical version at any time, you dare to experiment boldly—if it breaks, roll back, no big deal.

---

## Viewing History: What Did the AI Actually Change?

When developing with Claude Code, the AI frequently modifies files. Sometimes you want to know "what exactly did it just change" or "what did yesterday's code look like." Git's commit history is your answer. Each commit record contains who made changes when, which files were modified, and specifically which lines in each file were changed.

You don't need to remember the differences between `git log`, `git diff`, and `git show`. Tell Claude Code what you want to see—"what changed recently," "what was in the last commit," "this file's modification history"—and it will choose the appropriate command and explain the results to you.

This is especially useful in AI programming. Sometimes Claude Code changes several files at once, and you want to verify if the changes are correct—glancing at a diff is much faster than re-reading entire files. You've probably used "Track Changes" in Word—deleted text in red strikethrough, new text in blue. Diff is the code world's track changes, using green for added lines and red for deleted lines, clear at a glance. Developing the habit of "check the diff after changes" helps you better understand what the AI did, and catch unexpected modifications early.

Viewing history has another less obvious but important use: **understanding how code evolved.** When you see a piece of code and don't understand why it's written that way, checking that file's commit history often reveals the answer. Maybe three commits ago the code was simpler, and it became this way to fix a bug. The commit message might say "fix: handle null pointer exception when user not logged in"—you immediately understand what this seemingly redundant check is guarding against. The code itself only tells you "what," the commit history tells you "why."

---

## Commit Messages: Leaving Clues for Your Future Self

When naming folders, you write "2024-tax-invoices" instead of "New Folder (3)." Commit messages follow the same principle—they're for your future self (and your collaborators) to read.

Good commit messages follow a simple format: type prefix plus brief description. `feat: add movie rating feature` indicates a new feature, `fix: fix empty search results issue` indicates a bug fix, `docs: update API documentation` indicates documentation changes, `refactor: refactor user authentication module` indicates refactoring. Other common ones include `style` (style adjustments) and `chore` (miscellaneous tasks, like upgrading dependencies).

This isn't a rigid rule, but a convention to make commit history **scannable.** When reviewing dozens of commit records, prefixes help you quickly locate "where was that commit that added the rating feature." Commit history without prefixes is like a pile of folders without labels—you have to open each one to see what's inside. Imagine your email inbox: if every subject was "update" or "modification," how long would it take to find a specific email? But if subjects read "[Reimbursement] March travel expenses" or "[Meeting] Friday product review," you can find what you need at a glance.

Claude Code typically generates standard commit messages automatically when helping you commit. It analyzes which files you changed and what you changed, then generates an accurate description. Most of the time, its generated messages are good enough. If you feel the description isn't accurate enough—like it wrote "update components" but you think it should be more specific—you can tell it to adjust. Good commit messages don't need to be long; one sentence clearly stating "what was done" is sufficient.

---

::: tip Key Takeaways from This Section
Git's core problem is **version management**—no longer marking versions with filenames, but using commits to record every meaningful modification. You can view history, compare differences, and return to any version at any time. If your project already has a `.git` directory, you're already using it—now you understand what it's doing.
:::

---

**Next section**: Code is only on your computer, teammates can't access it, and if your hard drive fails it's all gone. [11.2 Push to the Cloud, Start Collaborating](./02-remote-and-collaboration.md) solves this problem.