
# Managing the Users

Linux has different kinds of Users.
1. Root users: *UserID = 0*
2. Regular users:
3. Service users: 

**Groups**: 
	All users have a primary group.
	Can be assigned to zero to unlimited additional groups.

On Linux, User information is stored in various files:
## /etc/passwd:

Contains basic user account information
Username, user ID (UID), group ID (GID), user description (fullname), home directory, and default shell.
![[Screenshot 2025-08-27 at 02.56.44.png]]
The above file shows service users and real users.

| **Type**      | **Purpose**                                                          | **Example**                |
| ------------- | -------------------------------------------------------------------- | -------------------------- |
| System Users  | Used by services, daemons, or system components. Not for logging in. | `www-data`,`sshd`,`syslog` |
| Regular Users | Real people who log in and use the system                            | ubuntu                     |
- Only `ubuntu` and `root` are typical ==interactive users== with shells like `/bin/bash`.

### Why so many System Users?

==Each service runs under its own user account== account for security purposes.
If ==apache runs as www-data==, and gets hacked, the attacker only has access as www-data, ==not root==

```bash
grep '/bin/bash\|/bin/sh' /etc/passwd
```
List only Real Users.

## /etc/shadow

- Stores encrypted user passwords and password aging information.
- Also stores additional information, such as the date of the last password change, expiry dates,...Readable only by the root users (or users with root privileges).
![[Screenshot 2025-08-27 at 03.11.53.png]]

## /etc/group:

- Contains information about the groups and their members
- Readable by all users

# Creating and Securing New users

```bash
useradd [option] username
```
*-m*: create home directory
*-d:* set custom home directory
*-s*: specify default shell
*-g*: Specify primary group instead of using the default configuration
*-G*: Add user to secondary groups

```bash
passwd [option] username
```
*-S*: display pass status
*-d*: delete passwd
*-n*: min passwd age
*-x*: max passwd age
*-l*: lock account
*-u*: unlock account

```bash
usermod [option] username
```
*-c*: change user description
*-s*: change shell
*-d*: change home directory
*-l*: change username
*-g*: change group
*-G*: change secondary group
*-aG*: add secondary group

```bash
userdel [option] username
```

# Groups

