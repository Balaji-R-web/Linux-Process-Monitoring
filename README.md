# Project 3: Linux Process Monitoring

## 📌 Project Overview

This project demonstrates Linux process monitoring and management using command-line utilities on an Amazon Linux server. It covers viewing running processes, monitoring CPU and memory usage, identifying process IDs, checking system load, and managing processes safely.

## 🎯 Objectives

- Understand Linux processes and process IDs (PIDs).
- Monitor CPU and memory usage.
- View running processes.
- Identify resource-intensive processes.
- Check system load and uptime.
- Manage background processes.
- Stop processes safely.
- Inspect process details and parent processes.

## 🛠️ Environment

- Operating System: Amazon Linux
- Platform: AWS EC2
- Access: SSH Terminal
- Shell: Bash

---

## 1. Check the Current User

**Command:**

```bash
whoami
```

**Sample Output:**

```text
ec2-user
```

**Explanation:** Displays the current logged-in username. The output depends on your account.

## 2. Display System Uptime

**Command:**

```bash
uptime
```

**Sample Output:**

```text
19:30:00 up 5 days,  3:20,  1 user, load average: 0.08, 0.12, 0.10
```

**Explanation:** Displays the current time, system uptime, logged-in users, and load averages over 1, 5, and 15 minutes.

## 3. Display All Running Processes

**Command:**

```bash
ps -ef
```

**Sample Output:**

```text
UID          PID  PPID  C STIME TTY          TIME CMD
root           1     0  0 Oct05 ?        00:00:05 /sbin/init
root         650     1  0 Oct05 ?        00:00:01 /usr/sbin/sshd
ec2-user    1200   650  0 19:25 pts/0    00:00:00 -bash
```

**Explanation:** Lists processes with their users, PIDs, parent PIDs, start times, terminals, CPU time, and commands. These example rows are illustrative.

## 4. Display Processes in a Tree Structure

**Command:**

```bash
ps -ef --forest
```

**Explanation:** Displays processes in a tree-like format, helping you understand parent-child relationships.

An alternative is:

```bash
pstree -p
```

If `pstree` is unavailable, use the `ps` command instead or install the appropriate package if permitted.

## 5. Monitor Processes in Real Time

**Command:**

```bash
top
```

**Explanation:** Displays continuously updated process and system resource information.

Useful keys inside `top`:

- `P` — sort by CPU usage.
- `M` — sort by memory usage.
- `1` — show individual CPU statistics.
- `q` — quit.

**Expected result:** A live display of system load, CPU utilization, memory usage, and running processes.

## 6. Check Whether htop Is Available

**Command:**

```bash
command -v htop
```

If it is installed, launch it:

```bash
htop
```

**Explanation:** `htop` provides an interactive process-monitoring interface. It may not be installed by default on Amazon Linux.

## 7. Identify the Top CPU-Consuming Processes

**Command:**

```bash
ps -eo pid,ppid,comm,%cpu,%mem --sort=-%cpu | head -n 11
```

**Sample Output:**

```text
    PID    PPID COMMAND         %CPU %MEM
   1200     650 bash             0.0  0.1
      1       0 systemd          0.0  0.5
```

**Explanation:** Lists up to ten processes after the header, ordered by CPU usage. Actual output depends on system activity.

## 8. Identify the Top Memory-Consuming Processes

**Command:**

```bash
ps -eo pid,ppid,comm,%mem,rss --sort=-%mem | head -n 11
```

**Explanation:** Lists processes sorted by memory percentage. `RSS` represents resident memory in KiB on Linux.

## 9. Find a Specific Process

Search for SSH-related processes:

```bash
pgrep -a sshd
```

Or search using `ps`:

```bash
ps -ef | grep '[s]shd'
```

**Explanation:** Helps identify processes by name or command line. The exact results depend on what is running.

## 10. Inspect a Process by PID

**Command:**

```bash
ps -p 1 -o pid,ppid,user,stat,%cpu,%mem,cmd
```

**Sample Output:**

```text
    PID    PPID USER     STAT %CPU %MEM CMD
      1       0 root     Ss    0.0  0.5 /sbin/init
```

**Explanation:** Displays selected details for process ID 1. Its command and displayed statistics can differ by distribution.

## 11. Check Memory Usage

**Command:**

```bash
free -h
```

**Sample Output:**

```text
               total        used        free      shared  buff/cache   available
Mem:           1.9Gi       450Mi       700Mi        10Mi       800Mi       1.3Gi
Swap:             0B          0B          0B
```

**Explanation:** Displays physical memory and swap usage in a human-readable format. Values above are illustrative.

## 12. Check CPU Information

**Command:**

```bash
lscpu
```

**Explanation:** Displays CPU architecture, processor count, CPU model information, and other processor details.

To check the number of available processing units:

```bash
nproc
```

**Sample Output:**

```text
2
```

The number of CPUs depends on your EC2 instance type.

## 13. Check System Load

**Command:**

```bash
cat /proc/loadavg
```

**Sample Output:**

```text
0.08 0.12 0.10 1/250 1500
```

**Explanation:** Displays load averages for 1, 5, and 15 minutes, followed by runnable/total scheduling entities and the most recently created PID.

Load average is not the same as CPU percentage. Interpret it alongside the number of CPU cores and other metrics.

## 14. Monitor Processes for a Fixed Number of Updates

**Command:**

```bash
top -b -n 3 -d 2
```

**Explanation:** Runs `top` in batch mode for three updates, with a two-second interval. Useful for capturing process information in scripts or logs.

## 15. Create a Test Process

Create a harmless background process that sleeps for 300 seconds.

**Command:**

```bash
sleep 300 &
```

**Sample Output:**

```text
[1] 2345
```

**Explanation:** Starts a background process. The PID shown is only an example; record the actual PID returned by your terminal.

Save the PID in a variable:

```bash
TEST_PID=$!
echo "$TEST_PID"
```

## 16. Check the Test Process

**Command:**

```bash
ps -p "$TEST_PID" -o pid,ppid,stat,cmd
```

**Sample Output:**

```text
    PID    PPID STAT CMD
   2345    1200 S    sleep 300
```

**Explanation:** Verifies that the test process exists and displays its state. Use the PID captured in the same shell.

## 17. Stop the Test Process Gracefully

**Command:**

```bash
kill -TERM "$TEST_PID"
```

Verify:

```bash
ps -p "$TEST_PID"
```

**Expected result:** If the process has exited, `ps` will display only its header and return a nonzero status.

**Explanation:** Sends the `SIGTERM` signal, requesting that the process terminate gracefully.

If the process has already exited, there is no need to stop it again.

## 18. Understand Process Signals

Common signals:

| Signal | Number | Purpose |
|---|---:|---|
| `SIGTERM` | 15 | Requests graceful termination |
| `SIGKILL` | 9 | Forces termination |
| `SIGSTOP` | 19 on common x86 Linux systems | Suspends a process |
| `SIGCONT` | 18 on common x86 Linux systems | Resumes a stopped process |

**Example:**

```bash
kill -TERM PID
```

Replace `PID` with the actual numeric process ID.

**Important:** Avoid terminating system services or unfamiliar processes. `SIGKILL` does not allow a process to clean up before termination.

## 19. Check Background Jobs

**Command:**

```bash
sleep 60 &
jobs -l
```

**Sample Output:**

```text
[1]+ 2346 Running                 sleep 60 &
```

**Explanation:** `jobs -l` lists background jobs associated with the current shell. Job numbers and PIDs will vary.

Wait for the job to finish:

```bash
wait
```

This waits for the shell's outstanding background jobs.

## 20. Check Process File Descriptors

For a process that is still running, use its actual PID:

```bash
ls -l /proc/PID/fd
```

Replace `PID` with a real numeric PID, for example:

```bash
ls -l /proc/1/fd
```

**Explanation:** Displays file descriptors associated with the selected process, subject to system permissions.

## 21. Check Process Resource Limits

**Command:**

```bash
ulimit -a
```

**Explanation:** Displays resource limits for the current shell, including limits related to open files and process scheduling.

For a specific process, if permitted:

```bash
cat /proc/1/limits
```

**Explanation:** Displays the resource limits applicable to process ID 1.

## 22. Monitor Process Statistics Through /proc

**Command:**

```bash
cat /proc/1/status
```

**Explanation:** Displays detailed information about process ID 1, including its state, memory information, and thread count.

To inspect a different process, substitute its PID in the path.

## 23. Capture a Basic Process Report

**Command:**

```bash
mkdir -p ~/process-monitoring
ps -eo pid,ppid,user,stat,%cpu,%mem,comm --sort=-%cpu \
  > ~/process-monitoring/process-report.txt
```

Verify the report:

```bash
head -n 10 ~/process-monitoring/process-report.txt
```

**Explanation:** Saves a process snapshot to a text file for later review.

## 24. Add a Timestamp to the Report

**Command:**

```bash
{
  echo "Process Monitoring Report"
  echo "Generated: $(date)"
  echo
  ps -eo pid,ppid,user,stat,%cpu,%mem,comm --sort=-%cpu
} > ~/process-monitoring/process-report.txt
```

Display the report:

```bash
cat ~/process-monitoring/process-report.txt
```

**Explanation:** Generates a timestamped report containing process and resource information.

## 📚 Important Commands Learned

| Command | Purpose |
|---|---|
| `uptime` | Check uptime and load averages |
| `ps -ef` | List processes |
| `ps --forest` | Display process hierarchy |
| `top` | Monitor processes interactively |
| `pgrep` | Search for processes |
| `free -h` | Check memory usage |
| `lscpu` | Display CPU information |
| `nproc` | Count processing units |
| `kill` | Send a signal to a process |
| `jobs -l` | List shell background jobs |
| `ulimit -a` | Display shell resource limits |
| `/proc/PID/status` | Inspect process details |

## 🔐 Best Practices

- Monitor CPU, memory, and system load together.
- Investigate resource spikes before terminating processes.
- Use `SIGTERM` before considering `SIGKILL`.
- Avoid stopping production processes without authorization.
- Record process snapshots to help troubleshoot incidents.
- Use monitoring tools appropriate to the server's workload.

## ✅ Project Outcome

This project demonstrates practical Linux process monitoring, CPU and memory inspection, process identification, signal handling, and basic resource troubleshooting on Amazon Linux.

## 👨‍💻 Technologies Used

- Linux
- Amazon Linux
- Bash
- Process Monitoring Utilities
- `/proc` Filesystem
- AWS EC2
