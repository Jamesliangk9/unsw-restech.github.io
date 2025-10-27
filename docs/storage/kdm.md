title: Katana Data Mover (kdm)

Also known as `kdm` or `kdm.restech.unsw.edu.au`.

If you need to copy, move, compress, or extract data **to or within** the Katana cluster, use the **Katana Data Mover (KDM)** server rather than the cluster head node. KDM is designed to handle many and/or very large transfers without impacting the login/head nodes.

You can log in to the KDM server using SSH just like you do with Katana:

```bash
ssh z1234567@kdm.restech.unsw.edu.au
```

Once connected you can use standard Linux commands (`cp`, `mv`, `rsync`, `tar`, etc.) to manage files on the cluster.

---

## Why use KDM?
- KDM is built for data transfers — it avoids overloading the head nodes.  
- It supports robust tools (rsync) and GUI clients (FileZilla, WinSCP) for large transfers.  
- For small files (e.g. a few job scripts <2–3 MB) you can copy directly to Katana, but please keep such direct copies minimal.

!!! note
    We require large or frequent data transfers to go through the KDM server. If you are only moving a couple of tiny text files, copying directly to Katana is acceptable but keep it small.

---

## Transfer Methods Overview

Choose a method depending on your local machine and preference:

- **Linux / macOS (command line):** `rsync` (recommended) or `scp`  
- **Windows:** WinSCP or FileZilla (GUI clients)  
- **All platforms:** SSH into `kdm.restech.unsw.edu.au` and use command-line tools

!!! warning
    Some antivirus vendors flag FileZilla as a potential risk. If that happens, try **WinSCP** (Windows) or use `rsync` from the command line instead.

---

## Using GUI Clients (FileZilla / WinSCP) — Recommended

<h3> FileZilla (quick steps) </h3>
1. Install FileZilla (or WinSCP on Windows).  
2. Open **Site Manager** in FileZilla and create a new site:  
   - Host: `kdm.restech.unsw.edu.au`  
   - Protocol: SFTP - SSH File Transfer Protocol  
   - Logon Type: Normal (use your zID and password)  
   - User: `z1234567`  
3. Connect — you should see the remote filesystem for your Katana account. Drag-and-drop to transfer files.

(See `../assets/filezilla.png` for a sample Site Manager screenshot and `../assets/filezillaquick.png` for Quick Connect.)

---

## Command-line Examples (rsync)

> **Important:** Use `rsync -avh` for readable progress and to preserve file attributes.  
> The trailing colon `:` after the username/host is crucial when copying *to* your home on KDM — it tells `rsync` that the target is a **remote directory**, not a local file.

---

<h3> Understanding Local and Remote Paths </h3>

Before using `rsync`, you need to correctly define your **local path** and **remote path**.

- **Local path:** the directory or file on your own computer.  
  Examples:  
  - `~/Documents/myproject/` (your project folder in your home directory)  
  - `/mnt/data/results/output.csv` (an absolute path on Linux or WSL)  
  - `C:\Users\Me\Desktop\file.txt` (an absolute path on Windows)

- **Remote path:** the location on the KDM server.  
  - Home directory → `z1234567@kdm.restech.unsw.edu.au:`  
  - Scratch directory → `z1234567@kdm.restech.unsw.edu.au:/srv/scratch/z1234567`

Make sure the directory exists before running `rsync`. If it doesn’t, you can create it on KDM with `mkdir -p /srv/scratch/z1234567/myfolder`.

---

<h3> From my computer to Katana Home </h3>

!!! Note
    When trying to copy files from your local machine to `kdm.restech.unsw.edu.au`, ensure using a new command line terminal (e.g., Command Prompt, PowerShell, Terminal, etc.) on your local machine. Do not run the rsync command from within an SSH session connected to kdm.restech.unsw.edu.au.


Copy your local directory `/path/to/my-directory` to your Katana **home** on KDM:

```bash
rsync -avh /path/to/my-directory z1234567@kdm.restech.unsw.edu.au:
```

The trailing `:` tells rsync to place files inside your remote **home directory** (e.g., `/home/z1234567`).

---

<h3> From my computer to Katana Scratch </h3>

Copy your local directory to your Katana **scratch** area:

```bash
rsync -avh /path/to/my-directory z1234567@kdm.restech.unsw.edu.au:/srv/scratch/z1234567
```

The explicit path ensures your files go to the **scratch** storage instead of your home directory.

---

<h3> From Katana (KDM) to my computer </h3>

If remote data is in your **home** on KDM:

```bash
rsync -avh z1234567@kdm.restech.unsw.edu.au:my-remote-results /home/me/
```

If remote data is in **scratch** on KDM:

```bash
rsync -avh z1234567@kdm.restech.unsw.edu.au:/srv/scratch/my-remote-results /home/me/
```

In these examples, `/home/me/` is the destination on your **local** computer.

---

## Tips for Large/Long Transfers

- **Keep rsync running** even if your SSH disconnects:  
  Use `tmux` or `screen` on KDM to protect long sessions.  

- **Resume interrupted transfers:**  
  Use `--partial` and `--progress` (or `-P`) so rsync doesn’t restart from scratch.  
  Example:

  ```bash
  rsync -avhP --partial /path/to/largefile z1234567@kdm.restech.unsw.edu.au:/srv/scratch/z1234567/
  ```

- **For very large datasets:**  
  Compress the folder before transfer to reduce small-file overhead:

  ```bash
  tar -czf mydata.tar.gz /path/to/my-large-folder
  rsync -avh mydata.tar.gz z1234567@kdm.restech.unsw.edu.au:/srv/scratch/z1234567/
  ```

  Then, extract it on KDM:

  ```bash
  tar -xzf mydata.tar.gz
  ```

---

## Mounting University-provided Storage on KDM
If your project has access to university staff storage, you may be able to mount or access that storage from KDM. See UNSW's staff storage documentation for details and eligibility. If you need help, contact your local IT support or Research Technology Services.

Useful links:
- FileZilla: https://filezilla-project.org/  
- WinSCP: https://winscp.net/eng/download.php  
- Staff storage overview: https://www.myit.unsw.edu.au/services/staff/storage-staff  
- FSAM staff storage: https://www.myit.unsw.edu.au/services/staff/storage/fsam-staff

---

## Quick Troubleshooting
- **Authentication fails:** check you are using your zID and zPass; if using keys, ensure your public key is in `~/.ssh/authorized_keys`.  
- **Slow transfers:** try using `-z` (compression) with rsync for compressible data; check network conditions.  
- **Permission errors:** ensure you have read/write permission on source and destination paths.  
- **Antivirus warnings with FileZilla:** try WinSCP or command line `rsync` instead.

---

