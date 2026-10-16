# mqtt-cli → Go migration

This fork ports the HiveMQ MQTT CLI from Java to Go without changing the executable's behaviour.
Java and Go live side by side until cut-over. Overview and rules: issue #3.

## GitHub is the source of truth
- Plan = milestones, work = issues, decisions = `decision` issues (a closed one is the record).
- **Always pass `--repo mutualtape/mqtt-cli` to `gh`.** The `upstream` remote is the public
  `hivemq/mqtt-cli`. Never push, open issues or open PRs there.
- Before starting work, read the issue, what blocks it, and the decisions that touch it:
  `gh issue list --repo mutualtape/mqtt-cli --label decision --state all`

## Rules
- Reference behaviour: the Java CLI at `v4.56.0` (#1).
- Pin down before porting: a command is ported only once system tests pin its behaviour and
  pass on the Java binary.
- Help text must be content-identical; layout may differ.
- Any behaviour change, including bug fixes, needs a `divergence` issue approved by Thomas.
- Choices with tradeoffs, hard to reverse, or visible to users → open a `decision` issue
  (template) with options and a recommendation, and don't build on it until Thomas decides.
  Once he has, close it with a comment starting `Decision:` that sums up what was decided.
  Choices that are conventional, local and reversible: decide yourself and note them in the PR.
- Found something out of scope? Open an issue; don't widen the current one.

## Session routine
1. One issue per session. Branch `<issue>-<slug>`, one PR against the fork's `master` with `Closes #N`.
2. Done = tests green (on Java for characterization, on Java and Go for ports), reviewed by a
   Claude code-review agent and by Codex, PR open. Never merge; Thomas reviews and merges.
   - Codex only reviews, it never writes code:
     `codex exec -s read-only "Review only, don't modify files. Run git diff master...HEAD. <goal of the PR, what to check>"`
     (`codex review --base` can't take a prompt.)
   - Address or rebut every finding; list both reviews' findings and outcomes in the PR.
3. End with a handoff comment on the issue: done, open, surprises.

## Build & test
- Java: `./gradlew build` (includes `systemTestNative`, which needs `./gradlew installNativeImageTooling`
  first). System tests: `./gradlew systemTest` (shadow jar), `./gradlew systemTestNative`, and
  `./gradlew systemTestDocker` (`DockerImageST` only; builds the CLI image). The harness gets the
  executable from the `cliExec` system property (`src/systemTest/java/com/hivemq/cli/utils/cli/MqttCli.java`).
- Go: to be added in M0.
