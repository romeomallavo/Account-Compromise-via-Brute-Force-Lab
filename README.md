# Brute Force Attack Analysis Lab: Hack The Box Brutus Challenge

## Project Overview

This lab involves analyzing a brute force attack on a Confluence server using logs from the Hack The Box Sherlock challenge named "Brutus." We will focus on identifying unauthorized access attempts, tracking successful logins, and understanding the attacker's activities post-compromise.

## Setup

1. **Install Windows Subsystem for Linux (WSL):**
   - Open PowerShell as an administrator and run:
     ```bash
     wsl --install
     ```
   - For more details, follow the [WSL Installation Guide](https://docs.microsoft.com/en-us/windows/wsl/install).

2. **Download and Extract Brutus Logs:**
   - Go to [Hack The Box](https://hackthebox.eu).
   - Navigate to the "Sherlock" challenges and select the "Brutus" challenge.
   - Download the `Brutus.zip` file and extract it using the password `hacktheblue`:
     ```bash
     unzip Brutus.zip -P hacktheblue
     ```
   - Verify the extracted files:
     ```bash
     ls Brutus/
     # Output: auth.log, wtmp
     ```

3. **Tools Used:**
   - **WSL (Windows Subsystem for Linux)**: Linux environment for Windows.
   - **cat, grep, cut, sort**: Commands used for log analysis.
   - **curl**: For gathering IP information.

## Log Analysis

1. **List of Files:**
   - **auth.log**: Contains authentication-related logs, including successful and failed login attempts.
   - **wtmp**: Tracks successful login activities, providing source IP, username, and timestamps.

2. **Analyzing `auth.log`:**
   - **View Log File:**
     ```bash
     cat auth.log
     ```
   - **Extract Failed Login Attempts:**
     ```bash
     grep -i 'failed password' auth.log
     ```
   - **Exclude Invalid User Attempts:**
     ```bash
     grep -i 'failed password' auth.log | grep -vi 'invalid'
     ```
   - **Extract Usernames:**
     ```bash
     grep -i 'failed password' auth.log | cut -d ' ' -f 10 | sort | uniq -c | sort -nr
     ```
   - **Identify IP Addresses:**
     ```bash
     grep -i 'failed password' auth.log | cut -d ' ' -f 12 | sort | uniq -c | sort -nr
     ```

3. **Analyzing `wtmp`:**
   - **Read `wtmp` File:**
     ```bash
     last -f wtmp
     ```
   - **Find Successful Logins for Specific IP:**
     ```bash
     last -f wtmp | grep '65.21.161.168'
     ```

4. **Grep and Cut Commands:**
   - **Using `grep` to Find Accepted Passwords:**
     ```bash
     grep -i 'accepted password' auth.log
     ```
   - **Using `cut` to Extract Specific Fields:**
     ```bash
     cat auth.log | cut -d ' ' -f 1,2,3,10,12
     ```

5. **Analyzing IP Information:**
   - **Use `curl` to Get IP Details:**
     ```bash
     curl ipinfo.io/65.21.161.168
     ```

## Findings

1. **Brute Force Analysis Summary:**
   - The attacker used IP `65.21.161.168` from Amazon's Mumbai data center to perform brute force attacks on the server.
   - **Successful Login:** The attacker gained access to the server with the username `root`.

2. **Persistence Strategy:**
   - The attacker created a new user account `cyberjunkie` and added it to the `sudo` group for administrative privileges.

3. **Indicators of Compromise (IOCs):**
   - **Attacker IP:** `65.21.161.168`
   - **Compromised Account:** `root`
   - **New User Created:** `cyberjunkie`

## Conclusion

This lab demonstrated how to analyze brute force attacks using Linux commands like `grep`, `cut`, and `sort` on log files. Understanding such attacks and their patterns is crucial for SOC analysts in identifying and mitigating security threats.

## Resources

- [Hack The Box](https://hackthebox.eu)
- [Linux Command Line Basics](https://linuxcommand.org/)
- [IPInfo](https://ipinfo.io/)

## Notes

Always conduct log analysis in a controlled environment. Be cautious when handling sensitive log files and avoid exposing them to external networks.

---
