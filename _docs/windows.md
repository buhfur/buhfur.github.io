
# Windows 10 notes **WORK IN PROGRESS**

## Table of Contents 

# Table of Contents

1. [Managing Files](#managing-files)
2. [Environment Variables](#environment-variables)
   - [Command Snippets](#command-snippets)
3. [Powershell Configuration](#powershell-configuration)
4. [Useful Cmdlets](#useful-cmdlets)
    - [System Cmdlets](#system-cmdlets)
    - [File and Directory Cmdlets](#file-and-directory-cmdlets)
    - [Networking Cmdlets](#networking-cmdlets)
    - [User and Group Cmdlets](#user-and-group-cmdlets)
    - [System Monitoring Cmdlets](#system-monitoring-cmdlets)
    - [Automation and Scripting Cmdlets](#automation-and-scripting-cmdlets)
    - [Security Cmdlets](#security-cmdlets)
    - [Windows Update Cmdlets](#windows-update-cmdlets)
    - [Help and Support Cmdlets](#help-and-support-cmdlets)
5. [Troubleshooting](#troubleshooting)
    - [Disabled Running Scripts](#disabled-running-scripts)

## Managing files  

- List 10 largest files for C:\ in megabytes
    ```bash
    Get-ChildItem c:\ -r -ErrorAction SilentlyContinue –Force |sort -descending -property length | select -first 10 name, DirectoryName, @{Name="MB";Expression={[Math]::round($_.length / 1MB, 2)}}
    ```


## Environment Variables 

The commands below are used to modify existing Environment Variables.

### Command Snippets 

- List Environment Variables 
    ```bash
    Get-Childitem env: 
    ```

- Permanantly append Variable to path 
    ```bash 
    setx MyVariable "MyPersistentValue" 
    ```

- Add new path to system Path 
    ```bash
    $newPath = "$env:Path;C:\NewPath"
    setx Path $newPath
    ```

- Remove temporary Environment Variable from path 
    ```bash
    Remove-Item env:MyVariable
    ```

- Remove persistent Environment Variable from path 
    ```bash
    setx MyVariable ""
    ```

- Check if Environment Variable exists in a powershell script 
    ```bash 
    if($env:MyVariable) {
        Write-Output "MyVariable exists with value $env:MyVariable"
    }else { 
        Write-Output "MyVariable does not exist"
    }
    ```

- Using an Environment Variable in a Script 
    ```bash 
    $env:MyVariable = "something"
    Write-Output "The value of MyVariable is : $env:MyVariable"
    ```
- Add script to PATH temporarily to Current Session  
    ```bash
    $env:PATH += ";C:\path\to\your\script"
    ```

- Add script to users persistent PATH  
    ```bash
    [System.Environment]::SetEnvironmentVariable("PATH", $env:PATH + ";C:\path\to\script", [System.EnvironmentVariableTarget]::User)
    ```

- List current path 
    ```bash 
    echo $env:path 
    ```

- Temporarily append Variable to path 
    ```bash
    $env:path += ";C:\Program Files\GnuWin32\bin" 
    ```

---

## Powershell configuration 

- Change Keybinds to Bash like keybinds 
    
    1. Check if `$PROFILE` variable exists 
        ```bash
        echo $PROFILE
        ```
    
    2. Open file in path listed in `echo $PROFILE`, in this case i'm using Vim but you can use any editor you like  
        ```bash
        vim $PROFILE
        ```

    3. Then , paste the following code into the file 
        ```bash
        # Load PSReadLine (should already be loaded in most cases)
        Import-Module PSReadLine

        # Map Bash-like key bindings
        Set-PSReadLineKeyHandler -Chord Ctrl+a -Function BeginningOfLine       # Move to start of line
        Set-PSReadLineKeyHandler -Chord Ctrl+e -Function EndOfLine             # Move to end of line
        Set-PSReadLineKeyHandler -Chord Ctrl+k -Function KillLine              # Delete text from cursor to end of line
        Set-PSReadLineKeyHandler -Chord Ctrl+u -Function BackwardDeleteLine    # Delete text from cursor to beginning of line
        Set-PSReadLineKeyHandler -Chord Ctrl+w -Function BackwardKillWord      # Delete previous word
        Set-PSReadLineKeyHandler -Chord Ctrl+y -Function Yank                  # Paste the last cut text
        Set-PSReadLineKeyHandler -Chord Ctrl+l -Function ClearScreen           # Clear screen
        ```

    4. Now, save the file and reload profile.
        ```bash
        . $PROFILE
        ```

        If you receive an error like below : 

        ```
        . : File C:\Users\username\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1 cannot be loaded because running scripts
        is disabled on this system. For more information, see about_Execution_Policies at https:/go.microsoft.com/fwlink/?LinkID=135170.
        At line:1 char:3
        ```

        - **Follow the instructions under "Troubleshooting" using the link here :** [ERROR:cannot be loaded because running scripts
        is disabled on this system.](#disabled-running-scripts)
        
        
    
> The $PROFILE variable is a variable that contains the path to your windows "profile".ps1 , it can be named anything 
> but this is where you would keep all your custom configurations. In my case the variable contains the path of : 
> `C:\Users\username\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1`


- Use bash like aliases in Powershell 

I am a fan of how bash allowsy you to customize your experience using the beloved ~/.bashrc file. For powershell it's the same process with a verbose syntax compared to bash. For this section I will put some examples below of alises you might want to add. The basic structure of an alias would look like the below

- Alias syntax template 
    ```bash
    Set-Alias -Name <name-of-alias> -Value "<value-of-alias>"
    ```

- Remove an alias 
    ```bash
    Remove-Item Alias:<alias-name>
    ```
-  Alias for Pre-installed Windows command  
    
Let's say we wanted to change the alias for Changing the execution policy for the current user. Open the file located in your $PROFILE variable ( check the above inline comments for further info on what this variable is ). 

    ```bash 
    Set-Alias -Name "Resolve-DnsName" -Value "dig"
    ```

- Create alias for non-built in script
    
    Powershell aliases were intended for running cmdlets, not custom commands. To get around this limitation, you may want to define functions for your custom scripts instead of using `Set-Alias` directly. Please see the below example for how I would do this using a python 3 script. 

    ```bash
    function tfind{
        python3 "C:\Users\buhfur\tfind\tfind.py"
    }

    ```

> Note : Aliases in powershell were intended for executing cmdlets and not executing scripts. To get around this you may need to define functions with the command used to execute your script using an alias. Please see the above example for how to do this.
---

# Useful cmdlets 

Below is a gathered list of all cmdlets installed by default on most powershell systems.

## System Cmdlets

- **`Get-Command`**: Gets all cmdlets, functions, workflows, aliases installed on your system.
- **`Get-Help`**: Displays help information for cmdlets and functions.
- **`Get-Process`**: Gets a list of all processes currently running on the local or remote machine.
- **`Stop-Process`**: Stops a running process by its process ID or process name.
- **`Get-Service`**: Gets the status of services on a local or remote machine.
- **`Start-Service`**: Starts a stopped service.
- **`Stop-Service`**: Stops a running service.

## File and Directory Cmdlets

- **`Get-ChildItem`**: Lists files and directories in a specified location.
- **`Set-Location`**: Sets the current working location to a specified path (similar to `cd`).
- **`Copy-Item`**: Copies an item from one location to another.
- **`Move-Item`**: Moves an item from one location to another.
- **`Remove-Item`**: Deletes files and directories.
- **`New-Item`**: Creates a new item (file, directory, etc.).
- **`Get-Content`**: Reads the content of a file.
- **`Set-Content`**: Writes content to a file, replacing existing content.
- **`Add-Content`**: Appends content to a file.

## Networking Cmdlets

- **`Test-Connection`**: Tests the network connection to a remote host (similar to `ping`).
- **`Resolve-DnsName`**: Performs a DNS query for a specified name.
- **`Invoke-WebRequest`**: Sends HTTP and HTTPS requests to web pages and retrieves content.
- **`Get-NetIPAddress`**: Gets the IP address configuration for network adapters.
- **`Get-NetAdapter`**: Gets the status of network adapters.

## User and Group Cmdlets

- **`Get-LocalUser`**: Gets local user accounts.
- **`Add-LocalUser`**: Creates a new local user account.
- **`Remove-LocalUser`**: Deletes a local user account.
- **`Get-LocalGroup`**: Gets local groups.
- **`Add-LocalGroupMember`**: Adds users to a local group.
- **`Remove-LocalGroupMember`**: Removes users from a local group.

## System Monitoring Cmdlets

- **`Get-EventLog`**: Retrieves events from the event log.
- **`Get-PerformanceCounter`**: Retrieves performance counter data.
- **`Measure-Object`**: Calculates properties of objects (e.g., count, sum).

## Automation and Scripting Cmdlets

- **`ForEach-Object`**: Processes each item in a collection with a specified script block.
- **`Where-Object`**: Filters objects based on specified criteria.
- **`Select-Object`**: Selects specific properties from an object.
- **`Sort-Object`**: Sorts objects by property values.
- **`Export-CSV`**: Exports data to a CSV file.
- **`Import-CSV`**: Imports data from a CSV file.

## Security Cmdlets

- **`Get-Acl`**: Gets the security descriptor for a specified item (file, folder, etc.).
- **`Set-Acl`**: Sets the security descriptor for a specified item.
- **`Get-Credential`**: Prompts for user credentials securely.

## Windows Update Cmdlets

- **`Get-WindowsUpdateLog`**: Retrieves Windows Update logs.
- **`Install-Module`**: Installs PowerShell modules from the PowerShell Gallery.

## Help and Support Cmdlets

- **`Get-Command`**: Lists all available commands, including cmdlets, functions, and aliases.
- **`Update-Help`**: Downloads and installs the newest help files for PowerShell modules.


---

## Troubleshooting 


### Disabled Running Scripts

This error indicates the user does not have the execution policy set for a specific user to run scripts inside their session. To allow the user to run the spcified command / script. Follow the instructions below, **make sure you're logged in as administrator**. If there are any scripts that can be used by the admin but not you, it's likely something to do with the execution policy that's been set. 


- Change Execution Policy to allow Local scripts
   ```bash
    Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
    ```

- Change Execution policy to allow user to run scripts from the internet 

    ```bash
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
- Bypass prompts for Internet Scripts
    ```bash
    Set-ExecutionPolicy Bypass -Scope CurrentUser
    ```
    

> The 'RemoteSigned' policy allows scripts created on the local machine to run , while blocking scripts from the internet unless they are signed by a trusted source. 
> The 'CurrentUser' scope only applies for the currently logged in user account , not the entire system
> Even when the Scope is set to 'Unrestricted' , you will still be prompted 
> 


---
