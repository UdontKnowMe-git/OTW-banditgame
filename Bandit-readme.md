## Breakthroughs
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
* **Level 30 -> 31: Git Tag Forensics**
  * **The Problem:** `git log` and `git branch` showed a perfectly clean, boring history with no obvious leaks.
  * **The Breakthrough:** Realized that Git metadata includes 'Tags' which can point to objects not referenced in the current branch's HEAD.
  * **The Process:** 1. `git tag` revealed a hidden tag.
    2. `git show [tag_name]` displayed the contents associated with that tag.
  * **Key Concept:** Tags are static pointers in Git that can survive even if the branches they were created on are deleted or moved.
* **Level 32: The SHOUTSHELL Escape**
  * **The Challenge:** Logged into a restricted shell that converted all input to uppercase using a wrapper script. Since Linux is case-sensitive, commands like `ls` or `cat` failed as `LS` and `CAT`.
  * **The Vulnerability:** The shell's uppercase filter only affected literal text input, not shell expansion variables.
  * **The Bypass:** Used the positional parameter `$0`. 
  * **How it works:** In Bash, `$0` represents the name of the currently executing process. By executing `$0` directly, I forced the system to start a new instance of the execution context. 
  * **The Result:** This spawned a standard, non-restricted Bourne Shell (`sh`) that didn't have the uppercase filter, allowing me to `cat /etc/bandit_pass/bandit33`.
