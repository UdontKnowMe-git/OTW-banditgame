# 🛡️ Cybersecurity Journey: The Quest for Knowledge
> "The more you know, the more you realize you don't know."

## About This Repo
This repository serves as my personal lab notebook and progress tracker as I transition into the world of **Ethical Hacking and Bug Bounty Hunting**. 

My goal is to move beyond "script kiddie" status by building a rock-solid foundation in Linux internals, networking, and web vulnerabilities. I am documenting my solutions, thought processes, and the hurdles I've overcome to sharpen my technical mindset.

## Current Focus: OverTheWire (Bandit)
I am currently working through the **Bandit** wargames. These challenges are designed to teach the fundamentals of the Linux command line and security concepts.

### Skills I'm Sharpening:
* **Linux Navigation:** Mastering the CLI and filesystem hierarchy.
* **Data Filtering:** Using `grep`, `awk`, `sed`, and `strings` to find needles in haystacks.
* **File Metadata:** Understanding permissions, file types, and hidden attributes.
* **SSH & Networking:** Securely connecting to remote servers and understanding port configurations.

## Learning Path
- [x] **Phase 1: Linux Foundations** (In Progress: OverTheWire Bandit)
- [ ] **Phase 2: Networking Basics** (Planned: TryHackMe Pre-Security)
- [ ] **Phase 3: Web Security** (Planned: PortSwigger Academy)
- [ ] **Phase 4: Active Bug Bounty Hunting** (Planned: Hacker101 / Bugcrowd)

## Latest Breakthroughs
* **Level 6 -> 7: Mastered I/O Redirection & Stream Merging**
  * Learned that Linux separates data into **stdout** (Success) and **stderr** (Errors).
  * Discovered the `2>/dev/null` trick to silence permission errors while searching system-wide directories.
  * Mastered the `2>&1` operator to merge streams, allowing `grep` to filter both successful output and error messages simultaneously.
  * Internalized the difference between `>` (overwrite) and `>>` (append) for persistent note-taking.
* **Level 12 -> 13: The Hex/Compression Gauntlet**
  * **Objective:** Reverse a hex dump and peel back multiple layers of nested compression.
  * **Tools Used:** `xxd -r` (Hex reverse), `file` (Signature analysis), `tar`, `gzip`, `bzip2`.
  * **Key Takeaway:** Filename extensions are irrelevant in Linux; the "Magic Bytes" (headers) determined by the `file` command are the only truth.
  * **Workflow:** Revert Hex -> Identify Type -> Rename -> Decompress -> Repeat.
* **Level 13 -> 14: Data Exfiltration & Windows SSH Hardening**
  * **The Hurdle:** Internal pivoting via localhost was blocked by server-side resource rules.
  * **The Solution:** Exfiltrated the RSA Private Key to the local host (laptop) to initiate a direct connection to the target user (`bandit14`).
  * **Technical Challenge:** Windows NTFS permissions are broader than Linux. Had to use `icacls` to strip inherited permissions so the OpenSSH client would accept the key as "secure."
* **Level 15 -> 16: Encrypted Communication (SSL/TLS)**
  * **Concept:** Transitioning from plaintext (HTTP/Telnet style) to encrypted streams.
  * **Tool:** `openssl s_client -connect localhost:30001`
  * **Key Insight:** Standard tools like `nc` (Netcat) cannot negotiate the SSL/TLS handshake. Using `openssl` allows the client to provide the necessary encryption layer to "talk" to the service.
  * **Discovery:** Learned to look for the `---` end-of-certificate marker to know when the connection is ready for input.

* **Level 16 -> 17: Network Reconnaissance & Service Fingerprinting**
  * **Concept:** Hunting for a specific needle in a haystack of open ports.
  * **Tool:** `nmap -sV -p 31000-32000 localhost`
  * **The Breakthrough:** Identified that an "open" port isn't always the "right" port. Used Service Versioning (`-sV`) to find the `ssl/unknown` service while ignoring the `echo` and `ssl/echo` red herrings.
  * **Technical Skill:** Handshaking with multiple ports to identify logic-based responses vs. simple mirrors.

* **Level 18 -> 19: Bypassing Shell Restrictions**
  * **Concept:** Non-interactive shell execution.
  * **The Hurdle:** An `.exit` or `logout` command placed in the remote user's `.bashrc` or `.profile` to kill interactive sessions immediately.
  * **The Solution:** Appending a command string to the SSH connection: `ssh bandit18@host -p 2220 "cat readme"`.
  * **Insight:** Learned that SSH can execute single commands in a non-interactive buffer, bypassing the scripts that only trigger for interactive TTY sessions.
* **Level 21 -> 22: Cron Job Analysis**
  * **Tool:** `ls /etc/cron.d/` and `cat`.
  * **Concept:** Scheduled tasks (Cron) can be used to perform background actions as different users.
  * **Technique:** Identified a script running every minute as `bandit22`. By analyzing the script's contents, I can find where it stores processed data or temporary files.
  * **Logic:** If a root/high-privilege script writes to a world-readable location (like `/tmp`), the security of the password is compromised.
* **Level 23 -> 24: Script Spooling & Permissions**
  * **The Strategy:** Create a bash script that 'leaks' the target password to a world-writable directory.
  * **Critical Requirement:** The script must have execute permissions (`chmod +x`) and the destination folder must be world-writable (`chmod 777`).
  * **Lesson:** Scheduled tasks run in the context of their owner. I must provide a script the owner *can* and *will* execute, then wait for the system clock to trigger it.
* **Level 25 -> 26: Pager-Based Shell Escape**
  * **The Hurdle:** The target user has a non-standard shell (`/usr/bin/showtext`) that exits immediately after printing a banner.
  * **The Vulnerability:** The shell uses `more` to display text. Pagers like `more` or `less` allow users to open editors (like Vim) if the text exceeds the screen height.
  * **The Attack:**
    1. Resized the terminal to a few lines to force `more` to pause.
    2. Pressed `v` to jump into Vim.
    3. Used Vim's internal command `:set shell=/bin/bash` followed by `:shell` to spawn a functional shell.
  * **Lesson:** Any program that allows a user to "drop into an editor" is a potential path to full shell access.
