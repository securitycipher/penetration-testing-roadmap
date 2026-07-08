# Linux
Linux is an open-source operating system that serves as an alternative to popular operating systems like Windows and macOS. It's known for its stability, security, and flexibility. Here are some key points to help you understand Linux better:

## Open Source

- Linux is built on the principles of open-source software. This means that its source code is freely available to the public, and anyone can view, modify, and distribute it.
- This openness encourages collaboration and allows users to customize the system according to their needs.
## Distributions (Distros)

- Linux comes in various distributions, commonly referred to as "distros." Each distro is a different version of the Linux operating system with its own set of features and pre-installed software.
- Some popular distros include Ubuntu, Fedora, Debian, and CentOS.
## User Interface

- Linux offers different desktop environments, such as GNOME, KDE, XFCE, and others. These environments provide the graphical user interface (GUI) that users interact with.
- However, Linux can also be used entirely through the command line interface (CLI), which might seem intimidating at first but becomes powerful and efficient with practice.
## Package Management

- Linux uses package management systems to install, update, and remove software. Each distro has its own package manager (e.g., apt for Debian/Ubuntu, yum for CentOS/Fedora). This makes software installation and maintenance straightforward.
## Terminal Commands

- The command line interface, accessed through the terminal, is a powerful aspect of Linux. It allows users to perform a wide range of tasks using text commands.
- Basic commands include ls (list files), cd (change directory), cp (copy), mv (move), and rm (remove). Learning a few commands can go a long way in using Linux efficiently.
## Security

- Linux is known for its robust security features. User permissions are carefully managed, and the system is less prone to malware and viruses compared to other operating systems.
- Regular security updates are released, and users have control over firewall settings.
## Multitasking and Stability

- Linux is designed to handle multiple tasks simultaneously, making it an excellent choice for servers and high-performance computing.
- The system is known for its stability; Linux servers often run for extended periods without needing a reboot.
## Community Support

- The Linux community is vast and active. Online forums, documentation, and community-driven support make it easy for users, especially beginners, to find help and resources.
## Server Usage

- Linux is widely used in server environments. Many web servers, cloud services, and networking devices run Linux due to its stability, security, and scalability.
## Learning Resources

- If you're new to Linux, there are plenty of online resources and tutorials to help you get started. Websites like Linux Journey, Ubuntu Documentation, and the Arch Wiki provide valuable information.

In summary, Linux is a versatile operating system with a strong emphasis on customization, security, and community collaboration. While it may take some time to become familiar with its nuances, learning Linux can be a rewarding experience, offering you a deeper understanding of how computer systems work.

---

## Linux for penetration testers

Linux is both the attacker's platform (Kali/Parrot) and the most common server target. You must be fluent in navigating and escalating on it.

### Post-exploitation enumeration (run right after getting a shell)

```bash
# Who am I and what can I do?
id; whoami; sudo -l
hostname; uname -a; cat /etc/os-release

# Users and interesting files
cat /etc/passwd; cat /etc/shadow 2>/dev/null    # shadow = jackpot if readable
ls -la /home/*; cat /home/*/.bash_history
find / -name "id_rsa" 2>/dev/null               # SSH keys

# Network and running services
ip a; ss -tulpn; netstat -antup 2>/dev/null
ps aux --forest

# Scheduled jobs and services (privesc leads)
cat /etc/crontab; ls -la /etc/cron.*
systemctl list-timers
```

### Privilege escalation quick wins

```bash
# SUID/SGID binaries — check each against GTFOBins
find / -perm -4000 -type f 2>/dev/null
find / -perm -2000 -type f 2>/dev/null

# Writable files owned by root, world-writable dirs
find / -writable -type f 2>/dev/null | grep -v /proc
find / -perm -2 -type d 2>/dev/null

# Sudo misconfig -> GTFOBins (e.g., sudo vim -c ':!/bin/sh')
sudo -l

# Kernel exploits — match version to known CVEs
uname -r

# Capabilities
getcap -r / 2>/dev/null
```

### Automated enumeration

```bash
# Drop and run one of these on the target
./linpeas.sh
./LinEnum.sh
python3 linux-exploit-suggester.py
```

### Key privesc vectors

- **Sudo misconfigurations** → [GTFOBins](https://gtfobins.github.io/)
- **SUID binaries** with exploitable behavior
- **Writable `/etc/passwd`** or cron scripts
- **Kernel exploits** (DirtyPipe, DirtyCow, PwnKit/polkit)
- **Docker group membership** → instant root

## Related

- [Privilege Escalation](../Vulnerabilities/Privilege%20Escalation.md)
- [Operating System Hardening](Operating%20System%20Hardening.md)
