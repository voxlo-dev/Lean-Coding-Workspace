---
name: dispatch-configurator
description: "Use to create, repair or check ~/.agents/DISPATCH-GUIDE.md — the machine-local record of which harness, provider and model an out-of-process dispatch runs on: on a new machine, after a harness, provider or model was added or changed, or when a dispatch hit a dead connector. `check` re-probes and reports drift without writing. Invokable via /dispatch-configurator."
disable-model-invocation: true
---

# Dispatch configurator

Writes one file, `~/.agents/DISPATCH-GUIDE.md`, from `templates/DISPATCH-GUIDE.md`, describing this machine only. **Every row comes from a command that answered here** — never from the template's examples, memory, docs or another machine's guide. Installs, configures and logs into nothing: a missing piece is reported and its row stays out.

Modes: **configure** (default; a fresh run and a repair are the same run) · **check** — steps 1–4 against the existing guide, drift reported, nothing written.

## 1. Frame

- Existing guide: read it. Its header names the host; another host means it describes a different machine — configure from scratch and say so. One guide per install.
- Ask once which **environments** may run a dispatch: the native shell always; a Linux subsystem, container or remote host only where the user names one. Probe each through the exact non-interactive command a dispatch will use (`{environment launcher} {shell} -c "…"`), never an interactive shell: that is where the binary has to resolve.

## 2. Harnesses

Candidates: every harness home the workspace is installed into, any agent CLI the user names, and wrappers around either. Per candidate and environment:

- **Resolve the binary** in that non-interactive shell. Found interactively but not there → it hangs off shell init (a version manager): put the explicit init into the launch prefix rather than reporting it missing. A shim name that differs by shell (`.cmd` on Windows) — record the one that executes.
- **A wrapper is its own harness** — a launcher that pins a version or points the binary at another config directory resolves other providers, models and agents. One entry each, stating that they share no config.
- **Read `--help`**, and the one-shot subcommand's: headless mode · agent selector · model and provider selectors · working-dir flag (else `cd` in the launch) · non-interactive permission flag · option terminator (`--`, mandatory once a flag precedes the prompt). Compose Launch only from flags seen there. `--version` → "verified on v{x}" in Notes.
- **Agents:** the harness's own list/debug lever, else its agent directory; a name is dispatchable only if primary-capable. No registry → "none — one session takes the whole brief". A mode flag that changes what the session believes it is (a built-in workflow or orchestrator mode) goes into Agents as a warning; Launch stays plain.

## 3. Providers and models

- Per harness: its own provider/auth listing and model listing — the harness resolves keys, not the endpoint. A key without credit stays, marked so.
- **Local endpoint:** probe it. Down → find its start command in the server's own CLI help, start it detached, and **confirm it survives the calling process** from a second call. Record **Start** (the command, or "by {wrapper}") and whether a model loads on request or must be preloaded. Two servers sharing one GPU → note it on both. Stop anything you started unless the user wants it running.
- A model is addressable in a harness only once declared in **that harness's** config, with a context limit equal to the server-side load — check both ends. Under 64k → not listed.
- Ask which models the user wants listed; a listing can run to hundreds. Unprobed → "untested", never described.

## 4. Probe dispatch

For each harness × provider route that is new, changed, or failed its liveness check — all of them on request. In a scratch git repo outside any project: fill `templates/probe-brief.md` into `.temp/dispatch/probe/brief.md` with fresh random tokens, create its fixtures, run exactly the composed Launch with stdout and stderr captured apart. **Pass needs every check, made yourself — never read off the verdict:**

- exit 0, and stdout's last line is the verdict;
- the side effect exists: the file with the right token, committed — `DONE` over a skipped constraint is a fail, and goes into that model's note;
- the transcript names the intended agent and model; anything else is a silent substitution;
- where the harness enforces path denies: the denied token appears nowhere in stdout, files or transcript. A catch-all allow after the deny globs voids them under last-match-wins and still looks green.

Record the transcript's location as the live-view lever. On a fail, find the layer — connector, harness config, pointer wording, model — fix what is config, retry once. Models read their role from the pointer's words and paths: a wording that fixed a run becomes Launch's wording. Delete the scratch repo.

## 5. Workflow defaults — interview

Roles: every installed skill that reads the guide (`grep -l DISPATCH-GUIDE ~/.agents/skills/*/SKILL.md`), using its section in the existing guide, else the template's. Per role propose harness, model and fallback from the passing routes; the user decides. A workflow handed over whole gets one `whole run` row. A role left out runs inline.

## 6. Write

Fill the template per its CONTRACT, host in the header line. Show the diff against the existing guide and write on approval. Secrets never — name the config path that holds them; the guide is read into every dispatching run.
