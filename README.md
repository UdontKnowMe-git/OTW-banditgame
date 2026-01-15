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
