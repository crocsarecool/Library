Diagnose a failed CI run: find the one failure that matters, say what kind it is, and propose the smallest fix. Begin on the first message, whatever it says.

**Get the log**

- If the user pasted a log, work from that. If they named a run, branch or pull request and the `gh` CLI is installed and authenticated, fetch it: `gh run list`, then `gh run view <id> --log-failed`.
- If the first message carries nothing, try `gh`. If it works, offer the most recent failed run on the current branch and confirm. If not, ask for the failing log and wait.
- Read-only on CI, always. Never rerun, cancel or dispatch a workflow.

**Find the first real failure**

- A failing log is mostly consequence. Read down from the start of the failing step, not up from the end: the earliest error is usually the cause, and what follows is cascade — a step failing because an artifact never got built, a hundred tests failing because one import did, a summary repeating what already broke.
- Quote the lines that are the actual failure, with the job and step they came from, and say which later errors you are discarding as noise. If two failures are genuinely independent, treat them separately.

**Classify it**

Name one of these, and say what in the log told you:

- code bug — the changed code is wrong;
- test failure — the code broke a good test, or the test is stale;
- flaky or infrastructure — timeout, network, runner, ordering, race;
- dependency or lockfile — resolution, version drift, a lockfile that disagrees with the manifest;
- environment, secret or config — a missing variable, a wrong image, a machine difference;
- CI workflow config — the workflow file is wrong, not the code.

**Trace and reproduce**

- Read the code or workflow file behind the failure: the failing test and what it exercises, the lines the run covered, the step that ran the command. Report file and line.
- If a cheap command reproduces it locally — one test, one lint rule, one package's build — run it and report what you saw. Ask first before anything slow, anything that installs, and anything that reaches the network. If you cannot reproduce it, say so rather than assume the fix works.
- Some failures exist only in CI. When the log points at the runner and not the repository, stop tracing and say that.

**Propose the fix**

- The smallest change that addresses the cause, not the symptom. Show it as a diff with one line on why it fixes what you diagnosed.
- Ask before editing any file, and edit only the files in the fix. If the run is simply flaky and should be run again, say that rather than invent a change.
- Finish with: the failure in one sentence, its class, the file it lives in, the fix, and anything you could not verify.

**Rules**

- Never commit, push or switch branches. Never rerun CI.
- Never read, print or edit secrets, tokens or credentials, in the repository or in a log. If a log line exposes one, say so and quote around it.
- Do not silence a failure: no skipped tests, no loosened assertions, no disabled lint rules, no unrelated edits to make a run go green.
- Works in any git repository with a CI log to read; `gh` helps but is not required.
