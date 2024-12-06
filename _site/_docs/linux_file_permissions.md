

# Linux file permissions 


# Chmod table 

**Example:**
    ```bash
    chmod 600 file.txt
    ```

* With chmod you can set file permissions using a numerical value or a combination of symbols which represents the 3 permissions which can be assigned to the user, users group , or the "others" group. 

Each of the permissions in the 3 digits you see above translate to the following 






# chmod Numerical Permission Settings

| Numerical Value | Symbolic Representation | Permissions for Owner | Permissions for Group | Permissions for Others | Example Usage                 |
|-----------------|-------------------------|-----------------------|-----------------------|------------------------|--------------------------------|
| 0               | ---                     | None                  | None                  | None                   | `chmod 000 file` (no access)   |
| 1               | --x                     | Execute               | None                  | None                   | `chmod 100 file`               |
| 2               | -w-                     | Write                 | None                  | None                   | `chmod 200 file`               |
| 3               | -wx                     | Write & Execute       | None                  | None                   | `chmod 300 file`               |
| 4               | r--                     | Read                  | None                  | None                   | `chmod 400 file`               |
| 5               | r-x                     | Read & Execute        | None                  | None                   | `chmod 500 file`               |
| 6               | rw-                     | Read & Write          | None                  | None                   | `chmod 600 file`               |
| 7               | rwx                     | Read, Write & Execute | None                  | None                   | `chmod 700 file` (full access for owner) |

## Common Full chmod Examples

| Command         | Numerical Value | Owner Permissions | Group Permissions | Other Permissions | Description                                      |
|-----------------|-----------------|-------------------|-------------------|-------------------|--------------------------------------------------|
| `chmod 644 file`| 644             | rw-               | r--               | r--               | Owner can read/write, others can read.           |
| `chmod 755 file`| 755             | rwx               | r-x               | r-x               | Owner has full access, others can read/execute.  |
| `chmod 700 file`| 700             | rwx               | ---               | ---               | Owner has full access, others have no access.    |
| `chmod 600 file`| 600             | rw-               | ---               | ---               | Owner can read/write, others have no access.     |
| `chmod 777 file`| 777             | rwx               | rwx               | rwx               | Everyone has full access (read, write, execute). |

## Explanation of Permissions

- **r**: Read
- **w**: Write
- **x**: Execute

The **numerical value** is represented as a combination of three numbers (e.g., `755`), each specifying the permissions for the **owner**, **group**, and **others**.

- The first digit is for **owner**.
- The second digit is for **group**.
- The third digit is for **others**.

### Numerical Values:
- **0**: No permissions
- **1**: Execute (`--x`)
- **2**: Write (`-w-`)
- **3**: Write & Execute (`-wx`)
- **4**: Read (`r--`)
- **5**: Read & Execute (`r-x`)
- **6**: Read & Write (`rw-`)
- **7**: Read, Write & Execute (`rwx`)
