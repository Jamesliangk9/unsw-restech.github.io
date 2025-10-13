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

## Using GUI Clients (FileZilla / WinSCP)

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

## Command-line Examples (rsync) — Recommended

> **Important:** Use `rsync -avh` for readable progress and preserving attributes. The trailing colon `:` after the username/host matters when copying *to* your home on KDM.

<h3> From my computer to Katana Home </h3>
Copy local directory `/path/to/my-directory` to your Katana **home** on KDM:

```bash
rsync -avh /path/to/my-directory z1234567@kdm.restech.unsw.edu.au:
```

The trailing `:` targets your remote **home** directory (e.g. `/home/z1234567`).

<h3> From my computer to Katana Scratch </h3>
Copy local directory to your Katana **scratch** area:

```bash
rsync -avh /path/to/my-directory z1234567@kdm.restech.unsw.edu.au:/srv/scratch/z1234567
```

<h3> From Katana (KDM) to my computer </h3>
If remote data is in your **home** on KDM:

```bash
rsync -avh z1234567@kdm.restech.unsw.edu.au:my-remote-results /home/me/
```

If remote data is in **scratch** on KDM:

```bash
rsync -avh z1234567@kdm.restech.unsw.edu.au:/srv/scratch/my-remote-results /home/me/
```

---

## Tips for Large/Long Transfers
- Use `tmux` or `screen` on KDM to keep long-running rsync sessions alive if your SSH client disconnects.  
- Use `--partial` and `--progress` (or `-P`) with rsync when transfers may be interrupted. Example:

```bash
rsync -avhP --partial /path/to/largefile z1234567@kdm.restech.unsw.edu.au:/srv/scratch/z1234567/
```

- If you have extremely large datasets, consider compressing them before transfer (`tar -czf`) and then extract on KDM to reduce the number of small file operations.

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

