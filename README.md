# login-notification

## Purpose

Login-notification is a shell script that can be run by PAM to monitor logins/logouts on a PAM-enabled system (i.e. most Linux distributions) by sending an email to a designated address every time a user logs in or out. This includes logins through `sudo`.

Please note that the script is intended for system administrators who are already well-versed in a Linux command line. The author will gladly provide reasonable amounts of help, but please note that the script is shared as a courtesy to the community, not as a commitment to maintain or support it. If the author is busy, you may very well be on your own.

**IMPORTANT**: Do not rely on this script if you don't generally trust your system administrators. The root user can easily disable it, and anyone who controls the network, or either email account used to send or receive notifications, can also easily make sure that notifications are not delivered. This script may be used as a piece in a bigger security puzzle, but cannot guarantee that notifications are delivered if a user with too much control over your system or network wants to circumvent it.

## Pre-requisites

1. An account at an SSL-enabled outgoing mail server (SMTP), typically an email account.

2. An email account to receive the notifications. It can be the same one as in step 1.

## Setup

### 1. Place the script on the server.

As a root user, you should be able to copy/paste this into your terminal.

    mkdir -p /opt
    cd /opt
    git clone https://github.com/binary-is/login-notification
    cd login-notification
    cp login-notification.example.json login-notification.json

### 2. Create a configure file (JSON).

Open `/opt/login-notification/login-notification.json` with your favorite editor and replace the example values with true values.

### 3. Set up PAM to run the script with the config file when a session is started.

First, test the script by simply running it.

    /opt/login-notification/login-notification /opt/login-notification/login-notification.json

You should receive the notification via email. It is normal that the `PAM_*` variables are all empty when you run it from a command line.

On a Debian-based system (Ubuntu, for example), open `/etc/pam.d/common-session` and add this line above the others:

    session optional pam_exec.so /opt/login-notification/login-notification /opt/login-notification/login-notification.json

Even if you're not running a Debian-based system, odds are that the location is the same. By all means let me know if the path is different on your system.

You don't have to restart anything. At this point you should receive an email when you log in, whether through SSH, the login prompt on the physical system or when using `sudo`.

**A conceivably insane idea** is to change "session optional" to "session required" in the PAM-line above. This will make the login fail if the script fails to fully execute. **This will render your system entirely inaccessible if the script fails for any reason and should never be done unless you can fully restore the system, including its data, without notice**.

