# Saber

**A Windows IDE built around the coding agent you already pay for.** Saber runs Claude Code or
Codex on your own subscription, puts every change the agent makes behind a checkpoint you can
undo, reviews the result without sending the whole repository, and shows what each turn read,
changed and spent.

## What you can do

**Discuss** — ask about the open repository in a side panel. The model can read and search the
code, and cannot change it.

**Agent** — hand over a goal.

- **Ask, Plan or Act.** Ask and Plan are read-only. A plan comes back as structured fields —
  expected files, risks, the checks that would prove it — with the dependency graph's blast
  radius alongside, and approving it stages the Act turn.
- **You set the permissions.** *Manual* asks before every edit and command. *Accept edits* lets
  file edits through and asks before commands. *Auto* applies edits and runs only the commands
  you have already approved for that workspace. In every mode, a short fixed list of read-only
  commands such as `git status` runs without asking, unless you switch that off.
- **Every writing turn can be undone.** Saber snapshots the repository first; keep or restore
  each file the agent touched, or undo the whole run.
- **Commands are checked before they run.** Hidden characters and shell tricks are refused, and in
  the modes that ask, a chain is approved one command at a time. An optional, experimental
  Windows sandbox runs approved commands with access to the repository and nothing else — no
  other files, no network.
- **Steer while it works.** A follow-up reaches the running turn at its next safe point.
- **Run the tests that matter.** After a run, Saber offers to run the tests the change reaches.

**Review** — check a change before it lands.

- Reviews your diff by default, and maps the callers, dependents and tests the change reaches
  with a dependency graph for C#, TypeScript/JavaScript, Python and Go. Switch to whole files and
  the review reads the changed files plus exactly that code, instead of the whole repository.
- Findings land in the editor next to the code they are about, and **Fix with Agent** carries a
  finding into the agent's composer.
- The token explorer shows what was sent, why it was sent, and what leaving it out would have
  saved.

**See what you spend.** Every review and agent run carries its token counts, and an agent run
splits them by model call into fresh input, cache and output where the engine reports them.
Anything Saber estimates is labelled as an estimate, and a figure an engine does not report is
shown as missing rather than guessed.

**A real editor around it.** Monaco, language servers for C#, TypeScript, Python and Rust, Git with
diffs and history, an integrated terminal, project-wide search, and C#/.NET debugging.

## Requirements

- Windows 10 or 11, 64-bit.
- At least one coding agent, installed and signed in:
  - [Claude Code](https://code.claude.com/docs), with a Claude plan that includes it, or
  - [Codex CLI](https://github.com/openai/codex), with a ChatGPT plan that includes it.
- [Git](https://git-scm.com/downloads). Reviews, snapshots and Undo work on Git repositories.
- Optional: the language servers you want (`csharp-ls`, `typescript-language-server`, `pyright`,
  `rust-analyzer`), and [NetCoreDbg](https://github.com/Samsung/netcoredbg) for C# debugging.

## Install

1. Download `Saber-Setup-<version>.exe` from the
   [latest release](https://github.com/sixnationn/saber-releases/releases/latest).
2. Run it. The installer is not code-signed yet, so Windows SmartScreen may ask you to confirm
   (**More info → Run anyway**).
3. Saber checks for new versions when it starts. The one exception is 0.9.2, which looks for
   updates in the wrong place: if that is what you have, install the latest release over it once.

## First run

1. On the welcome screen, choose **Set up Claude Code or Codex**. Saber finds the installed tools
   and checks that you are signed in, without sending a paid request.
2. Pick a model. Until you do, reviews and agent turns return placeholder results instead of
   calling a model.
3. Open a folder that is a Git repository.
4. Make a change and press **Review changes**, or open the Agent and describe what you want done.

## Measured, not promised

Saber's figures say where they came from, and so do these. The review and log figures were
measured on Saber's own repository; the agent figure on a suite of 54 small repair tasks.

| | Result |
|---|---|
| **Review context** — a whole-file review sends the changed files and the code they reach | A median **6.3x** fewer input tokens than sending the whole codebase, across 88 reviews (2026-09-05; 7.3x on Claude, 5.1x on Codex). A diff review, the default, sends only the diff |
| **Agent tools** — Saber's own tools and prompt, against Claude Code's built-in tools | **60–64%** less fresh input and **14–22%** faster at the median, with every Saber task passing an independent check, over two runs of a 54-task suite (2026-09-06 and 2026-09-12) |
| **Build, test and Git output** — compressed before a review sends it | **27x** smaller on one capture of `git log`, `dotnet build` and `dotnet test` output (2026-08-20) |

They come from one codebase and small suites, so read them as evidence rather than a guarantee.
Your own reviews and runs show their real figures in the app.

## Your accounts and your repositories

- Saber drives the official `claude` and `codex` tools on your machine, signed in through their
  own login. It never asks for your Claude credentials and never stores them.
- Everything counts against your own plan's usage, as if you had run the tool yourself.
- An optional, experimental Codex mode calls ChatGPT directly using the Codex tool's existing
  sign-in. It reads that access token in memory and never copies or saves it. It stays off
  unless you turn it on.
- A repository cannot grant the agent permissions by shipping a settings file: those settings are
  ignored until you trust that workspace.

## Status

Saber is pre-1.0 and ships for Windows only. A capability is switched on for an engine only after
it has been exercised on that engine, so Claude and Codex do not match everywhere — per-call token
breakdowns and isolated worktree tasks are Claude-only today. The optional command sandbox cannot
run `npm`, `npx` or Git's Unix tools.

Found a problem? [Open an issue](https://github.com/sixnationn/saber-releases/issues).

**Just want the dependency graph?** It ships on its own as
[`code-review-graph`](https://github.com/sixnationn/code-review-graph-releases/releases/latest),
a standalone MCP server for Claude Code, Cursor or Zed — one self-contained binary for Windows,
Linux or macOS.

## About this repository

This repository holds Saber's installers and the `latest.yml` file installed copies read to find
updates. Saber's source lives in a private repository, which is why releases are published here.

Each release carries `Saber-Setup-<version>.exe` (the installer), `latest.yml` (the version, size
and SHA-512 the updater checks) and a `.blockmap` that lets an update download only what changed.
