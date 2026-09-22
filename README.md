= LAB 5: Windows/Linux Security Baseline
:author: [Your Name]
:date: [Date]
:toc: left
:toclevels: 3
:sectnums:

== Objective
Apply the concepts from Lesson 5 in an isolated, authorized environment.

== Lab Setup
Instructor-provided virtual machine(s), Packet Tracer, or other approved training environment as appropriate.

---

== Procedure

=== Step 1: Create a Non-Administrator Lab Account

*Windows:*
[source,powershell]
----
# Open PowerShell as Administrator
net user labuser P@ssw0rd123! /add
net localgroup Users labuser /add
----

*Linux:*
[source,bash]
----
sudo adduser labuser
sudo usermod -aG users labuser
----

**Observation:** A new standard user account was created without administrative privileges.

---

=== Step 2: Review Running Processes

*Windows:*
[source,powershell]
----
tasklist
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10
----

*Linux:*
[source,bash]
----
ps aux
top -b -n 1 | head -20
----

**Observation:** List of active processes recorded.

---

=== Step 3: Review Services

*Windows:*
[source,powershell]
----
Get-Service | Where-Object {$_.Status -eq "Running"}
----

*Linux:*
[source,bash]
----
systemctl list-units --type=service --state=running
----

**Observation:** List of running services recorded.

---

=== Step 4: Open Security / System Logs

*Windows:*
[source,powershell]
----
eventvwr.msc
Get-EventLog -LogName Security -Newest 20
----

*Linux:*
[source,bash]
----
sudo tail -n 50 /var/log/syslog
sudo journalctl -n 50
----

**Observation:** Security and system logs reviewed.

---

=== Step 5: Record Five Observations

. Observation 1: The `labuser` account has no administrative privileges.
. Observation 2: There are multiple background processes running (e.g., `svchost.exe` on Windows, `systemd` on Linux).
. Observation 3: Several services are running that may not be needed (e.g., `Print Spooler`, `Bluetooth`).
. Observation 4: Security logs show login attempts and system events.
. Observation 5: Default configurations may leave unnecessary ports open.

---

=== Step 6: Apply Two Safe Hardening Changes

*Change 1: Disable Unused Service (Windows: Print Spooler)*
[source,powershell]
----
Stop-Service -Name Spooler
Set-Service -Name Spooler -StartupType Disabled
----

*Change 1: Disable Unused Service (Linux: Bluetooth)*
[source,bash]
----
sudo systemctl stop bluetooth
sudo systemctl disable bluetooth
----

*Change 2: Enforce Password Policy (Windows)*
[source,powershell]
----
net accounts /minpwlen:12 /maxpwage:60
----

*Change 2: Enforce Password Policy (Linux)*
[source,bash]
----
sudo apt install libpam-pwquality -y
sudo nano /etc/security/pwquality.conf
# Set: minlen = 12
----

**Observation:** Two hardening changes applied successfully.

---

== Student Questions

=== Q33: What is the difference between a user and a process?

*User:* A user is a person or entity that interacts with the system. A user has an identity, credentials, and permissions.

*Process:* A process is a running instance of a program. It executes in memory and uses system resources (CPU, RAM). A process runs under the context of a user.

*Key Difference:* A user is an identity; a process is an execution. A user can start many processes.

---

=== Q34: Why should normal users avoid administrator/root privileges?

*Principle of Least Privilege:* Normal users should only have the permissions they need to perform their tasks.

*Security Reasons:*
. Malware executed under a standard user cannot modify system files.
. Accidental damage to the system is limited.
. Attackers who compromise a standard user cannot take over the entire system.
. It reduces the attack surface.

*Best Practice:* Use `sudo` or "Run as Administrator" only when necessary.

---

== Deliverable

* Baseline report with before/after screenshots.

== Hardening Checklist

- [x] Create non-admin account
- [x] Review running processes
- [x] Review running services
- [x] Review security/system logs
- [x] Record five observations
- [x] Apply two hardening changes
- [ ] Take before/after screenshots
- [ ] Upload report to GitHub

== References

* Lesson 5: Security Baseline Concepts
* Microsoft Docs: https://docs.microsoft.com/en-us/powershell/
* Linux Man Pages: https://man7.org/linux/man-pages/
