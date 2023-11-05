---
cover: >-
  https://images.unsplash.com/photo-1618044619888-009e412ff12a?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwxfHxhdXRoZW50aWNhdGlvbnxlbnwwfHx8fDE2OTkxNzc5OTZ8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# SSH key authentication on Windows

SSH is an amazing remote access method we have been using for remote management in \*Unix admin world, and its key authentication is even better for security and easy access to target machines. However, it is quite unusual for Windows admin to use SSH to manage their Windows servers and clients although they can do almost everything with Powershell. The normal workflow for Windows admin would be RDP into the Windows servers to administer and do the daily stuffs in the GUI. Throughout my IT career, I have never seen a Windows box has been setup without GUI and RDP for system management.&#x20;

In the parallel universe, SSH is the only secure and standard remote access we use as Linux or network admins for our daily admin tasks. I rarely see any Linux admins use SSH password to login into their remote servers but almost always use SSH key authentication. Well... network admins would probably still use SSH password as their primary mean of remote access to their network gears.&#x20;

From my recent password reset process with IT, I became to think that the password is one of the bad inventions for authentication. Why and how have we coped with it for the past decade? Here is the list of things why I consider the password is cumbersome.&#x20;

* Short password is easy to crack but the end users love it short and easy so that it can be remembered and used multiple times for different logins.
* Long password is hard to remember for users and it ends up written on the sticky note around their desks although they have been trained over and over again not to do so. With the emerging technology of quantum computing, a long password is not effective as much as it used to be in the past. But size still matters in passwords for authentication.
* Users tend to reuse the same short or long password everywhere; meaning it is really bad when there is a data breach happened on the platform or service they use on the internet. Nowadays data breach or leak has been heavily normalised from the recent data breaches or leaks.&#x20;
* Although a sophisticated user can use a password manager, it is not quite straightforward to understand why it is required in the first place. When the password manager platform is breached or even the local password database is corrupted, it is very concerning for the total loss of all passwords stored in it.&#x20;
* Due to the nature of password security, multi-factors authentication MFA has been introduced for some time but a lot of normal end users are not using it since it is considered as an extra complexity.&#x20;

Those are a few things I can think of why we shouldn't use password for the authentication. We could have done so much better with its innovation around it.&#x20;

As an automation guy, SSH key authentication is my bread and butter for all Linux logins and automation workflow. When it comes to Windows server or client, it is not almost weird using SSH to login and Powershelling for all Windows admin tasks. Note that Windows doesn't even come with any usable text editor in pure Powershell environment. Since I started working on automation with Windows, there are a lot of Powershell I have to use and understand its mechanism for various stuffs. The more I unleash the power of Windows PowerShell for automation, the more I am comfort working in its CLI for most of the things I need to do for Windows workflow.&#x20;

In this article, I intend to capture how we can setup the SSH key authentication for Windows. These days Windows come with OpenSSH server and client in its optional features section so we can easily install the feature as followed.

* Go to Settings > Apps & features > Optional features > Add a feature > OpenSSH Server > Install&#x20;

That's it. Now you can use the ssh command with password to login into the Windows machine for remote access. But to configure ssh key authentication, we need to configure _sshd\_config_ file located in C:\ProgramData\ssh. Following parameters are required for key authentication.&#x20;

```bash
PubkeyAuthentication yes
PasswordAuthentication no

# override default of no subsystems
Subsystem	sftp	sftp-server.exe

Match Group administrators
       AuthorizedKeysFile __PROGRAMDATA__/ssh/administrators_authorized_keys
```

Then add a new file called _administrators\_authorized\_keys_ in C:\ProgramData\ssh\ directory and place all desired admins ssh keys into the file and save. After that, run the following command in Powershell to change its permission.

```powershell
icacls.exe "C:\ProgramData\ssh\administrators_authorized_keys" /inheritance:r /grant "Administrators:F" /grant "SYSTEM:F"
```

Remember it will only allow the users in Administrators group for key authentication. Otherwise it will reject the ssh logins for any other users not belong to the group. After all those changes in sshd configuration, we need to restart the sshd service as below.&#x20;

```powershell
restart-service sshd
```

Now SSH key authentication on Windows is finished and ready to use.&#x20;
