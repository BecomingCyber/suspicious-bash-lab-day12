# 📝 Log Analysis Notes – Day #12: Suspicious Bash Script Execution

## 🔍 Incident Overview

- **Incident Type:** Suspicious Bash Script Execution
- **Detection Source:** File activity monitoring (flagged `/tmp/payload.sh`)
- **Attack Vector:** Script downloads and executes a remote payload via `curl`
- **System:** Kali Linux (user: kali)

---

## 🧪 Evidence Collected

### ✅ File Identified

- **File Name:** `/tmp/payload.sh`
- **Content Summary:** Used `curl` to download another script from `http://malicious.site/payload.sh` and pipe it to `bash`
- **Command to View:**
  ```bash
  cat /tmp/payload.sh
  ```

## 🧠 Bash History Review
- Command Used:
```bash
grep payload.sh ~/.bash_history
```
- Result: Confirmed script was executed manually
  
## 🧍Process Inspection
- Command Used:
```bash
ps aux | grep '[c]url'
```

- Result: Active curl process observed (later killed)

## 🗃️ Temp File Check
- Command Used:
```bash
sudo find /tmp -name "*.sh"
```
- Result: Confirmed presence of payload.sh

## 🕒 Persistence Check
## 🔹 User Crontab
- Command Used:
```bash
crontab -l
```
- Result: no crontab for kali — ✅ No persistence detected

## 🔹 System Crontab
- Command Used:
```bash
sudo grep -r "payload.sh" /etc/cron*
```
- Result: No persistence mechanisms found

## 🔒 Containment and Eradication
- Kill Process:
```
sudo pkill curl
```
- Remove Script:
``` 
sudo rm -f /tmp/payload.sh
```
- Verify Deletion:
```bash
ls -l /tmp/payload.sh
```
No such file or directory – Confirmed removed

# 📋 Final Recommendations
## 🔐 Recommendations

| Recommendation              | Description                                                             |
|----------------------------|-------------------------------------------------------------------------|
| 🔐 **noexec on /tmp**      | Prevent script execution from `/tmp` by remounting it with `noexec`     |
| 🛡️ **File Integrity Monitoring** | Use tools like **AIDE** to detect unauthorized changes in system directories |
| 📚 **User Education**       | Train users not to run unfamiliar or unsigned scripts                   |
| 📜 **Audit Logging**        | Ensure `auditd` or system logging is enabled to trace commands/files     |

## 📎 Summary
- Script Detected: /tmp/payload.sh

- Action Taken: Process killed, script deleted

- Persistence Found: None

- User Involved: kali

- Final Status: ✅ System clean and incident resolved

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
