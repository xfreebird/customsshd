# Passwordless SSHD daemon for OSX

Need to run commands through SSH in user's UI session ? Then this is for you.

## How to install ?

* Install ```customssd```

```shell
brew tap xfreebird/utils
brew install customsshd
```

* Generate SSH public/private key

```shell
ssh-keygen -t rsa -f sshd_rsa_key
```

Now you should have two files:

```sshd_rsa_key``` - private SSH key to be stored in Jenkins

```sshd_rsa_key.pub``` - public SSH key that will be used by ```customsshd```


* Install the ```customsshd``` ```launchd``` service

```shell
customsshd install sshd_rsa_key.pub
```

* Configure OSX to autologing your user at startup, this will start the daemon too.
* Configure Jenkins Slave with SSH port **50111**, and the generated private SSH key ```sshd_rsa_key```


## Why ?

Since Xcode 6 running iOS unit tests through standard SSH connection on the build machine failed the tests.

```shell
Timed out waiting 120 seconds for simulator to boot, current state is 1. If you believe this error represents a bug, please attach the log 
```
The solution is to run those tests in user's UI session.


## How it works ?

* When machine starts the configured user, and autologins it, the ```customssd``` is started by ```launchd``` in user UI session.
* ```customssd``` injects the provided public ssh key to ```$HOME/.ssh/authorized_keys```. In this way Jenkins or any other client can connect to the machine with the private SSH key and run commands in user's UI session.
* ```customssd``` keeps the public key and other files in ```$HOME/.customsshd```

# Usage

* Get version:

```shell
customsshd -v
```

* Install/update the launchd service:

```shell
customsshd install sshd_rsa_key.pub
```

* Stop the launchd service:

```shell
launchctl unload ~/Library/LaunchAgents/com.customsshd.plist
```

* Start the launchd service:

```shell
launchctl load ~/Library/LaunchAgents/com.customsshd.plist
```

# Logs

All standard output is stored in ```/tmp/customsshd.log```.
Error logs in ```/tmp/customsshd_err.log```.

# License

```
The MIT License (MIT)

Copyright (c) 2014 Nicolae Ghimbovschi

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```
