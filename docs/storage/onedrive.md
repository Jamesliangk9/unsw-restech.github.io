# OneDrive

You can mount Microsoft's OneDrive on Katana.

<h2> OneDrive Background </h2>

We don't generally recommend researchers mount their OneDrive because the configuration process is not ideal, and RClone integration with OneDrive has **limitations** that UNSW support cannot assist with.  
However, it is technically possible using the following method.

!!! note
    Please mount OneDrive only on **Katana Data Mover** (zID@kdm.restech.unsw.edu.au), not on the login nodes.

Mounting OneDrive locally will only work on the machine where the `mount` command is executed.  
The configuration part usually needs to be done once, whereas the "How to mount" step must be repeated each time you log in.

---

## Prerequisites

1. [Grant OneDrive consent](https://consenthelper.it.unsw.edu.au/consent?appId=c8800f43-7805-46c2-b8b2-1c55f3859a4c) to allow RClone access to your files.  
2. We cannot guarantee functionality for users in **Germany or China**, as OneDrive infrastructure there differs due to data regulations.  
3. Create an empty directory to use as the mount point, for example:  
   ```bash
   mkdir -p /home/z1234567/OneDrive
   ```

---

## Configure RClone for OneDrive

Log in to **KDM** and run:

```bash
[z1234567@kdm ~]$ rclone config
```

You will be guided through a setup wizard. The short answers are:

1. **n** (for new remote)  
2. Name: `OneDrive`  
3. Storage type: **Microsoft OneDrive** (option **26** in rclone v1.55.1)  
4. OAuth Client Id → *Press Enter for default*  
5. OAuth Client Secret → *Press Enter for default*  
6. Choose national cloud region → `"1. global"`  
7. Edit advanced config? → `"n"`  
8. Remote config? → `"n"`  
9. On a **local machine** (not KDM), run the command displayed, e.g.  
   ```bash
   rclone authorize "onedrive"
   ```  
   Copy the output token.  
10. Back in KDM, **paste the authorization token**.  
11. Choose `"1. OneDrive Personal or Business"`  
12. Choose drive `"0"`  
13. Confirm with `"Y"`  

Example output (press `y` to accept):

```bash
--------------------
[MS OneDrive]
type = onedrive
client_id = c8800f43-7805-46c2-b8b2-1c55f3859a4c
client_secret = SECRET
region = global
token = {"access_token":"eyJ0e...asdasd"}
drive_type = business
--------------------
y) Yes this is OK (default)
e) Edit this remote
d) Delete this remote
y/e/d>
```

---

## How to Mount OneDrive

Once logged in to **KDM Data Mover**, mount your drive with:

```bash
rclone mount OneDrive: /home/z1234567/OneDrive --daemon --vfs-cache-mode writes
```

This mounts your OneDrive folder in the background (`--daemon`) and ensures write caching (`--vfs-cache-mode writes`) for smooth performance.

!!! info
    Your OneDrive files will now appear under `/home/z1234567/OneDrive` (or your chosen mount point).

---

## Additional Notes

- To unmount, use:  
  ```bash
  fusermount -u /home/z1234567/OneDrive
  ```
- You will need to re-run the mount command each session (mounts are temporary).
- Visit the following resources for more details:  
  - [OneDrive](https://onedrive.live.com/)  
  - [Grant OneDrive Consent](https://consenthelper.it.unsw.edu.au/consent?appId=c8800f43-7805-46c2-b8b2-1c55f3859a4c)  
  - [RClone + OneDrive Limitations](https://rclone.org/onedrive/)
