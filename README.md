# Linux Administrator – AI-Assisted Infrastructure Assignment

**Candidate Name:** Yash Rathod

**Role Applied:** DevOps Engineer

**Environment:** Linux VMs on VMware vSphere / ESXi

---

## 1. Problem Explanation

### 1.1 Performance Issues in Linux VMs on vSphere

Linux virtual machines may experience high CPU, memory, or disk usage due to:

* Application resource leaks
* Inefficient process scheduling
* Kernel or filesystem checking
* CPU Usage, Memory allocation, or swapping at server level

Diagnosing issues requires **OS metrics and virtualization layer metrics**.

### 1.2 VM Unresponsiveness and Downtime

Linux VMs may become unresponsive due to:

* Hung processes
* Application deadlocks
* Kernel lockups
* Resource exhaustion
* Network or storage stalls

Manual intervention increases downtime and operational risk.

---

## 2. ( AI Prompt 1 ) – Diagnosing High CPU / Memory / Disk Usage

### 2.1 AI Prompt

```
I have a Linux VM running on VMware that is experiencing high CPU and memory usage.

1. OS-level investigation (top, htop, free, iostat)
2. Process-level root cause identification
3. Disk I/O checking
4. VMware-level checks (CPU ready time, memory allocation, swapping)
5. Correlation between OS metrics and vSphere metrics
6. Long-term preventive recommendations
```

### 2.2 Technical Strategy – Performance Diagnosis

**Step 1: OS-Level Checks**

* `top`, `htop`        → Identify CPU-heavy processes
* `free -m`            → Check Memory pressure
* `iostat -xz`         → Identify Disk latency

**Step 2: Process-Level Analysis**

* Identify Running processes
* Check thread count, open files

**Step 3: VMware-Level Validation**

* CPU Usage
* Memory allocation
* Swap activity

**Step 4: Long term Solution**

* Short-term: restart service, optimize config
* Long-term: resize VM, fix application logic

### 2.3 Diagnostic Script

```bash
#!/bin/bash
echo "CPU Usage ====
top -b -n1 | head -20

echo "Memory Usage ===="
free -m

echo "Disk I/O ===="
iostat -xz 1 3
```

#### CPU Usage

``` top ``` : Shows real-time CPU, memory, and process usage

``` -b (batch mode) ``` : Runs top non-interactive
Required for scripts

``` -n1 ``` : Runs top only once (1 refresh)

``` | (pipe) ``` : sends output to next command

``` head -20 ``` : shows only first 20 lines

#### Memory Usage

``` -m ``` : Displays values in MB (Megabytes)

#### Disk I/O 
``` iostat ```: Shows disk performance stats

``` -x ``` : Extended statistics (important for troubleshooting)

``` -z ``` : Hides devices with zero activity

``` 1 ``` : Refresh interval = 1 second

``` 3 ``` : Show output 3 times

---

## 3. ( AI Prompt 2 ) – Auto-Recovery for Unresponsive Linux VM

### 3.1 AI Prompt

```
Design an auto-recovery mechanism for a Linux VM running on VMware vSphere that becomes unresponsive.

Requirements:
1. how can i check unresponsiveness using (ping, SSH, application health check)
2. Attempt soft recover works using (service restart, resource cleanup) ?
3. can i reboot VM if required?
4. Integrate with vSphere APIs if OS-level recovery fails
5. Use shell/Python/Ansible for automation
6. Log every action and recovery attempt
7. Ensure no data corruption during recovery

Provide:
- Architecture flow
- Sample scripts
- Decision logic
- Failure handling scenarios
```

### 3.2 Steps for Diagnosis unresponsiveness

* Defining a **tiered recovery strategy**
* Preventing unnecessary VM reboots
* Providing automation templates
* Improving overall system availability
* Aligning recovery logic

### 3.3 Technical Strategy – Auto-Recovery

**Level 1:** Health checks (ping, SSH, app endpoint)

**Level 2:** Service restart

**Level 3:** Resource cleanup

**Level 4:** VM reboot

**Level 5:** vSphere-level intervention All actions are logged for auditing and RCA.

### 3.5 Auto-Recovery Script

```bash
#!/bin/bash

HOST="localhost"
SERVICE="nginx.service"
LOG="/var/log/auto_recovery.log"

echo "$(date) - Health check started" >> $LOG

if ! ping -c 2 $HOST >/dev/null; then
  echo "$(date) - Host unreachable" >> $LOG

  systemctl restart $SERVICE
  sleep 30

  if ! systemctl is-active --quiet $SERVICE; then
    echo "$(date) - Service restart failed, rebooting VM" >> $LOG
    reboot
  fi
fi
```
