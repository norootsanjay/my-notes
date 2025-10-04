# Boot Reboot and Shutdown a System Safely

Linux uses the systemctl command (short for "system control") to manage system states. 

>[!Root vs. Sudo]
If you are logged in as root, you do not need the sudo prefix for any of these commands.
Below, we present a summary of the fundamental commands for rebooting and shutting down your system.

## Rebooting the System

### As the Root User

When you are logged in as the root user, simply execute:

```bash
systemctl reboot
```

### As a Regular User with Elevated Privileges

If you are a regular user, use sudo to obtain temporary root privileges:

```bash
sudo systemctl reboot
[sudo] password for aaron:
```

## Shutting Down the System

To safely shut down the system, whether as root or using sudo, execute:

```bash
sudo systemctl power off
[sudo] password for aaron:
```

In certain cases, an unresponsive or misbehaving program may cause the system to refuse a normal reboot or shutdown. In such instances, you can force the operation by appending the --force flag. Use this option only when absolutely necessary.

```bash
sudo systemctl reboot --force
```

or

```bash
sudo systemctl power off --force
```

If a single force does not succeed, you ==may specify the force flag twice==. This method functions similar to pressing the physical reset button, immediately rebooting the system without allowing programs to close properly or save their data.

>[!Caution]
Forcing a reboot or shutdown can result in data loss. Use the force option only as a last resort.

## Scheduling Reboots and Shutdowns

In managed server environments, you might need to perform scheduled reboots or shutdowns without manual intervention, often during off-peak hours. The shutdown command is ideal for this purpose.

### Schedule a Shutdown at a Specific Time

Use the 24-hour format when specifying a shutdown time:

```bash
sudo shutdown 02:00
[sudo] password for aaron:
```

### Schedule a Shutdown after a Delay

To schedule a shutdown a certain number of minutes in the future, replace the time with a plus sign (+) followed by the delay in minutes. For example, to shut down after 15 minutes:

```bash
sudo shutdown +15
[sudo] password for aaron:
```

### Schedule a Reboot

To schedule a reboot, simply include the -r option. For instance:

```bash
# Schedule a reboot at 2 a.m.
sudo shutdown -r 02:00
[sudo] password for aaron:


# Schedule a reboot after 15 minutes
sudo shutdown -r +15
[sudo] password for aaron:
```

## Utilizing the Wall Message Feature

The shutdown command supports a wall message feature that notifies all logged-in users about an impending reboot or shutdown. This feature gives users time to save their work or prepare for disconnection.

For example, to schedule a reboot in one minute while displaying a message about a scheduled Linux kernel upgrade, use:

```bash
sudo shutdown -r +1 'Scheduled restart to upgrade our Linux kernel'
[sudo] password for aaron:
```

# Boot or Change System Into Different Operating Modes Optional

Currently, the Linux system ==boots to a graphical login screen==. At startup, the operating system loads various programs and services in a specific order using instructions *defined in systemd target* files. To see which boot target is active by default, run:

```bash
systemctl get-default
```

If the output is "graphical.target," it means the system is configured to boot into a full graphical environment. The corresponding graphical.target file contains the necessary instructions to start services and programs required for a graphical login.

Booting into the graphical target requires additional system resources due to the loading of the graphical user interface. 
- If you do not need the graphical environment, you can change the default boot target to a more resource-efficient option, such as the multi-user target. 
- This target provides essential services—like daemons and network services—running in a text-based mode. To switch the default boot target, execute:

```bash
sudo systemctl set-default multi-user.target
```

>[!Note]
Changing the default boot target means that on the next reboot, the system will operate in text mode rather than launching a graphical interface.

Below is an example session showing how to check and change the default boot target:

```bash
# Check the current default target
jeremy@kodekloud:~$ systemctl get-default
graphical.target


# Set the default target to multi-user
jeremy@kodekloud:~$ sudo systemctl set-default multi-user.target
[sudo] password for jeremy:
Removed "/etc/systemd/system/default.target".
Created symlink /etc/systemd/system/default.target → /lib/systemd/system/multi-user.target.
jeremy@kodekloud:~$
```

The multi-user target is named so because it supports simultaneous logins by multiple users, while still keeping network services active to ensure continuous connectivity.

After changing the default target, reboot the system. Instead of the usual graphical login screen, you will encounter a text-based login console, similar to the following:

```
Ubuntu 23.10 kodekloud tty1
kodekloud: jeremy
Password:
Welcome to Ubuntu 23.10 (GNU/Linux 6.5.0-27-generic x86_64)
 * Documentation: https://help.ubuntu.com
 * Management: https://landscape.canonical.com
 * Support: https://ubuntu.com/advantage


68 updates can be applied immediately.
To see these additional updates run: apt list --upgradeable


The list of available updates is more than a week old.
To check for new updates, run: sudo apt update
Last login: Tue May 28 12:25:21 PDT 2024 on tty1
jeremy@kodekloud:~$
```

If you temporarily need a graphical interface—perhaps to work with a 3D modeling application—you don't have to permanently switch the boot target. Instead, you can start the graphical environment immediately using:

```bash
sudo systemctl isolate graphical.target
```

This command activates the graphical interface on demand without altering the system's default text-based boot mode.

Additional systemd targets include emergency.target and rescue.target. The table below summarizes the most commonly used targets and their purposes:

| **Target**        | **Description**                                           | **Use Case**                                              | **Command Example**                          |
| ----------------- | --------------------------------------------------------- | --------------------------------------------------------- | -------------------------------------------- |
| graphical.target  | Boots into a full graphical desktop environment           | Standard desktop usage                                    | systemctl get-default                        |
| multi-user.target | Boots into a text-based environment with network services | Server or low-resource environments                       | sudo systemctl set-default multi-user.target |
| emergency.target  | Boots with minimal system services; root FS is read-only  | Critical troubleshooting when other services cause issues | (Invoked automatically when selected)        |
| rescue.target     | Loads essential services with a root shell access         | Administrative tasks in a minimal environment             | (Invoked automatically when selected)        |

# Use Scripting to Automate System Maintenance Tasks

- By utilizing bash scripting, you can log system information, archive important data, and streamline routine maintenance operations.

When you log into a Linux system, you are immediately presented with a command-line interface. After a successful login, the bash shell starts, providing a text-based environment. Bash interprets every command you type, which is why it is also known as a command interpreter or shell.

An interactive bash session typically looks like this:

```bash
[aaron@kodekcloud]$ date
Mon Dec  6 16:28:09 CST 2021
```
## Creating a Simple Script

Let's start by creating a basic script called script.sh. Although the ".sh" extension is optional, it helps identify the file as a script when browsing directories.

First, create and open the file for editing:

```bash
touch script.sh
vim script.sh
```

Begin your script with the shebang line, which must be the very first line in the file without any leading spaces:

```bash
#!/bin/bash
```

The shebang (#! followed by the interpreter's full path) specifies that bash should be used to execute the script.

Next, add a comment to document the purpose of the script. Comments start with a "#" and are ignored during execution:

```bash
#!/bin/bash
# Log the date and time when the script was executed
date >> /tmp/script.log
```

Now, include another command to log the Linux kernel version from the /proc/version file:

```
#!/bin/bash
# Log the date and time when the script was executed
date >> /tmp/script.log
# Append the current Linux kernel version to the log
cat /proc/version >> /tmp/script.log
```

After saving your changes (for example, by typing :wq in vim), make the script executable:

```bash
chmod +x script.sh
```

To run the script from the current directory, execute:

```bash
./script.sh
```

Verify the output by checking the log file:

```bash
$ cat /tmp/script.log
Linux version 5.15.0-94-generic (buildd@lcy02-amd64-096) (gcc …)
```

This script now logs both the date/time and the kernel version, which can be very helpful for maintaining system records. Later, you might consider scheduling this script to run automatically at regular intervals.

## Enhancing Scripts with Bash Built-ins

Bash includes a variety of built-in commands that can make your scripts more intelligent and efficient—these include conditional statements, loops, and more. To see a list of available built-in commands, simply execute:

```bash
help
```

This command displays built-ins such as if, test, alias, and others.

## Archiving Application Data

Next, let’s create a script that archives the contents of the /etc/apt directory. Even if the file does not exist, your editor (like vim) will create it once you save.

Open a new file for editing:

```bash
vim archive-apt.sh
```

Enter the following content to create a tar archive:

```bash
#!/bin/bash
tar acf /tmp/archive.tar.gz /etc/apt/
```

This script uses tar to create an archive at /tmp/archive.tar.gz that contains the entire /etc/apt directory. After making the script executable and running it, you can inspect the archive contents using:

```
tar -tf /tmp/archive.tar.gz
```

>[!Note]
Re-running this script will overwrite the existing archive. Adding backup logic can preserve previous archives.

## Archive Script with Backup Rotation

To avoid unintended data loss, create a script that implements backup rotation for the archive file:

```bash
vim archive-apt-2.sh
```

Add the following content:

```bash
#!/bin/bash
if test -f /tmp/archive.tar.gz; then
    mv /tmp/archive.tar.gz /tmp/archive.tar.gz.OLD
    tar acf /tmp/archive.tar.gz /etc/apt/
else
    tar acf /tmp/archive.tar.gz /etc/apt/
fi
```

This script checks if the file /tmp/archive.tar.gz exists. If it does, the script renames it to /tmp/archive.tar.gz.OLD before creating a new archive. Otherwise, it simply creates the archive.

Make the script executable and run it:

```bash
$ chmod +x archive-apt-2.sh
$ ./archive-apt-2.sh
$ ls /tmp
archive.tar.gz
archive.tar.gz.OLD
script.log
```

Now you have both the new archive and a backup available for reference.

## Understanding Exit Status Codes and Conditional Execution

In bash, every command returns an exit status. A ==zero value usually indicates success==, while a ==non-zero value signals an error or a condition== that was not met. For example, the grep command returns zero if a match is found and one if no match is found.

Consider the following script, which checks if the file /etc/default/grub contains the digit '5':

```bash
#!/bin/bash
if grep -q '5' /etc/default/grub; then
    echo 'Grub has a timeout of 5 seconds.'
else
    echo 'Grub DOES NOT have a timeout of 5 seconds.'
fi
```

Here, the -q option makes grep work in quiet mode, suppressing output and relying solely on its exit status for the conditional check.

## Reviewing Common Script Structures

Many essential system scripts are located in directories such as ==/etc, /cron.daily, /cron.weekly, and /cron.monthly==.

Below is an example shell script that checks whether anacron was run today and confirms if the system is running on battery power:

```bash
#!/bin/sh
# Check whether anacron was run today already
if test -r /var/spool/anacron/cron.daily; then
    day=`cat /var/spool/anacron/cron.daily`
fi
if [ `date +%Y%m%d` = "$day" ]; then
    exit 0
fi


# Do not run jobs when on battery power
online=1
for psupply in AC ADP0 ; do
    sysfile="/sys/class/power_supply/$psupply/online"
    if [ -f $sysfile ] ; then
        if [ `cat $sysfile 2>/dev/null` = 1 ]; then
            online=1
            break
        else
            online=0
        fi
    fi
done
if [ $online = 0 ] ; then
    exit 0
fi
```

This script exemplifies standard practices in shell scripting, including proper use of the shebang, commenting, and managing conditionals with if/else statements and loops.

---

# Manage Startup Process and Services In Services Configuration

Linux automatically launches several critical applications in a defined order. For example, if Application 2 depends on Application 1, Application 1 will load first. Additionally, if a critical application crashes, the system is configured to automatically restart it to ensure uninterrupted operation.

![The image illustrates a startup process flow, showing "Boot Up" leading to "App1" and "App2," with a note that "init" stands for the initialization system.](https://kodekloud.com/kk-media/image/upload/v1752881346/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-Manage-Startup-Process-and-Services-In-Services-Configuration/startup-process-flow-boot-apps.jpg)

## The Role of the Init System and Systemd Units

The startup and management of services in Linux are controlled by the ==init system==. 
This system uses configuration files called ==systemd units== to determine how applications should be started, what actions to take when an application fails, and other necessary operations. The term systemd refers both to the suite of tools that manage Linux systems and the primary program that acts as the init system.

![The image shows icons representing different system units: service, socket, device, and timer, with a note that "init" stands for the initialization system.](https://kodekloud.com/kk-media/image/upload/v1752881347/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-Manage-Startup-Process-and-Services-In-Services-Configuration/system-units-icons-init-diagram.jpg)

Systemd is a system and service manager for Linux OS. Systemd is the first process (PID 1) that runs when your Linus System boots.

Older init systems started services ==sequentially==, which could slow down boot times. Systemd improves this by starting services in ==parallel==, managing ==service dependencies==, and integrating ==logging== and ==monitoring== features.

>[!Features]
>1. Parallel service startup
>2. Service dependency management
>3. Integrated logging via journald
>4. Service monitoring & auto-restart
>5. Resource control

![[Pasted image 20251003030719.png]]

## Where Systemd fits in the Linux Boot Process

When you power on a Linux machine, a sequence of events happens before you ever see a login screen.
Systemd is a critical part of this chain.
![[Pasted image 20251003030915.png]]

- Systemd uses **unit files** to describe services, targets, devices, sockets, timers, and more. The most common type is the **service unit** (`.service`).

## Example: Managing the SSH Daemon

Many Linux servers run an SSH daemon to enable remote connections. Systemd manages this daemon using a specific service unit. You can display the SSH service unit file by executing:

```
$ systemctl cat ssh.service
```

The output might resemble the following:

```toml
# /lib/systemd/system/ssh.service
[Unit]
Description=OpenBSD Secure Shell server
Documentation=man:sshd(8) man:sshd_config(5)
After=network.target auditd.service
ConditionPathExists=!/etc/ssh/sshd_not_to_be_run


[Service]
EnvironmentFile=/etc/default/ssh
ExecStartPre=/usr/sbin/sshd -t
ExecStart=/usr/sbin/sshd -D $SSHD_OPTS
ExecReload=/usr/sbin/sshd -t
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
RestartPreventExitStatus=255
Type=notify
RuntimeDirectory=sshd
RuntimeDirectoryMode=0755
```

In this file:

- **ExecStart** specifies the command used to launch the SSH daemon.
- **ExecReload** defines the commands to reload the SSH configuration.
- **Restart=on-failure** ensures that systemd automatically restarts the service if it crashes.

If the SSH daemon fails, systemd will restart it to maintain remote connectivity.

## Checking Service Status

To verify the status of the SSH service, run:

```bash
sudo systemctl status ssh.service
```

A typical output may look like this:

```
● ssh.service - OpenBSD Secure Shell server
   Loaded: loaded (/etc/systemd/system/ssh.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2024-02-28 18:32:18 UTC; 2h 29min ago
     Docs: man:sshd(8)
           man:sshd_config(5)
 Main PID: 688 (sshd)
    Tasks: 1 (limit: 4558)
   Memory: 7.6M
      CPU: 88ms
   CGroup: /system.slice/ssh.service
           └─688 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"


Feb 28 18:32:18 kodekloud systemd[1]: Starting OpenBSD Secure Shell server...
Feb 28 18:32:18 kodekloud sshd[688]: Server listening on 0.0.0.0 port 22.
Feb 28 18:32:18 kodekloud sshd[688]: Server listening on :: port 22.
Feb 28 18:32:18 kodekloud systemd[1]: Started OpenBSD Secure Shell server.
```

The output indicates whether the service is enabled to start at boot, confirms that the process is running, and displays the process identifier (PID) along with log messages for troubleshooting.

## Starting, Stopping, Restarting, and Reloading Services

You can manually manage services using various systemctl commands:

- **Stop a service:**
```bash
sudo systemctl stop ssh.service
```
    
- **Start a service:**
```bash
sudo systemctl start ssh.service
```
    
- **Restart a service:**  
    This command stops and then starts the service to apply new configurations.
```bash
sudo systemctl restart ssh.service
```
    
- **Reload a service:**  
    This command reloads the service’s configuration without interrupting active sessions, which is particularly useful when users are connected.
```bash
sudo systemctl reload ssh.service
```

After modifying the SSH configuration file located at `/etc/ssh/sshd_config`, you can enforce the new settings with either of the following commands:

```bash
sudo systemctl restart ssh.service   # Fully stops and starts the service.
sudo systemctl reload ssh.service    # Gracefully reloads the configuration.
```

You can review the status again to confirm the service behavior:

```bash
$ systemctl status ssh.service
Feb 28 21:42:16 kodekloud systemd[1]: Stopped OpenBSD Secure Shell server.
Feb 28 21:42:16 kodekloud systemd[1]: Starting OpenBSD Secure Shell server...
Feb 28 21:42:48 kodekloud systemd[1]: Reloading OpenBSD Secure Shell server...
Feb 28 21:42:48 kodekloud sshd[2413]: Received SIGHUP; restarting.
Feb 28 21:42:48 kodekloud systemd[1]: Reloaded OpenBSD Secure Shell server.
```

>[!Note]
Not all applications support configuration reloads. When in doubt, systemd will first attempt a graceful reload and then perform a full restart if necessary.

## Enabling and Disabling Services

To prevent a service from starting automatically at boot, use the disable command:

```bash
sudo systemctl disable ssh.service
systemctl status ssh.service
# Output shows "disabled" in the Loaded line.
```

Verify the service's enablement status with:

```bash
systemctl is-enabled ssh.service
```

To enable the service for automatic startup at boot, run:

```bash
sudo systemctl enable ssh.service
```

If you want a daemon to start immediately and at boot, you can use the --now option:

```bash
sudo systemctl enable --now ssh.service
```

Likewise, to stop a service immediately and disable it for future boots, run:

```bash
sudo systemctl disable --now ssh.service
```

>[!Warning]
>Be cautious when disabling critical services such as SSH, particularly on remote systems.

## Masking Services

Some services might restart automatically even after being stopped or disabled because other services trigger them. In such cases, you can mask the service to completely prevent it from starting. For example, to prevent the at daemon from being activated:

```bash
sudo systemctl mask atd.service
```

A masked service cannot be enabled or started. Any attempt to do so will generate an error:

```
Failed to enable unit: Unit file /etc/systemd/system/atd.service is masked.
Failed to start atd.service: Unit atd.service is masked.
```

To reverse the masking and allow the service to operate normally, run:

```bash
sudo systemctl unmask atd.service
```

## Listing Service Units

Sometimes the service name for an installed application may not be obvious (for instance, Apache might be listed as apache.service or httpd.service depending on your distribution). To display all service units regardless of their state, use:

```bash
sudo systemctl list-units --type service --all
```

This command lists service units in various states—active, inactive, enabled, or disabled—ensuring you have a complete overview. An example output may look like:

| **UNIT**                   | **LOAD**  | **ACTIVE** | **SUB** | **DESCRIPTION**           |
| -------------------------- | --------- | ---------- | ------- | ------------------------- |
| accounts-daemon.service    | loaded    | active     | running | Accounts Service          |
| alsa-restore.service       | loaded    | inactive   | dead    | Save/Restore Sound Card   |
| alsa-state.service         | loaded    | active     | running | Manage Sound Card State   |
| apparmor.service           | not-found | inactive   | dead    | apparmor.service          |
| atd.service                | loaded    | active     | running | Job spooling tools        |
| auditd.service             | loaded    | active     | running | Security Auditing Service |
| auth-rpcgss-module.service | loaded    | inactive   | dead    | Kernel Module support     |

In addition to service units, systemd also manages other types of units such as sockets and timers.

---

# Create systemd Services

Since applications can occasionally crash, a mechanism is required to monitor and automatically restart them. Additionally, you may want the application to start automatically when the system boots. Systemd services offer these features and more by defining the instructions to manage your application's lifecycle.

## Building the Sample Application

Let's begin by creating a sample application. Open your preferred text editor with the following command:

```bash
jeremy@kodekloud:~$ sudo vim /usr/local/bin/myapp.sh
[sudo] password for jeremy:
```

Add the following content to the file. The first `echo` statement logs an informational message that "MyApp" has started, while the second statement logs an error after a five-second delay to simulate a crash:

```bash
#!/bin/sh
echo "MyApp Started" | systemd-cat -t MyApp -p info
sleep 5
echo "MyApp Crashed" | systemd-cat -t MyApp -p err
```

>[!Explanation]
• `systemd-cat` sends messages directly to the system log.  
• The `-t` option attaches a tag (MyApp) to every log message.  
• The `-p` option sets the logging priority (using `info` for normal status and `err` for error).  
• The `sleep 5` command simulates a delay of five seconds before logging a crash.

Save the file and make it executable:

```
jeremy@kodekloud:~$ sudo chmod +x /usr/local/bin/myapp.sh
```

## Understanding Systemd Service Options

Detailed explanations for service unit files and configuration options are available in the systemd manual pages. For example, you can learn more by using:

```
man systemd.service
```

![The image shows a manual page for "systemd.service," detailing the configuration of service unit files in systemd. It includes sections like NAME, SYNOPSIS, and DESCRIPTION, explaining how these files encode information about processes.](https://kodekloud.com/kk-media/image/upload/v1752881337/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-Create-systemd-Services/systemd-service-manual-page.jpg)

The manual explains useful configuration options. For instance, the `Restart`option specifies under which conditions systemd will restart your application. Even if your application always returns a zero exit code, configuring it to restart every time it exits can be beneficial. Additionally, the `RestartSec` option introduces a delay before restarting, which helps avoid creating a rapid restart loop that can overwhelm the system.

![The image shows a section of a manual page for `systemd.service`, detailing the configuration options for the `Restart=` setting, which controls when a service should be restarted based on various conditions and exit statuses.](https://kodekloud.com/kk-media/image/upload/v1752881338/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-Create-systemd-Services/systemd-service-restart-options.jpg)

Other parameters, such as `RestartSec`, `RestartSteps`, `RestartMaxDelaySec`, and `TimeoutStartSec`, offer fine-tuned control over the timing and behavior of service restarts in different scenarios.

![The image shows a manual page for systemd service configuration options, detailing parameters like `RestartSec`, `RestartSteps`, `RestartMaxDelaySec`, and `TimeoutStartSec`. These settings control service restart intervals and timeouts.](https://kodekloud.com/kk-media/image/upload/v1752881340/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-Create-systemd-Services/systemd-service-configuration-manual.jpg)

## Creating a Service Template

Instead of starting from scratch, you can use an existing service file as a template. Pre-existing service files are typically stored in the `/lib/systemd/system`directory. List the contents of this directory with:

```bash
ls /lib/systemd/system
```

As a starting point, copy the SSH service file to create your application service file:

```bash
sudo cp /lib/systemd/system/ssh.service /etc/systemd/system/myapp.service
```

After copying, open the file for editing. A standard service file is divided into three sections: `[Unit]`, `[Service]`, and `[Install]`.

### Modifying the Unit Section

In the `[Unit]` section, update the description to better reflect your application. Additionally, adjust dependencies so the service starts only after critical services like networking and audit are available:

```
[Unit]
Description=My Application
After=network.target auditd.service
```

>[!Tip]
Some examples might include conditions like `ConditionPathExists=!/etc/ssh/sshd_not_to_be_run`. This condition prevents the service from starting if the file exists. For our simple application, you can omit such conditions.

### Configuring the Service Section

In the `[Service]` section, define how systemd should manage your application's lifecycle. Remove any settings that are irrelevant to a simple script, and insert a pre-start command that logs when systemd is about to start MyApp. Then specify your application script as the start command:

```
[Service]
ExecStartPre=/bin/echo "Systemd is preparing to start MyApp"
ExecStart=/usr/local/bin/myapp.sh
KillMode=process
Restart=always
RestartSec=1
Type=simple
```

Key configuration options include:

• `KillMode=process` instructs systemd to terminate only the main process.  
• `Restart=always` ensures that the service is restarted regardless of its exit status.  
• `RestartSec=1` sets a one-second delay before each restart.  
• `Type=simple` is appropriate since the application does not send a readiness notification to systemd.

### Setting Up the Install Section

The `[Install]` section controls how the service integrates with the system startup process. Typically, you would include the service in the `multi-user.target` to ensure it runs in a multi-user environment:

```
[Install]
WantedBy=multi-user.target
```

Your final service unit file should resemble the following:

```
[Unit]
Description=My Application
After=network.target auditd.service


[Service]
ExecStartPre=/bin/echo "Systemd is preparing to start MyApp"
ExecStart=/usr/local/bin/myapp.sh
KillMode=process
Restart=always
RestartSec=1
Type=simple


[Install]
WantedBy=multi-user.target
```

## Activating the Service

After saving your service file in `/etc/systemd/system/myapp.service`, reload the systemd daemon so that it recognizes your new service:

```bash
sudo systemctl daemon-reload
```

Next, start the service with the following command:

```bash
sudo systemctl start myapp.service
```

## Monitoring the Service Logs

To view your service's logs in real time, use the journalctl command with the follow option:

```bash
sudo journalctl -f
```

A typical log output might look like this:

```
May 22 16:46:55 kodekloud MyApp[1838]: MyApp Started
May 22 16:47:00 kodekloud MyApp[1842]: MyApp Crashed
May 22 16:47:00 kodekloud systemd[1]: myapp.service: Deactivated successfully.
May 22 16:47:00 kodekloud systemd[1]: myapp.service: Scheduled restart job, restart counter is at 4.
May 22 16:47:01 kodekloud systemd[1]: Starting myapp.service - My Application...
May 22 16:47:03 kodekloud systemd[1]: Started myapp.service - My Application.
May 22 16:47:04 kodekloud sudo[1852]:     jeremy : TTY=pts/0 ; PWD=/home/jeremy ; USER=root ; COMMAND=/usr/local/bin/myapp.sh
May 22 16:47:07 kodekloud MyApp[1856]: MyApp Crashed
May 22 16:47:07 kodekloud systemd[1]: myapp.service: Deactivated successfully.
May 22 16:47:07 kodekloud systemd[1]: myapp.service: Scheduled restart job, restart counter is at 5.
May 22 16:47:08 kodekloud systemd[1]: Starting myapp.service - My Application...
May 22 16:47:08 kodekloud systemd[1]: Started myapp.service - My Application.
May 22 16:47:08 kodekloud MyApp[1863]: MyApp Started
```

When you have finished reviewing the logs, press Ctrl-C to exit.

## Further Exploration

To learn more about the systemd options we used in this lesson, consult the following manual pages:

```bash
man systemd.service
man systemd.unit
man systemd.exec
man systemd.kill
```

Additionally, you can explore existing service files in the `/lib/systemd/system` directory to better understand various service configurations:

```bash
ls /lib/systemd/system
```

This command will list several services and target files that showcase different configurations used in practice.

---

# Diagnose and Manage Process

>[!Note]
>Every command you execute creates a process that runs until it completes its task or is manually terminated.

For instance, running a command like 'ls' creates a short-lived process that displays the directory contents and then exits, whereas services like the SSH daemon run continuously in the background.

## Using the ps command

- [f] The ps command is used to inspect processes.

ps -a: Unix style
ps a: BSD style

- [I] **ps -a ≠ ps a**

Running `ps` without any options only displays processes associated with the current terminal session. To see all processes running on the system, combine the options `a`, `x`, and `u`:

```bash
ps aux
```

In this command:

- The `ax` options ensure that processes from all controlling terminals are listed.
- The `u` option presents a user-oriented format, adding columns for memory and CPU usage as well as the process owner. This is why `ps aux` is widely used for obtaining a complete snapshot of system processes.
![[Screenshot 2025-10-01 at 19.08.45.png]]
### Analyzing the ps aux Output

Review the typical columns shown in the `ps aux` output:

| **Column** | **Description**                                                                                                                 |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------- |
| *%CPU*     | Percentage of one CPU core’s capacity used by the process. A value of 150 may indicate one full core plus 50% of a second core. |
| *%MEM*     | Percentage of the system's total memory used by the process.                                                                    |
| *START*    | The time or date when the process was initiated.                                                                                |
| *TIME*     | Total CPU time consumed by the process. Processes with long lifespans often spend the majority of their time sleeping.          |
| *COMMAND*  | The command that initiated the process, along with its parameters.                                                              |
Processes displayed within square brackets (e.g., `[kthreadd]`) are kernel processes operating in privileged areas and usually do not require user interaction. Only processes outside these brackets represent user-space applications.

## Monitoring Processes in Real Time with top

While `ps` gives you a static snapshot, the `top` command is ideal for real-time process monitoring. It continuously updates its display and reorders the processes by CPU usage, making it easier to identify resource-intensive processes.

Use the arrow keys to navigate the list, and press "Q" to exit.

---

# Locate and Analyze System Log Files

In this guide, you will learn how to locate and analyze system log files on a Linux system. Logging is a critical part of managing any Linux server since logs offer detailed insights into system activities—covering events such as user actions, system errors, and service operations. These logs are written as text messages, making them easy to search, read, and troubleshoot.

## Logging Basics

The Linux kernel and many applications generate status messages, errors, and warnings, which are stored in log files. Because multiple programs continuously generate these messages, logging daemons are used to collect and organize information into centralized log files. The most common logging daemon on Linux is rsyslog (Rocket Fast System for Log Processing). By default, rsyslog stores log files in the `/var/log` directory. These plain text files can be easily explored using text-search tools like `grep`.

Below is an example of listing the files in the `/var/log` directory:

```bash
$ ls /var/log/
alternatives.log     landscape           nginx
apt                  lastlog             private
auth.log             dmesg.0             syslog
auth.log.1           dmesg.1.gz         syslog.1
auth.log.2.gz        dmesg.2.gz         syslog.2.gz
bootstrap.log        dpkg.log           ubuntu-advantage.log
btmp                 faillog            ubuntu-advantage.log.1
btmp.1               installer          unattended-upgrades
cloud-init.log       journal             wtmp
cloud-init-output.log kern.log           kern.log.1
dist-upgrade         kern.log.2.gz      dmesg
```

>[!Note]
Most log files under `/var/log` are restricted to the root user. If you need to view these logs as a non-root user, consider switching to root using commands like `su --login` or `sudo --login`.

For example:

```
$ su --login
Password:
```

or

```
$ sudo --login
[sudo] password for aaron:
```

## Identifying Specific Log Files

If you are unsure where specific logs are stored—such as SSH logs that give details about login attempts—you can search all log files for entries related to the SSH daemon (`sshd`):

```bash
grep -r 'ssh' /var/log/
```

The output might look like this:

```
/var/log/auth.log:Mar  3 03:32:37 kodekloud sshd[1653]: Connection closed by authenticating user aaron 10.11.12.1 port 57660
/var/log/auth.log:Mar  3 03:32:39 kodekloud sshd[1655]: Accepted password for aaron from 10.11.12.1 port 52560 ssh2
/var/log/auth.log:Mar  3 03:32:39 kodekloud sshd[1655]: pam_unix(sshd:session): session opened for user aaron(uid=1000) by
grep: /var/log/private: Permission denied
/var/log/installer/installer-journal.txt:Jun 30 12:18:56 ubuntu-server sshd[1409]: Server listening on 0.0.0.0 port 22.
...
```

This indicates that SSH logs are primarily found in `/var/log/auth.log`. You can open this file using an editor like Vim or a pager such as `less` to search for additional SSH-specific details.

Example output from `/var/log/auth.log`:

```
$ less /var/log/auth.log
Mar  3 03:21:24 kodekloud sshd[1501]: Accepted password for aaron from 10.11.12.1 port 56862 ssh2
Mar  3 03:32:34 kodekloud sshd[1653]: Failed password for aaron from 10.11.12.1 port 57660 ssh2
Mar  3 03:32:53 kodekloud sudo:     aaron : TTY=pts/0 ; PWD=/home/aaron; USER=root ; COMMAND=/usr/bin/apt update
Mar  3 03:37:30 kodekloud passwd[2129]: pam_unix(passwd:chauthtok): password changed for aaron
```

Another key log file is `/var/log/syslog`, which includes general system messages:

```
$ less /var/log/syslog
Mar  3 00:00:14 kodekloud systemd[1]: Finished Daily dpkg database backup service.
Mar  3 00:10:14 kodekloud rsyslogd: [origin software="rsyslogd" swVersion="8.2112.0" x-pid="638" x-info="https://www.rsyslog.com"] rsyslog was HUPed
Mar  3 00:17:01 kodekloud CRON[1357]: (root) CMD   (cd / && run-parts --report /etc/cron.hourly)
Mar  3 00:18:52 kodekloud systemd-timesyncd[521]: Network configuration changed, trying to establish connection.
Mar  3 00:33:14 kodekloud systemd[1]: Starting Refresh fwupd metadata and update motd...
```

Older log files often have suffixes such as `.1` or are compressed with `.gz`, while the uncompressed file (for example, `auth.log`) contains the latest entries.

## Monitoring Logs in Real Time

When you need to debug an application or monitor system changes as they happen, you can use the `tail` command with the `-f` option to follow a log file in real time. For example:

```
$ tail -F /var/log/auth.log
Mar  3 03:32:53 kodekloud sudo:     aaron : TTY=pts/0 ; PWD=/home/aaron ; USER=root ; COMMAND=/usr/bin/apt update
Mar  3 03:32:53 kodekloud sudo: pam_unix(sudo:session): session opened for user root (uid=0) by aaron(uid=1000)
Mar  3 03:32:58 kodekloud sudo: pam_unix(sudo:session): session closed for user root
Mar  3 03:37:30 kodekloud passwd[2129]: pam_unix(passwd:chauthtok): password changed for aaron
...
```

Press `Ctrl+C` to exit the follow mode.

To filter the live output for specific entries, such as those related to `sudo`, you can pipe the output through `grep`:

```
$ tail -F /var/log/auth.log | grep "sudo"
```

## Advanced Log Analysis Using journalctl

Modern Linux systems that use systemd employ the journal daemon for structured log management. The `journalctl` command provides powerful options to filter and analyze logs.

### Viewing Logs by Command

To view logs for a specific command, such as `sudo`, first determine its full path:

```
$ which sudo
/usr/bin/sudo
```

Then filter the journal logs associated with it:

```
$ journalctl /usr/bin/sudo
```

This command opens the log output in your default pager (typically `less`), allowing you to navigate and search through the logs. Press `q` to exit the pager.

### Viewing All Journal Logs

To view all entries collected by the journal daemon, run:

```
$ journalctl
```

Jump to the end of the log output by pressing `>` within the pager, or run:

```
$ journalctl -e
```

### Following Journal Logs Live

Just like `tail -f`, you can follow journal logs in real time by using:

```
$ journalctl -f
Mar 03 23:24:43 kodekloud sudo[1077]: pam_unix(sudo:session): session closed for user root
Mar 03 23:28:07 kodekloud systemd[1]: Starting Cleanup of Temporary Directories...
...
```

Press `Ctrl+C` to exit follow mode.

### Filtering Logs by Priority

Use the `-p` option with `journalctl` to filter log output based on priority levels. Available priorities include: emerg, alert, crit, err, warning, notice, info, and debug. For example, to display only error messages:

```
$ journalctl -p err
Feb 08 21:09:19 kodekloud systemd[1]: multipathd.socket: Socket service already active, refusing.
Feb 08 21:09:19 kodekloud systemd[1]: Failed to listen on multipathd control socket.
-- Boot 35a9a34be95e43cb85c097ecdd0afa4d --
Mar 03 00:33:14 kodekloud systemd[1]: Failed to start Refresh fwupd metadata and update motd.
```

Tip

If you forget the priority names, type `journalctl -p`  (with a trailing space) and press Tab twice to list all available options.

### Combining Filters with Regex

You can combine filters with regular expressions. For example, to display only log entries with `info` priority where messages start with the letter “b”, use:

```
$ journalctl -p info -g '^b'
Jun 30 15:34:11 kodekloud kernel: Booting paravirtualized kernel on KVM
Jun 30 15:34:11 kodekloud kernel: Built 1 zonelists, mobility grouping on.  Total pages: 1031896
Jun 30 15:34:11 kodekloud kernel: Block layer SCSI generic (bsg) driver version 0.4 loaded (major 243)
Jun 30 15:34:11 kodekloud kernel: blacklist: Loading compiled-in revocation X.509 certificates
Jun 30 15:34:11 kodekloud kernel: btrfs loaded, crc32c=crc32c-intel, zoned=yes, fsverity=yes
```

### Filtering Logs by Time Range

To filter journal logs by time, use the `-S` (since) and `-U` (until) options. For instance, to view logs between 1 a.m. and 2 a.m.:

```
$ journalctl -S 01:00 -U 02:00
Mar 04 01:17:01 kodekloud CRON[1417]: pam_unix(cron:session): session opened for user root(uid=0) by (uid=0)
Mar 04 01:17:01 kodekloud CRON[1418]: (root) CMD (cd / && run-parts --report /etc/cron.hourly)
Mar 04 01:17:01 kodekloud CRON[1417]: pam_unix(cron:session): session closed for user root
Mar 04 01:35:24 kodekloud systemd[1]: Starting Discard unused blocks on filesystems from /etc/fstab...
```

For full date and time filtering, enclose the datetime string in single quotes:

```
$ journalctl -S '2024-03-03 01:00:30'
Mar 03 01:13:06 kodekloud systemd-timesyncd[521]: Network configuration changed, trying to establish connection.
Mar 03 01:13:06 kodekloud systemd-timesyncd[521]: Initial synchronization to time server 185.125.190.56:123 (ntp.ubuntu.com).
...
```

### Viewing Logs by Boot Session

The journal organizes log entries by boot session. To view logs from the current boot, run:

```
$ journalctl -b 0
Mar 03 23:12:59 kodekLOUD kernel: Linux version 5.15.0-97-generic (buildd@lcy02-amd64-033) ...
Mar 03 23:12:59 kodekLOUD kernel: Command line: BOOT_IMAGE=/vmlinuz-5.15.0-97-generic root=...
...
```

For previous boot sessions, use a negative offset (e.g., `-b -2` for two boots ago). Note that some systems might only store logs in memory, so persistent logging may not be available unless enabled. To enable persistent logging, create the directory:

```
$ sudo mkdir /var/log/journal/
```

If you run a journalctl command and see no output, try using `sudo` or check that your user has the appropriate permissions.

## Viewing User Login History

Reviewing user login history is straightforward with the `last` command, which displays recent logins in reverse chronological order:

```
$ last
aaron     pts/0        10.11.12.1    Sun Mar  3 23:15 - 23:15  still logged in
reboot    system boot  5.15.0-97-generic  Sun Mar  3 23:12 - 23:12  still running
aaron     tty1         Sun Mar  3 04:14 - 04:22  (00:08)
...
```

The `lastlog` command provides a summary of the last login times for each user and can include the originating IP address for remote logins (such as SSH):

```
$ lastlog
Username      Port     From             Latest
root          pts/0    **Never logged in**
daemon        pts/0    **Never logged in**
bin           pts/0    **Never logged in**
sys           pts/0    **Never logged in**
tss           pts/0    **Never logged in**
landscape     pts/0    **Never logged in**
fwupd-refresh pts/0    **Never logged in**
usbmux       pts/0    **Never logged in**
aaron        pts/0    10.11.12.1      Sun Mar  3 23:15:19 +0200 2024
lxd          pts/0    **Never logged in**
```

---

# Schedule Tasks to Run at a Set Date and Time

In this article, we explore how to schedule tasks to run at specified times on Linux systems. Automating tasks such as database backups every Sunday at 3:00 AM is crucial for consistent system maintenance. There are three primary tools available for task scheduling:

1. Cron Utility
2. Anacron
3. At Utility

Below, we detail how each tool works and how to configure them for your needs.

---

## Cron Utility

Cron is best suited for repetitive tasks that run at regular intervals—whether every few minutes, specific hours, days, or even months.

The basic syntax for a cron job consists of five time-and-date fields followed by the command to be executed. When editing the system-wide cron table (found at `/etc/crontab`), a username field is included. The time fields are as follows:

- Minute (0–59)
- Hour (0–23)
- Day of the month (1–31)
- Month (1–12)
- Day of the week (0–6, where 0 or 7 denotes Sunday)

You can use special characters in these fields:

- Asterisk (\*) denotes every possible value.
- Comma (,) separates multiple values. For example, "15,45" in the minute field runs the job at minute 15 and 45.
- Dash (-) specifies a range (e.g., "2-4" in the hour field).
- Slash (/) defines steps. For example, "\*/4" in the hour field indicates every 4 hours, and "0-8/4" represents 0 AM, 4 AM, and 8 AM.

The default system-wide cron table, located at `/etc/crontab`, usually includes explanatory comments. Below is a sample excerpt:

![The image illustrates a server setup for automated tasks, specifically a database backup every Sunday at 3:00 AM, using Cron, Anacron, and "at" for scheduling.](https://kodekloud.com/kk-media/image/upload/v1752881353/notes-assets/images/Linux-Foundation-Certified-System-Administrator-LFCS-Schedule-Tasks-to-Run-at-a-Set-Date-and-Time/server-setup-automated-backup-schedule.jpg)

```
$ cat /etc/crontab
SHELL=/bin/sh
# You can also override PATH; by default, newer versions inherit it.
# Example of job definition:
# ----------- minute (0 - 59)
# ----------- hour (0 - 23)
# ----------- day of month (1 - 31)
# ----------- month (1 - 12) OR jan,feb,mar,apr ...
# ----------- day of week (0 - 6) (Sunday=0 or 7) OR
#               sun,mon,tue,wed,thu,fri,sat
# * * * * * user-name command to be executed
35 6 * * * root /bin/some_command --some_options
```

Note

When modifying the system-wide cron file, be aware that package updates may overwrite your changes. It is generally safer to add cron jobs using the user's personal cron table.

To edit your personal cron table, run:

```
$ crontab -e
```

In your personal cron table, the username is not required because the job inherits your current privileges. For example, if you want to create a file named `test_path`every day at 6:35 AM using the `touch` command, first determine its full path:

```
$ which touch
/usr/bin/touch
```

Then, add the following line using `crontab -e`:

```
35 6 * * * /usr/bin/touch test_path
```

Save and exit the editor to schedule the job.

Below are additional examples for scheduling tasks:

```
$ which touch
/usr/bin/touch


$ crontab -e
35 6 * * * /usr/bin/touch test_passed            # Every day at 6:35 AM
0 3 * * 0 /usr/bin/touch test_passed              # Every Sunday at 3:00 AM
0 3 * * 7 /usr/bin/touch test_passed              # Alternative notation for Sunday
0 3 15 * * /usr/bin/touch test_passed             # On the 15th of every month at 3:00 AM
```

To run a command every day at 3:00 AM or at the top of every hour (when the minute is 00), adjust the timing fields as needed.

For further testing of your cron expressions, visit [crontab.guru](http://www.crontab.guru/).

To list your current cron jobs, use:

```
$ crontab -l
35 6 * * * /usr/bin/touch aaron_test
```

To view or edit other users' cron jobs (e.g., root or another user), use `sudo`:

```
$ sudo crontab -l
0 * * * * /usr/bin/touch root_test


$ sudo crontab -e -u jane
30 * * * * /usr/bin/touch jane_test
```

If you need to remove a user's cron jobs entirely, use the `-r` option:

```
$ crontab -r
$ sudo crontab -r -u jane
```

An alternative to adding jobs to the cron table directly is by placing scripts into specific directories, such as `/etc/cron.daily`, `/etc/cron.hourly`, `/etc/cron.weekly`, or `/etc/cron.monthly`. For instance, to schedule a shell script named `shellscript` to run hourly:

```
$ touch shellscript
$ sudo cp shellscript /etc/cron.hourly/
```

Make sure the script is both readable and executable. To remove it, simply delete the file from the directory.

---

## Anacron

Anacron is designed for tasks that need to run periodically (daily, weekly, monthly) on systems that are not continuously powered on. If a scheduled cron task is missed because the system is off, anacron will run the task once the system is available.

On many systems, anacron is pre-installed. If not, install it with:

```
$ sudo apt install anacron
```

Anacron's configuration is in the `/etc/anacrontab` file, which includes helpful comments and examples. Here’s an excerpt:

```
$ sudo vim /etc/anacrontab
# See anacron(8) and anacrontab(5) for details.
# These entries replace cron's job entries.
1       5       cron.daily      run-parts --report /etc/cron.daily
7       10      cron.weekly     run-parts --report /etc/cron.weekly
@monthly 15      cron.monthly    run-parts --report /etc/cron.monthly
```

The syntax in the anacrontab file consists of four fields:

1. The period in days (e.g., 1 for daily, 7 for weekly).
2. A delay (in minutes) after system startup.
3. A unique job identifier.
4. The command to execute.

For example, to run a job every three days with a 10-minute delay:

```
3 10 test_job /usr/bin/touch /root/anacron_created_this
```

Other examples include:

```
7 10 test_job /usr/bin/touch /root/anacron_created_this
@monthly 10 test_job /usr/bin/touch /root/anacron_created_this
```

After editing, verify your anacrontab syntax with:

```
$ anacron -T
anacron: /etc/anacrontab: Unknown named period on line 13, skipping
```

If no errors are reported, your anacron entries are correctly formatted.

Documentation Tip

For more detailed information on anacron, refer to the man pages by running:

```
$ man 5 anacrontab
```

---

## At Utility

The at utility is ideal for one-time task scheduling, rather than recurring jobs. If at is not already installed on your Ubuntu system, install it with:

```
$ sudo apt install at
```

To schedule a job with at, specify the desired run time. For example, to run a job at 3:00 PM:

```
$ at '15:00'
warning: commands will be executed using /bin/sh
at> /usr/bin/touch file_created_by_at
at> <CTRL-D>
```

You can also schedule jobs for specific dates or relative times. Some examples include:

```
$ at 'August 20 2024'
$ at '2:30 August 20 2024'
$ at 'now + 30 minutes'
$ at 'now + 3 hours'
$ at 'now + 3 days'
$ at 'now + 3 weeks'
$ at 'now + 3 months'
```

To view scheduled at jobs, use:

```
$ atq
1 Wed Mar  6 15:00:00 2024 a aaron
```

Here, the first number is the job ID. To inspect a job's details, use the `-c` option, similar to using `cat`. To remove a job, execute:

```
$ atrm 1
```

Quick Reference

For more information on the at utility, refer to the [at documentation](https://linux.die.net/man/1/at).

---

Each of these tools—cron, anacron, and at—has its unique strengths. Cron excels for regular, recurring tasks; anacron ensures tasks are completed even if scheduled times are missed due to system downtime; and at is perfect for one-off commands.

---

# Manage Software with the Package Manager

This article explains how to install, update, upgrade, and remove software on Ubuntu using the apt package manager. Ubuntu simplifies software management by handling installation, upgrades, and dependency resolution with apt. Follow the examples below for practical guidance on managing your system's packages.

## Updating the Package Database

Before installing or upgrading any software, it is essential to refresh the local package database. Running the following command downloads the latest package information from the official repositories, ensuring that you work with up-to-date data.

```
sudo apt update
```

Example output:

```
sudo apt update
[sudo] password for jeremy:
Get:1 http://us.archive.ubuntu.com/ubuntu noble InRelease [97.5 kB]
Get:2 http://us.archive.ubuntu.com/ubuntu noble-updates InRelease [89.7 kB]
Get:3 http://us.archive.ubuntu.com/ubuntu noble-backports InRelease [89.7 kB]
Get:4 http://security.ubuntu.com/ubuntu noble-security InRelease [89.7 kB]
Fetched 179 kB in 2s (77.4 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
4 packages can be upgraded. Run 'apt list --upgradable' to see them.
jeremy@kodekloud:~$
```

Note

If the package information was last refreshed a while ago (for example, one week ago), running `sudo apt update` ensures you get the latest package listings.

## Upgrading Installed Packages

After updating the package database, you can upgrade the installed packages with:

```
sudo apt upgrade
```

When prompted, type "y" to confirm the upgrade. For added efficiency, you can chain the update and upgrade commands. Using the `&&` operator ensures that the upgrade command runs only if the update is successful:

```
sudo apt update && sudo apt upgrade
```

## Installing New Applications

To install a new application such as Nginx, run:

```
sudo apt install nginx
```

For a smoother process that uses the latest package data, chain the update and install commands together:

```
sudo apt update && sudo apt install nginx
```

During the installation, additional dependencies, including necessary libraries and components, may be automatically installed to ensure Nginx runs correctly.

Example output when installing Nginx:

```
sudo apt update && sudo apt install nginx
Hit:1 http://us.archive.ubuntu.com/ubuntu noble InRelease
Hit:2 http://security.ubuntu.com/ubuntu noble-security InRelease
Hit:3 http://us.archive.ubuntu.com/ubuntu noble-updates InRelease
Hit:4 http://us.archive.ubuntu.com/ubuntu noble-backports InRelease
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  nginx-common
Suggested packages:
  fcgiwrap nginx-doc ssl-cert
The following NEW packages will be installed:
  nginx nginx-common
0 upgraded, 2 newly installed, 0 to remove and 0 not upgraded.
Need to get 552 kB of archives.
After this operation, 1,596 kB of additional disk space will be used.
Do you want to continue? [Y/n]
```

## Understanding Packages

A package is an archive containing everything required by a piece of software, including binaries, configuration files, and documentation. Once a package is installed, you can inspect its contents with the dpkg tool.

For example, to list all files provided by the Nginx package:

```
dpkg --listfiles nginx
```

Example output:

```
.
usr
usr/sbin
usr/sbin/nginx
usr/share
usr/share/doc
usr/share/doc/nginx
usr/share/doc/nginx/changelog.Debian.gz
usr/share/doc/nginx/copyright
usr/share/man
usr/share/man/man8
usr/share/man/man8/nginx.8.gz
```

This output confirms that the Nginx executable is located at `/usr/sbin/nginx`.

To determine which package provides a specific file, use the `dpkg --search`command:

```
dpkg --search /usr/sbin/nginx
```

Example output:

```
jeremy@kodekloud:~$ dpkg --search /usr/sbin/nginx
nginx: /usr/sbin/nginx
jeremy@kodekloud:~$
```

To gather more details about a specific package, such as its dependencies or description, you can use the `apt show` command. For instance:

```
apt show libnginx-mod-stream
```

Example output:

```
jeremy@kodekloud:~$ apt show libnginx-mod-stream
Package: libnginx-mod-stream
Version: 1.24.0-2ubuntu7
Priority: optional
Section: httpd
Source: nginx
Origin: Ubuntu
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Original-Maintainer: Debian Nginx Maintainers <pkg-nginx-maintainers@alioth-lists.debian.net>
Bugs: https://bugs.launchpad.net/ubuntu/+filebug
Installed-Size: 224 kB
Depends: nginx-abi-1.24.0-1, libc6 (>= 2.33)
Recommends: nginx
Homepage: https://nginx.org
Download-Size: 84.2 kB
APT-Sources: http://us.archive.ubuntu.com/ubuntu noble/main amd64 Packages
Description: Stream module for Nginx
 The nginx_stream module adds stream proxy support to nginx.
 .
 Stream module supports loadbalancing & proxying to TCP servers. The module
 also supports ACLs/connection limiting and configuring multiple operational
 parameters.
jeremy@kodekloud:~$
```

## Searching for Packages

If you are unsure which package contains the software you need, you can search for it using `apt search`. For example, to find packages related to "nginx":

```
apt search nginx
```

This command looks through both package names and descriptions and may return multiple results. To narrow down the search to package names only, use the `--names-only` option:

```
apt search --names-only nginx
```

You can also search using multiple keywords. For example, to find a package that includes "nginx", "module", and "image", run:

```
apt search nginx-module-image
```

This ensures that all specified search terms are included in the results.

## Removing Packages

When a package is no longer necessary, you can remove it with the following command. For example, to remove Nginx:

```
sudo apt remove nginx
```

Example output:

```
jeremy@kodekloud:~$ sudo apt remove nginx
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be REMOVED:
  nginx nginx-common
0 upgraded, 0 newly installed, 2 to remove and 0 not upgraded.
After this operation, 1,596 kB disk space will be freed.
Do you want to continue? [Y/n] y
```

Note

This command removes only the main package. Residual dependencies that are no longer needed remain on the system.

To clean up those extra dependencies, use:

```
sudo apt autoremove nginx
```

Example output:

```
sudo apt autoremove nginx
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be REMOVED:
  nginx nginx-common
0 upgraded, 0 newly installed, 2 to remove and 0 not upgraded.
After this operation, 1,596 kB disk space will be freed.
Do you want to continue? [Y/n] y
(Reading database ... 83359 files and directories currently installed.)
Removing nginx-common (1.24.0-2ubuntu7) ...
Removing nginx (1.24.0-2ubuntu7) ...
Processing triggers for man-db (2.12.0-4build2) ...
```

If needed, you can always reinstall Nginx using:

```
sudo apt install nginx
```

Using `apt autoremove` is a convenient way to remove both the main package and any leftover dependencies in a single operation.

---

# Configure the Repositories of Package Manager

In Ubuntu 22.04—the latest release at the time of this article—the repository configuration file may differ from previous versions. Older Ubuntu versions stored the file in a different path and even included comments referencing a new filename. Let’s examine the repository configuration in detail.

Below is an excerpt from the file "/etc/apt/sources.list.d/ubuntu.sources":

```
Types: deb
URIs: http://us.archive.ubuntu.com/ubuntu/
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
Types: deb
URIs: http://security.ubuntu.com/ubuntu/
Suites: noble-security
Components: main restricted universe multiverse
```

## Breaking Down the Repository Configuration

1. **Types**:  
    The first line identifies the type of repository. Here, `deb` indicates a Debian-style repository, signifying that the package manager (e.g., apt) will work with Debian package files (with the `.deb`extension) containing executable programs, configuration files, documentation, and scripts.
    
2. **URIs**:  
    The next line specifies the Uniform Resource Identifier (URI) of the repository. In this instance, it points to the Ubuntu archive mirror in the United States (`us.archive.ubuntu.com`), with `/ubuntu` indicating the repository's content. This URI directs the package manager to the correct source for downloading packages.
    
3. **Suites**:  
    This field outlines the suites or release components provided in the repository. In Ubuntu, a suite represents a set of packages tied to a specific release. For the current release codenamed "noble," three suites are defined:

| **Suite**       | **Description**                                               |
| --------------- | ------------------------------------------------------------- |
| noble           | The primary suite containing core packages for the release.   |
| noble-updates   | Contains bug fixes, security patches, and minor enhancements. |
| noble-backports | Includes packages backported from newer Ubuntu releases.      |
    
4. **Components**:  
    This line categorizes packages based on licensing and functionality:

| **Component** | **Description**                                                                               |
| ------------- | --------------------------------------------------------------------------------------------- |
| Main          | Contains free and open-source software that is officially supported by Ubuntu.                |
| Restricted    | Contains free and open-source software that has certain usage or redistribution restrictions. |
| Universe      | Contains free and open-source software that is not officially supported by Ubuntu.            |
| Multiverse    | Contains packages that are non-free or have additional licensing restrictions.                |

On servers, you typically primarily use packages from "Main" (and possibly "Universe" if needed). By default, Ubuntu enables all four components.    

## Working with Additional Repositories

Sometimes, the required software may not be available in the official Ubuntu repositories or may be outdated. In these situations, you can add third-party repositories—maintained by external teams or companies—that remain compatible with your system.

For instance, if you need the latest stable version of Docker, begin by downloading the public key for Docker’s repository. Docker signs its packages with a private key; using the public key ensures the integrity of the packages by verifying their signatures.

### Downloading the Docker Public Key

Execute the following command to download the Docker public key and save it as `docker.key`:

```
jeremy@kodekloud:~$ curl "https://download.docker.com/linux/ubuntu/gpg" -o docker.key
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 3817  100 3817    0     0  9651      0 --:--:-- --:--:-- --:--:-- 9663
jeremy@kodekloud:~$
```

After downloading, verify the file by listing the directory contents:

```
jeremy@kodekloud:~$ ls -la
total 32
drwxr-x---  4 jeremy jeremy 4096 Jun  5 01:56 .
drwxr-xr-x  3 root   root   4096 Jun  5 01:47 ..
-rw-r--r--  1 jeremy jeremy  220 Mar 31 08:41 .bash_logout
-rw-r--r--  1 jeremy jeremy 3771 Mar 31 08:41 .bashrc
drwx------  2 jeremy jeremy 4096 Jun  5 01:48 .cache
-rw-r--r--  1 jeremy jeremy  807 Mar 31 08:41 .profile
drwxr-xr-x  2 jeremy jeremy 4096 Jun  5 01:47 .ssh
-rw-r--r--  1 jeremy jeremy    0 Jun  5 01:50 .sudo_as_admin_successful
jeremy@kodekloud:~$
```

Next, convert the key to a binary format using the `gpg --dearmor` command:

```
jeremy@kodekloud:~$ gpg --dearmor docker.key
jeremy@kodekloud:~$ ls
docker.key  docker.key.gpg
jeremy@kodekloud:~$
```

Then, move the dearmored key into the apt keyrings directory:

```
jeremy@kodekloud:~$ sudo mv docker.key.gpg /etc/apt/keyrings/
jeremy@kodekloud:~$ ls /etc/apt
apt.conf.d  keyrings  preferences.d  sources.list.d
jeremy@kodekloud:~$ ls /etc/apt/keyrings/
auth.conf.d  preferences.d  sources.list
```

This method organizes third-party keys separately, making them easier to manage. If you ever need to disable a repository, simply remove its configuration file.

### Adding the Docker Repository

Rather than altering the main sources file, create a new configuration file in the `sources.list.d` directory. For Docker, create a file named `docker.list` and insert the following configuration:

```
deb [signed-by=/etc/apt/keyrings/docker.key.gpg] https://download.docker.com/linux/ubuntu noble stable
```

This configuration line includes:

- `deb`: Specifies a Debian-style repository.
- The Docker repository URL.
- The distribution codename (`noble`).
- The component (`stable`) indicating the stable Docker software version.
- The `[signed-by=...]` option that points to the trusted public key.

After saving the file, update the package manager's database with:

```
jeremy@kodekLOUD:~$ sudo apt update
```

The output should indicate that the Docker repository is being queried:

```
jeremy@kodekloud:~$ sudo apt update
Get:1 https://download.docker.com/linux/ubuntu noble InRelease [48.8 kB]
Get:2 https://download.docker.com/linux/ubuntu noble/stable amd64 Packages [6,952 B]
Hit:3 http://us.archive.ubuntu.com/ubuntu noble InRelease
Get:4 http://security.ubuntu.com/ubuntu noble-security InRelease [126 kB]
...
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
```

Warning

If you encounter errors related to package signatures, ensure that the dearmored public key is correctly located in `/etc/apt/keyrings/docker.key.gpg`.

## Using Personal Package Archives (PPAs)

Ubuntu supports Personal Package Archives (PPAs) as a convenient method for adding third-party repositories. PPAs simplify the process and even allow you to create your own.

For example, to add a PPA for the latest graphics drivers, use:

```
sudo add-apt-repository ppa:graphics-drivers/ppa
```

This command structure is simple:

- The `ppa:` prefix indicates a Personal Package Archive.
- It is followed by the username (e.g., `graphics-drivers`) and the repository name (`ppa`).

After executing the command, confirm the change by pressing Enter when prompted. This utility will handle key management, update repository configuration files, and refresh the package database automatically.

To list all enabled PPAs, run:

```
sudo add-apt-repository --list
```

To remove a PPA, use:

```
sudo add-apt-repository --remove ppa:graphics-drivers/ppa
```

A prompt will appear asking for confirmation before removal.

## Final Steps

After configuring repositories—whether they are official, third-party, or PPAs—it is important to update the package database one final time:

```
sudo apt update
```

This ensures that your system is aware of all available packages and updates. With the repositories correctly configured and verified, you are now ready to install software and manage your Ubuntu system effectively.

---

# Install Software by Compiling Source Code


```bash
git clone <repo>

# install dependencies

./autogen.sh

make

make  clean

# run the compiled application

sudo make install
```

---

# Verify Integrity and Availability of Resources and Processes


## Checking Disk Space with df

![[Pasted image 20251003112056.png]]

To view the disk space used by a specific directory, use the `du` utility.

```bash
d -sh /usr
```

-s: summarize

## Checking Memory Usage with free

```bash
free -h
```

## Analyzing CPU Load with uptime

```bash
uptime
```

## Checking File System Integrity

Before checking file system for errors, ensure it is unmounted. File system checks differ depending on whether your system uses XFX or ext4. 
![[Pasted image 20251003121523.png]]

## Verifying the Key Processes and Services

This command displays Systemd init dependencies in a tree-like structure:
```bash
systemctl list-dependencies
```

```bash
journalctl -u atd.service
```

---

# Change Kernel Runtime Parameters Persistent and Non Persistent

## What are Kernel Parameters

Those are settings that control the behavior of the Linux Kernel. They can affect:
- Hardware
- Performance
- Security
- Other system behaviors

> [!Important]
> The kernel itself only recognizes **predefined parameters**

### Boot-time parameters

- Set when the system boots, usually via the boot-loader
>[!Example]
>Adding quiet or swapccount=1 to the kernel line in GRUB

> [!Syntax]
> parameter_name=value

>[!Example]
>GRUB_CMDLINE_LINUX="quiet splash maxcpus=2"

### Runtime parameters

- Can be changed while the system is running.
- Usually accessed via the `/proc/sys/` filesystem or the `sysctl` command.
>[!Example]
> Controlling IP forwarding:
> ```bash
> sysctl net.ipv4.ip_forward=1
> ```
- Permanent changes can be made in /etc/sysctl.conf or /etc/sysctl.d/\*.conf.

> [!Naming Conventions]
> - Parameters beginning with `net.` relate to networking.
> - Parameters starting with `vm.` pertain to virtual memory.
> - Filesystem settings use the `fs.` prefix.

## Summary Table

|Configuration Scope|Change Method|Example Command|
|---|---|---|
|Non-Persistent|Temporary setting change|sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1|
|Persistent|Create conf file in /etc/sysctl.d|sudo vim /etc/sysctl.d/swap-less.conf <br> (add vm.swappiness=20)|

---

# List and Identify SELinux File and Process Context

Traditional Linux access controls such as file permissions and directory settings are often too coarse to handle sophisticated cyber threats. Although these basic permissions allow read, write, or execute actions, they typically do not enforce fine-grained restrictions. SELinux bridges this gap by introducing advanced security contexts that enable highly granular access control.

![[Pasted image 20251003134329.png]]
In ubuntu, apparmor is enabled by default.

```bash
setatus
```
This command is used to see the status of SELinux
![[Pasted image 20251003134926.png]]
![[Pasted image 20251003135144.png]]

![[Pasted image 20251003135538.png]]
![[Pasted image 20251003135557.png]]

## How SELinux Works at a High Level

At its core, SELinux uses security contexts—extended metadata labels on files and processes—to determine which actions are permitted or denied. A typical SELinux file label includes four components in the following order:

1. **SELinux User** (e.g., `unconfined_u`)
2. **Role** (e.g., `object_r`)
3. **Type** (e.g., `user_home_t`)
4. **Level** (e.g., `s0`)

For example, the context label:

```
unconfined_u:object_r:user_home_t:s0
```

indicates that the file is associated with the SELinux user `unconfined_u`, the role `object_r`, the type or domain `user_home_t`, and the security level `s0`.

SELinux’s decision-making process follows these steps:

- **User Mapping**: The system first checks the SELinux user, which may be different from the Linux login username. Each login is mapped to an SELinux user as specified by the policy.
- **Role Verification**: The system then determines if the user has the necessary permissions to assume a specific role. For example, a developer may be confined to using roles such as `developer_r` or `docker_r`, while roles like `sysadmin_r` remain off limits.
- **Type-Based Access Control**: The type component enforces the most granular security. When processes or files are assigned a specific type, they are confined to a strict set of permitted actions.
- **Security Level**: Although rarely used in standard configurations, the level field enables multi-level security controls for organizations with multiple clearance layers.

```bash
getenforce
```

When the output is Permissive, it is not enforcing security rules, its just observing every action on our machine. Like Learning Mode.

# Create and Enforce MAC Using SELinux

In this lesson, we'll explore how to enforce Mandatory Access Control (MAC) using the SELinux security module. SELinux is enabled by default on Red Hat and CentOS systems, while Ubuntu uses AppArmor as its default security module. Before installing or configuring SELinux on a system set up by others, always verify which security module is active. In this guide, we assume that your Ubuntu system does not have SELinux enabled. We'll walk you through disabling AppArmor, installing SELinux tools, and configuring SELinux from scratch.
## Disabling AppArmor

Since AppArmor is enabled by default on Ubuntu systems, you must stop its service to avoid conflicts with SELinux:

```bash
sudo systemctl stop apparmor.service
```

After stopping the service, ensure that AppArmor does not automatically start on boot.

## Installing SELinux and AuditD

To get started with SELinux, install the SELinux utilities along with the AuditD package. The SELinux package includes essential tools and default security policies, and AuditD logs system events—a critical resource when building custom SELinux policies.

Below is a sample installation output. Your output may vary:

```
Selecting previously unselected package libgfortran5:amd64.
Preparing to unpack .../04-libgfortran5_14-20240412-0ubuntu1_amd64.deb ...
Unpacking libgfortran5:amd64 (14-20240412-0ubuntu1) ...
...
Progress: [ 28%] [##############################.....................]
```

Similarly, when AuditD is configured, you might observe:

```
Setting up python3-semanage (3.5-1build5) ...
...
Progress: [ 78%] [#########################.........................]
```

## Verifying and Activating SELinux

To verify whether SELinux is enabled on your system, run:

```bash
sestatus
```

If SELinux is disabled, the output will resemble:

```bash
jeremy@kodekLOUD:~$ sestatus
SELinux status:             disabled
jeremy@kodekLOUD:~$
```

You can also verify the current SELinux context assignments in the root directory:

```bash
jeremy@kodekLOUD:~$ ls -Z /
?      bin              ?   lib64           ?   mnt             ?   run
?      boot             ?   home            ?   lost+found      ?   proc
...
```

To enable SELinux, execute:

```bash
sudo selinux-activate
```

This command modifies the GRUB bootloader to include the parameter necessary to load the SELinux module at boot. The output will inform you about generating the GRUB configuration and activate SELinux—after which a system reboot is recommended.

## Configuring the Bootloader

After activating SELinux, verify the bootloader settings. In the `/etc/default/grub` file, you should see the following line instructing the kernel to load SELinux:

```bash
GRUB_CMDLINE_LINUX="security=selinux"
```

Once you confirm the configuration, note the presence of an autorelabel file in the root filesystem. This file tells SELinux to relabel every file upon reboot. For example, listing the root directory including hidden files shows:

```bash
jeremy@kodekLOUD:~$ ls -a /
.              bin.usr-is-merged  etc               lib.usr-is-merged  opt   sbin    swap.img  var
..             boot                home              lost+found        proc  sbin.usr-is-merged  root  snap
.autolabel    cdrom               lib               media             mnt   run     srv       tmp    usr
```

Now, reboot your system to allow SELinux to relabel the filesystem. The relabeling process might take some time depending on the number of files:

```bash
sudo reboot
```

During the first boot after enabling SELinux, the bootloader pauses for 30 seconds to allow intervention if necessary. Once complete, subsequent boots will operate normally. After rebooting, verify SELinux status by running:

```bash
sestatus
```

Expected output:

```bash
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             default
Current mode:                   permissive
Mode from config file:          permissive
...
```

## Understanding SELinux Modes

SELinux operates in two primary modes:

- **Permissive:** In this mode, SELinux logs policy violations without enforcing them. It is ideal for "training" your policies.
- **Enforcing:** Here, SELinux actively enforces policies and may deny actions that do not comply with the defined rules.

To check the current mode, run:

```bash
getenforce
```

When operating in permissive mode, actions that should be blocked are only logged. For instance, accessing via SSH may trigger AVC (Access Vector Cache) messages in the audit log. To inspect these messages, use:

```bash
sudo audit2why --all | less
```

The output might include entries like:

```
type=AVC msg=audit(1716410944.847:111): avc: denied { execute } for pid=922 comm="run-parts" name="1-landscape-sysinfo.wrapper" dev="dm-0" ino=284986 scontext=system_u:system_r:sshd_t:s0 tcontext=system_u:object_r:usr_t:s0 tclass=file permissive=1
        Was caused by:
                Missing type enforcement (TE) allow rule.
        You can use audit2allow to generate a loadable module to allow this access.
```

>[!Note]
Although similar audit entries might be repeated, a single occurrence generally provides enough insight into the issue.

---
## Examining Process and File Contexts

You can verify SELinux contexts for running processes and files. For example, to inspect the contexts of SSH daemons, run:

```bash
ps -eZ | grep sshd_t
```

Sample output:

```bash
system_u:system_r:sshd_t:s0       905 ?        00:00:00 sshd
system_u:system_r:sshd_t:s0       906 ?        00:00:00 sshd
system_u:system_r:sshd_t:s0       1020 ?       00:00:02 sshd
```

To view the context for the SSH daemon executable:

```bash
ls -Z /usr/sbin/sshd
```

Expected output:

```bash
system_u:object_r:sshd_exec_t:s0 /usr/sbin/sshd
```

When a file labeled with `sshd_exec_t` is executed, SELinux transitions the process into the `sshd_t` domain, where its type enforcement rules become active.

---

## Generating and Loading a Custom SELinux Policy

After operating in permissive mode and gathering necessary logs, you can generate a custom SELinux policy module from the audit logs. Run:

```bash
sudo audit2allow --all -M mymodule
```

The command output will include a message similar to:

```
********************* IMPORTANT *********************
To make this policy package active, execute:
semodule -i mymodule.pp
```

To load the custom policy module, execute:

```bash
sudo semodule -i mymodule.pp
```

>[!Note]
If you see a warning such as "libsemanage.add_user: user sddm not in password file", it is a known issue. You can safely ignore it if you are not using sddm.

---

## Switching to Enforcing Mode

To temporarily switch SELinux from permissive to enforcing mode, execute:

```bash
sudo setenforce 1
getenforce
```

Expected output:

```
Enforcing
```

For initial testing, it is advisable to use temporary enforcement. Once you are sure that your custom policies cover all the necessary actions, make the change permanent by modifying `/etc/selinux/config`.

Open the configuration file with:

```bash
sudo vim /etc/selinux/config
```

Then change:

```bash
SELINUX=permissive
```

to:

```
SELINUX=enforcing
```

Save the file and reboot the system to apply the changes.

---

## Reviewing the Custom Policy Module

After loading the custom module, two files are created:

- A binary package (`mymodule.pp`)
- A human-readable policy file (`mymodule.te`)

The `.te` file contains the type enforcement rules. An excerpt might look like:

```
module mymodule 1.0;
require {
    type sshd_t;
    type tmp_t;
    type getty_t;
    type systemd_journal_init_t;
    type policykit_t;
    type mount_exec_t;
    type kmod_t;
    type var_t;
    type systemd_journal_t;
    type systemd_runtime_notify_t;
    type xdg_cache_t;
    type fixed_disk_device_t;
    type fsadm_exec_t;
    type mount_runtime_t;
    type var_lib_t;
    type mount_t;
    type systemd_hostnamed_t;
    type var_log_t;
    type etc_runtime_t;
    type lib_t;
    type system_dbusd_t;
    type usr_t;
};
class dir { add_name search watch write };
class dbus send_msg;
```

Review this file carefully before deploying the policies in production.

---

## Understanding and Reviewing SELinux Type Enforcement

The custom module includes specific rules for the `sshd_t` domain. For instance, it allows the SSH daemon to access files labeled with `var_log_t`:

```bash
allow sshd_t var_log_t:file { append create getattr ioctl open };
```

This rule ensures that processes running in the `sshd_t` domain, such as the SSH daemon, can write to log files like `/var/log/auth.log`, which are labeled as `var_log_t`.

You can verify these contexts with the following commands:

```bash
ps -eZ | grep sshd_t
ls -Z /var/log/auth.log
grep var_log_t:file mymodule.te
```

Expected outputs:

```
system_u:system_r:sshd_t:s0       905 ?        00:00:00 sshd
...
system_u:object_r:var_log_t:file /var/log/auth.log
...
allow sshd_t var_log_t:file { append create getattr ioctl open };
```

This level of fine-grained control helps restrict processes to specific files, reducing potential risks if a process (such as an NGINX web server) is compromised.

---

## Changing SELinux File Contexts with chcon and restorecon

A file's SELinux context is composed of three parts: user, role, and type. To manually change a file’s context, use the `chcon` command. For example, to view and modify the context of `/var/log/auth.log`:

```bash
ls -Z /var/log/auth.log
sudo chcon -u unconfined_u /var/log/auth.log
```

To change the role and type:

```bash
sudo chcon -r object_r /var/log/auth.log
sudo chcon -t user_home_t /var/log/auth.log
```

>[!Warning]
>Manual changes made with `chcon` may be overridden during a complete filesystem relabel.

To restore the default context, use the `restorecon` command. For example, to fix the context of `/var/log/auth.log` based on `/var/log/dmesg` as a reference:

```bash
sudo chcon --reference=/var/log/dmesg /var/log/auth.log
```

---

## Fixing Contexts for Web Content

If you create a new directory for your website under `/var/www` and add files, they may initially have an incorrect SELinux type (such as `var_t`). To correct this recursively:

```bash
sudo mkdir /var/www
sudo touch /var/www/{1..10}
ls -Z /var/www/
# Files may initially be labeled as:
# unconfined_u:object_r:var_t:s0
sudo restorecon -R /var/www/
ls -Z /var/www/
```

After running `restorecon -R /var/www/`, the files should be relabeled correctly (for example, as `httpd_sys_content_t`), which is appropriate for web content. Note that `restorecon` by default only restores the type, not the user or role. If needed, use the force option to restore all parts of the label.

To permanently assign a default label to a specific file (e.g., `/var/www/10`), add a rule with `semanage`:

```bash
sudo semanage fcontext --add --type var_log_t /var/www/10
# You might encounter:
# libsemanage.add_user: user sddm not in password file
sudo restorecon /var/www/10
```

For directories and their contents, remember to wrap the path in quotes if it contains special characters. Refer to the SELinux documentation for additional examples.

---

## SELinux Booleans and Port Bindings

SELinux booleans act as switches to enable or disable related security policies. To list supported booleans and their current settings, run:

```bash
getsebool -a | grep virt_use_nfs
```

To set the `virt_use_nfs` boolean, execute:

```bash
sudo setsebool virt_use_nfs 1
```

SELinux also manages which ports a daemon is allowed to bind to. To list all allowed port bindings:

```bash
sudo semanage port --list
```

For instance, SSH is typically restricted to port 22. To allow SSH to bind to an additional port (such as 2222), run:

```bash
sudo semanage port --add --type ssh_port_t --proto tcp 2222
```

To later remove this port binding:

```bash
sudo semanage port --delete --type ssh_port_t --proto tcp 2222
```

>[!Note]
Any warnings regarding the user sddm may be safely ignored if you are not using sddm.

---

