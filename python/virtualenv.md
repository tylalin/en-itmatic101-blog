---
cover: >-
  https://images.unsplash.com/photo-1628853210688-acf6bfeb5daf?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw3fHxweXRob258ZW58MHx8fHwxNjk5MTc3Njg4fDA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Setting up a Python developer environment

Developer environment is the environment a developer use to code without affecting his/her work laptop/PC; while installing additional packages and plugins required by end customer's defined specifications and not breaking daily work engine (laptop/PC). Some developers prefer to set up a separate virtual machine to muck around with their environment carefree but some don't really have much horsepower on their work laptop/PC to do so. In that case, virtual environment or venv comes in handy for the ones don't really like to setup a virtual machine and maintain/patch two different operating systems; one on the host machine and one on the VM.&#x20;

In this tutorial, I will do a quick rundown on how you can quickly setup a developer environment with venv. Of course, I will use Ubuntu 20.04 LTS desktop as my distribution and try to install venv for Python 3

* As always and oftentimes, it is the best practice to update the APT. &#x20;

```
sudo apt update
```

* Install prerequisites packages and pip for Python 3

```
sudo apt install build-essential libssl-dev libffi-dev python-dev -y
sudo apt install python3-pip -y
```

* Then use pip to install virtualenv

```
sudo pip3 install virtualenv 
```

* Make a directory called py3 at your home directory for virtualenv

```
mkdir py3
```

* After that, make the newly created directory py3 as virtualenv for Python 3

```
virtualenv -p python3 py3
```

* Activate the Python 3 virtualenv

```
source py3/bin/activate 
```

* py3 is displayed as prefix at your terminal prompt as below. And now you are in the venv to start coding.

```
(py3) tyla@ubuntu:~$ 
```

* To leave the venv, type "deactivate" command at the prompt.

```
(py3) tyla@ubuntu:~$ deactivate
```

* To make our life easier in the terminal, we can create an alias called py3 in .bashrc file at home directory. Add the following line in our .bashrc file by using your favorite text editor like vim or nano.

```
alias py3='source /home/tyla/py3/bin/activate'
```

* Now all we have to do is type "py3" at the prompt rather than "source py3/bin/activate" as below.

```
tyla@ubuntu:~$ py3
(py3) tyla@ubuntu:~$ python --version
```

That's it! The virtual environment has been successfully setup as developer environment. Now you can start coding some python 3 in your developer environment.&#x20;
