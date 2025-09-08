# Runbook

## Ops Simulation Runbook

### Failure Scenario 1: Breaking Code Change in Web App

#### Symptoms

- Jenkins/TeamCity build fails (red pipeline).
- Unit tests fail or build stops with syntax/logic error.
<br><br>

#### Diagnosis
- Review build logs → error points to invalid code.
- Example:
    <ul>
    <li style="list-style-type: circle;">SyntaxError: missing `)` after argument list</li>
    <li style="list-style-type: circle;">Or CSS error like invalid hex color.</li>
    </ul>

#### Resolution Steps
1.	Fix the code locally (close bracket, correct hex).
2.	Commit & push fix.
3.	Re-run pipeline.
<br><br>

#### Verification
- Build turns green.
- Spinnaker (once healthy) receives artifact → deploys successfully.

---

### Failure Scenario 2: Breaking Config Change (TeamCity/Spinnaker)

#### Symptoms

- Pipeline fails at build or deploy step.
- Logs show “command not found” or “image not available.”
<br><br>

#### Diagnosis

- Check job configuration in TeamCity.
- Check Spinnaker pipeline manifest for bad image reference.
<br><br>

#### Resolution Steps

1.	Revert or fix the config (use valid command or correct image tag).
2.	Save configuration.
3.	Re-trigger pipeline.
<br><br>

#### Verification
- Build and deploy complete successfully.
- Application is running with correct image/config.

---

### Failure Scenario 3: TeamCity Build Agent Unavailable

#### Symptoms

- Pipeline stuck in queued state.
- No agent picks up the job.
<br><br>

#### Diagnosis

- Check TeamCity UI → build agent offline/unavailable.
- Logs show “No compatible agents found.”
<br><br>

#### Resolution Steps

1.	Start/restart the offline build agent.
2.	Assign job to another available agent.
3.	Resume build.
<br><br>

#### Verification

- Build moves out of queued state → runs successfully.

---

### Failure Scenario 4: Version Control Merge Conflict

#### Symptoms

- Git merge fails.
- Error message.
- CONFLICT (content): Merge conflict in app.js
- Automatic merge failed; fix conflicts and commit the result.
<br><br>

#### Diagnosis

- Run git status → shows conflicted files.
- Open file → conflict markers <<<<<<< HEAD, =======, >>>>>>> branch.
<br><br>

#### Resolution Steps

1.	Manually edit file to keep correct lines.
2.	Run git add `<file>` and git commit.
3.	Push merged branch to remote.
4.	Resume pipeline.
<br><br>

#### Verification

- Git shows clean working tree.
- Jenkins/TeamCity build passes with merged code.
- Spinnaker deploys merged build.
