# Scheduling Tasks

> Part of [[Operations & Deployment]] | [[00 - LFCS MOC]]

---

## Three Tools

| Tool | Use Case |
|---|---|
| `cron` | Repetitive jobs — runs at specific times/intervals (even multiple times per day) |
| `anacron` | Repetitive jobs — minimum interval is 1 day; catches missed jobs if system was off |
| `at` | One-time jobs — run a command once at a specific future time |

---

## cron

### Cron Syntax

```
MIN  HOUR  DOM  MON  DOW  COMMAND
 *    *     *    *    *   /usr/bin/command
```

| Field | Range | Notes |
|---|---|---|
| Minute | 0–59 | |
| Hour | 0–23 | |
| Day of Month | 1–31 | |
| Month | 1–12 | |
| Day of Week | 0–7 | 0 and 7 = Sunday |

**Special characters:**

| Symbol | Meaning | Example |
|---|---|---|
| `*` | Every value | `*` in hour = every hour |
| `,` | List of values | `15,45` in minute = at :15 and :45 |
| `-` | Range | `2-4` in hour = 2AM, 3AM, 4AM |
| `/` | Step | `*/4` in hour = every 4 hours |

### Managing Crontabs

```bash
crontab -e                     # edit current user's crontab
crontab -l                     # list current user's crontab
crontab -r                     # remove current user's crontab

sudo crontab -e                # edit root's crontab
sudo crontab -l -u jane        # view jane's crontab
sudo crontab -e -u jane        # edit jane's crontab
sudo crontab -r -u jane        # remove jane's crontab
```

> Always use the **full path** to commands in cron jobs (`which touch` → `/usr/bin/touch`).

### Example Cron Entries

```bash
# Every day at 6:35 AM
35 6 * * * /usr/bin/touch test_passed

# Every Sunday at 3 AM
0 3 * * 0 /usr/bin/touch test_passed

# 15th of every month at 3 AM
0 3 15 * * /usr/bin/touch test_passed

# Every day at 3 AM
0 3 * * * /usr/bin/touch test_passed

# Every hour on the hour
0 * * * * /usr/bin/touch test_passed

# Every 4 hours
0 */4 * * * /usr/bin/touch test_passed
```

> Practice cron expressions at **crontab.guru**

### cron.d Directories (Alternative)

Drop scripts into these directories — no crontab editing needed:

```bash
/etc/cron.hourly/
/etc/cron.daily/
/etc/cron.weekly/
/etc/cron.monthly/
```

```bash
sudo cp myscript /etc/cron.hourly/myscript    # no .sh extension required
sudo chmod +rx /etc/cron.hourly/myscript
sudo rm /etc/cron.hourly/myscript             # remove job
```

> Scripts placed in these directories must **not** have a `.sh` extension.

---

## anacron

anacron is best for jobs that should run daily/weekly/monthly on machines that aren't always on. It checks if a job was missed and runs it when the system comes back up.

### anacron Syntax

Edit `/etc/anacrontab`:

```bash
sudo vim /etc/anacrontab
```

```
PERIOD_DAYS   DELAY_MIN   JOB_ID   COMMAND
     3            10      test_job  /usr/bin/touch /root/anacron_created_this
```

| Field | Description |
|---|---|
| Period | How often in days (or `@monthly`, `@weekly`) |
| Delay | Minutes to wait before running (staggers multiple jobs) |
| Job ID | Label for logs |
| Command | Full path to command or script |

```bash
# Examples
3        10   test_job   /usr/bin/touch /root/anacron_test
7        10   weekly_job /usr/bin/touch /root/anacron_weekly
@monthly 10   monthly_job /usr/bin/touch /root/anacron_monthly
```

```bash
anacron -T                          # test syntax of anacrontab (no output = OK)
```

---

## at

`at` schedules a **one-time** job.

```bash
at '15:00'                         # run at 3 PM today
at 'August 20 2024'                # run on a specific date
at '2:30 August 20 2024'           # specific date + time
at 'now + 30 minutes'
at 'now + 3 hours'
at 'now + 3 days'
at 'now + 3 weeks'
at 'now + 3 months'
```

After entering `at '<time>'`, type commands line by line, then press **Ctrl+D** to save.

```bash
atq                                # list pending at jobs
at -c 1                            # show contents of job #1
atrm 1                             # remove job #1
```

```bash
# Install if needed (Ubuntu)
sudo apt install at
```

---

## Bash Scripting Basics

```bash
#!/bin/bash                        # shebang — must be line 1, no leading space

# This is a comment

# Make executable
chmod u+x script.sh                # owner only
chmod +x script.sh                 # everyone

# Run
./script.sh
/home/aaron/script.sh

# if/test example
if test -f /tmp/archive.tar.gz; then
    mv /tmp/archive.tar.gz /tmp/archive.tar.gz.OLD
    tar acf /tmp/archive.tar.gz /etc/apt/
else
    tar acf /tmp/archive.tar.gz /etc/apt/
fi

# grep in if (returns 0=true if found, 1=false if not)
if grep -q '5' /etc/default/grub; then
    echo 'Timeout is 5 seconds'
fi
```

> Example scripts to study: `/etc/cron.daily/`, `/etc/cron.monthly/0anacron`

---

## Exam Tips

> **Exam tip:** Use **full paths** in all cron and anacron commands. `which touch` gives you `/usr/bin/touch`.

> **Exam tip:** `anacron` catches missed jobs; use it for tasks that run on machines that aren't always on.

> **Exam tip:** Scripts in `/etc/cron.*` directories must have no `.sh` extension and must be executable.

---

## Related Notes

- [[systemd]] — timer units as an alternative scheduler
- [[Process Management]]
- [[Operations & Deployment]]
