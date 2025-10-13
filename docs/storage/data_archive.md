title: Data Archive
## How to Use the UNSW Data Archive

The [UNSW Data Archive](http://www.dataarchive.unsw.edu.au/) is the university's long-term research data storage platform. It provides free, safe, and secure storage for UNSW researchers - ensuring your data is preserved well beyond the end of a project.

To help researchers transfer data easily, the **Katana Data Mover (KDM)** includes a built-in script for copying files directly from Katana to the Data Archive.

!!! note
    To use the Data Archive, you must have access to it. This requires creating a [Research Data Management Plan (RDMP)](https://research.unsw.edu.au/research-data-management-unsw).

---

<h3> Learn More from the Data Archive Website </h3>

The official documentation provides comprehensive guidance on how to access and manage your data:

- [Web Application Guide](http://www.dataarchive.unsw.edu.au/help/web-application-guide)  
- [SFTP Client Guide](http://www.dataarchive.unsw.edu.au/help/sftp-client-guide)  
- [Command Line Script Guide](http://www.dataarchive.unsw.edu.au/help/command-line-script-guide)

---

## Checking the Available Script Versions

Log in to the Katana Data Mover server (`zID@kdm.restech.unsw.edu.au`) and run:

```bash
module avail unswdataarchive
```

To view help information for a specific version:

```bash
module help unswdataarchive/2021-02-17
```

---

## Initial Setup

Before transferring data, you must create a configuration file that stores your connection details.

1. **Load the module and create a default config file**

    ```bash
    [z1234567@kdm ~]$ module add unswdataarchive/2021-02-17
    [z1234567@kdm ~]$ get-config-file
    ```

    This command creates a file named `config.cfg` in your current directory, which contains the basic settings for connecting to the Data Archive.

2. **Generate an authentication token**

    Send an email to the [UNSW IT Service Centre](mailto:ITServiceCentre@unsw.edu.au) requesting a **Data Archive token**.  
    You'll need to specify your **Data Archive namespace**, which looks like one of the following:

    ```
    /UNSW_RDS/Dxxxxxx
    /UNSW_RDS/Hxxxxxx
    ```

    This namespace can be found in your Data Archive welcome email.

3. **Edit your configuration file**

    Open the `config.cfg` file and locate the line that begins with:

    ```
    token=
    ```

    Replace it with your generated token.

4. **(Optional) Use zID and zPass authentication**

    If you don't have a token yet, you can still upload files using your zID and zPass by adding this line to your `config.cfg`:

    ```bash
    user=z1234567
    ```

    When you start the upload, the system will prompt you for your zPass.

---

## Transferring Data

Once your configuration file is ready, you can start transferring data between Katana and the Data Archive.

<h3> Uploading Data to the Archive </h3>

Use the `upload.sh` command to send data **to** the archive:

```bash
upload.sh /path/to/your/local/directory /UNSW_RDS/D0000000/your/collection/name
```

This uploads the specified local directory to the given collection path in the Data Archive.

---

<h3> Downloading Data from the Archive </h3>

Use the `download.sh` command to retrieve data **from** the archive:

```bash
download.sh /UNSW_RDS/D0000000/your/collection/name /path/to/your/local/directory
```

This downloads the selected collection from the archive into your chosen local directory.

---

<h3> Summary </h3>

| Action | Command | Description |
|--------|----------|-------------|
| Upload data | `upload.sh [source] [destination]` | Send files from Katana to the Data Archive |
| Download data | `download.sh [source] [destination]` | Retrieve files from the Data Archive to Katana |
| Check available modules | `module avail unswdataarchive` | See available script versions |
| View module help | `module help unswdataarchive/[version]` | Get usage information for a module |
