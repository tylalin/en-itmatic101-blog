# SSH key authentication on Windows

SSH is an amazing remote access method we have been using for remote management in \*Unix admin world, and its key authentication is even better for security and easy access to target machines. However, it is quite unusual for Windows admin to use SSH to manage their Windows servers and clients although they can do almost everything with Powershell. The normal workflow for Windows admin would be RDP into the Windows servers to administer and do the daily stuffs in the GUI. Throughout my IT career, I have never seen a Windows box has been setup without GUI and RDP for system management.&#x20;

In the parallel universe, SSH is the only secure and standard remote access we use as Linux or network admins for our daily admin tasks. I rarely see any Linux admins use SSH password to login into their remote servers but almost always use SSH key authentication. Well... network admins would probably still use SSH password as their primary mean of remote access to their network gears.&#x20;

From my recent password reset process with IT, I became to think that the password is one of the bad inventions for authentication. Why and how have we coped with it for the past decade? Here is the list of things why I consider the password is cumbersome.&#x20;

* Short password is easy to crack but the end users love it short and easy so that it can be remembered and used multiple times for different logins.
* Long password is hard to remember for users and it ends up written on the sticky note around their desks although they have been trained over and over again not to do so. With the emerging technology of quantum computing, a long password is not effective as much as it used to be in the past. But size still matters in passwords for authentication.
* Users tend to reuse the same short or long password everywhere; meaning it is really bad when there is a data breach happened on the platform or service they use on the internet. Nowadays data breach or leak has been heavily normalised from the recent data breaches or leaks.&#x20;
