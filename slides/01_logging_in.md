---
title: "Connecting to Roihu"
subtitle: "This topic is about how to login to the Roihu supercomputer."
lang: en
---

# Login via the web interfaces

- The easiest way to login to Roihu supercomputer is via [roihu.csc.fi](https://www.roihu.csc.fi)
- Login requires 1) [multi-factor authentication](https://docs.csc.fi/accounts/mfa/) (MFA) and 2) a medium or high level of identity assurance (LoA)
  - Haka is recommended if your home organization already requires MFA. Otherwise, [activate CSC MFA in MyCSC](https://my.csc.fi/mfa-activation-login) and use your CSC credentials
  - Check your LoA in [MyCSC](https://my.csc.fi/profile) and [elevate it if needed](https://docs.csc.fi/accounts/strong-identification)
- The web interface can be used, _e.g._, to launch GUI applications, browse files or open a command-line shell
   - The latter is useful if your computer does not have an SSH client, but you need command-line access to the supercomputer
- LUMI also has a [similar web interface](https://docs.lumi-supercomputer.eu/runjobs/webui/).

# Login with SSH (1/3)

- SSH is a terminal program that gives you command-line access on the CSC supercomputer
- It is a versatile main interface to a supercomputer
   - Laptop &harr; Toyota, Supercomputer &harr; F1. F1 needs a specialist interface.
- Logging in with SSH requires **SSH keys** and a valid **SSH certificate**
  - A key pair is created and the **public** key is uploaded to MyCSC
  - The public key must then be signed **every 24 hours** to obtain a time-based certificate allowing SSH logins to Roihu
  - Read the [documentation](https://docs.csc.fi/computing/connecting/ssh-keys/) and do the [tutorial](https://csc-training.github.io/csc-env-eff/hands-on/connecting/ssh-keys.html)
  - Consult the [FAQ](https://docs.csc.fi/support/faq/ssh-keys-not-working/) or contact <servicedesk@csc.fi> to troubleshoot issues

# Login with SSH (2/3)

- After adding your public key to MyCSC, you need to sign it to generate an SSH certificate
   - The [CSC certificate helper tool](https://github.com/CSCfi/certificate-helper-tool) is recommended
     - `python3 csc_cert.py -u cscusername /path/to/local/public/key`
     - Signs the key, downloads the certificate, and adds it to your **SSH agent** to make logging in easy
     - Python is required on all operating systems
     - Using authentication agent also requires:
       - `ssh-agent` utility (Linux/macOS/PowerShell/MobaXterm terminal users) **or**
       - Pageant & WinSCP (PuTTY/MobaXterm GUI users)
   - Alternatively, users can sign and download their SSH certificate manually in MyCSC

# Login with SSH (3/3)

- Roihu has separate login nodes for the CPU and GPU partitions. Once SSH keys and certificate are set up, connect with `ssh` command:
  - Roihu-CPU: `ssh cscusername@roihu-cpu.csc.fi`
  - Roihu-GPU: `ssh cscusername@roihu-gpu.csc.fi`
  - Why? Roihu CPUs and GPUs have different **architectures** (x86 vs. ARM)
- Detailed instructions for [SSH login on macOS and Linux](https://docs.csc.fi/computing/connecting/ssh-unix/) and [on Windows](https://docs.csc.fi/computing/connecting/ssh-windows/)
  - On Windows, we recommend MobaXterm or PuTTY clients, or simply using the web interfaces instead of SSH
- Note! Plain SSH will not allow displaying remote graphics
   - The web interfaces are often best for this, but a graphical connection can also be enabled over SSH using [X11 forwarding](https://docs.csc.fi/computing/connecting/#graphical-connection)

# Moving files between a local computer and Roihu

- [`scp`](https://docs.csc.fi/data/moving/scp/) and [`rsync`](https://docs.csc.fi/data/moving/rsync/) are powerful command-line tools to copy files
   - `scp` works even in Windows PowerShell (but `rsync` is missing)
   - `scp filename cscusername@roihu-cpu.csc.fi:/scratch/project_xxxx`
   - `rsync -r foldername cscusername@roihu-cpu.csc.fi:/scratch/project_xxxx`
- Sometimes a [GUI tool for transferring files](https://docs.csc.fi/data/moving/graphical_transfer/) is more convenient
   - Nice tools are _e.g._ _FileZilla_ and _WinSCP_
   - _MobaXterm_ also has a file transfer GUI (Tip: set persistent home directory)
   - Roihu web interface can also be used to easily upload/download files
- Note! These file transfer tools (except Roihu web interface) are SSH-based, so using them at CSC requires SSH keys and a valid certificate!

# Moving files between Puhti and Roihu (1/2)

- SSH keys and certificate should only be stored on your local computer
- To access Roihu from Puhti, you must ensure your local SSH keys and certificate are **forwarded** to Puhti
  - This is called _SSH agent forwarding_
  - Allows using e.g. `rsync` or `scp` to move files directly from Puhti to Roihu
  - If you want to do the reverse, i.e. access Puhti from Roihu, it is enough to forward your SSH keys since Puhti does not require SSH certificates for authentication
- It is **strongly** recommended to use the CSC certificate helper tool to make adding SSH keys and certificate to SSH agent easy

# Moving files between Puhti and Roihu (2/2)

- On Linux/macOS, simply add option `-A` to your SSH command:
  - `ssh -A cscusername@puhti.csc.fi`
- Using MobaXterm:
  - Similar to Linux/macOS if using local terminal, otherwise toggle _Allow agent forwarding_ under "Session" -> "SSH" -> "Advanced SSH settings" -> "Expert SSH settings"
- Using PuTTY:
  - Select "SSH" -> "Connection" -> "Auth" and toggle option _Allow agent forwarding_ under "Other authentication-related options"

# License

![](https://mirrors.creativecommons.org/presskit/buttons/88x31/png/by-sa.png)

<small>
All materials (c) 2020-2026 by CSC – IT Center for Science Ltd.
This work is licensed under a **Creative Commons Attribution-ShareAlike** 4.0
Unported License, [http://creativecommons.org/licenses/by-sa/4.0/](http://creativecommons.org/licenses/by-sa/4.0/)
</small>
