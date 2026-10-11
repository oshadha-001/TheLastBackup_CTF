THE LAST BACKUP — DOCKER / CTFd v4

Six linked challenges: OSINT, steganography, web security, cryptography,
digital forensics and Linux investigation. All four team upload parts are
required. The source includes intentional flags and solutions; keep the
repository private during the exercise.

WINDOWS SETUP
Install Python 3.12, Docker Desktop with Linux containers, and the Windows
OpenSSH client. Open Docker Desktop and PowerShell in the repository root.
Run each command only after the preceding one succeeds:

py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements-dev.txt
.\.venv\Scripts\python.exe build.py
.\.venv\Scripts\python.exe local/prepare.py
powershell -NoProfile -ExecutionPolicy Bypass -File local/start.ps1

Evidence is rebuilt locally, including the 64 MiB forensic image. Preparation
generates fresh secrets and SSH keys in local/private and local/.env. Never
upload those files. Repeating preparation preserves existing credentials.
All local copies use the Docker project name last-backup-full and the same
ports. Do not run another checkout against an existing event with newly
generated credentials. Back up the existing event before switching copies.

RUN AND ACCESS
After initial setup, double-click Start Full Project.cmd.
CTFd: http://localhost:8001
Admin: http://localhost:8001/admin
Replay: http://localhost:8001/practice
Profile: http://localhost:8001/user
Archive: http://localhost:8081
Portal: http://localhost:8083
Verifier: http://localhost:8443
SSH: analyst@127.0.0.1, port 2222

Organizer login: local/private/organizer-login.txt
Register ordinary player accounts for normal stage progression.
After Stage 4, release build/handouts/northstar_evidence.img.
After Stage 5, issue a verifier token and authorized SSH access:

docker compose --env-file local/.env -f compose.local.yml exec -T verifier python manage.py issue-team team-01 --database /state/verifier.sqlite
ssh -i local/private/analyst_key -p 2222 analyst@127.0.0.1

Share the resulting token only with that team. Player evidence access does
not grant access to organizer source, host files or Docker administration.

PLAYER RESET / REPLAY
Solve a stage normally, then choose Replay. Reset & replay this stage starts
a fresh personal practice attempt. Reset all my practice stages resets all
unlocked practice stages. Confirm reset applies the action; Cancel does not.
The profile keeps correct flags, wrong flags, stage resets and whole-practice
resets. A bulk reset also counts one reset for every affected stage.
Practice cannot earn extra competition points. Scores, hints, historical
counters and guessing limits survive a practice reset. Practice does not
wipe shared Linux scratch, portal cookies or verifier-team completion.

GUESSING LIMITS
CTFd competition and practice share 5 requests/minute and 30/hour per
account, with additional shared IP limits. Reset actions have separate
limits and cannot clear flag cooldowns. The portal and verifier also limit
answer attempts. These controls mitigate online guessing; they do not
prevent reuse of already-known flags or offline analysis of downloaded files.

ORGANIZER RESET / RECOVERY
Use Reset Box.cmd with an option, or invoke local/reset.ps1 in PowerShell:
-Status: inspect services without resetting them.
-Backup: save database, uploads and verifier state in backups/<time>.
-Linux: restart shared Linux evidence and wipe scratch; SSH host keys stay.
-Portal: invalidate all review sessions by rotating the portal secret.
-Verifier: clear verifier completions and attempts; issued tokens stay.
  Add -Team team-01 to target one team.
-Scores: clear competition progress, hints purchased and awards; keep accounts
  and the v4 lifetime replay ledger.
-Players: clear scores and non-admin accounts, including their history.
-All: run Linux, portal, verifier and competition-score resets together.
-FullWipe: delete all project volumes and rebuild; all player history is lost.
-DryRun: preview score, player or verifier deletion without deleting progress.

Destructive score/player/full-wipe resets take a backup first and ask for
confirmation. Do not use them during active play. A player replay does not
need an organizer wipe. -Yes skips confirmation and -NoBackup skips backup;
use those only when you deliberately accept the consequences.

STOP WITHOUT DELETING DATA
docker compose --env-file local/.env -f compose.local.yml stop

TEST
.\.venv\Scripts\python.exe -m pytest tests -q
Selected test outputs are in test-results/. The replay HTTP test is for the
separate compose.replay-test.yml environment only; it creates test players
and changes test rate-limit buckets. Do not run it against a real event.

DEPLOYMENT SCOPE
This variant runs on localhost Docker containers, rather than four VMs.
Only loopback web/SSH ports are published; database/cache ports stay private.
Outbound isolation and independent difficulty/shortcut checks still need
validation on the final demonstration machine. Complete separate-host
backup restoration is not claimed as verified. The setup/run/reset guide
is included as plain text for the assignment deliverable.

TEAM UPLOAD ALLOCATION
Minura: platform, Docker, CTFd UI and replay/reset integration.
Pathum: Stages 1-3 and portal protection.
Oshada: Stages 4-6 and final verifier.
Mudith: integration, testing and evidence generation.
This allocation identifies upload responsibility, not original authorship.
See ownership-manifest.csv for file allocation and THIRD-PARTY-NOTICES.txt
plus licenses/ for the bundled third-party software notices.
