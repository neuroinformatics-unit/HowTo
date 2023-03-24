# Programming

## Ubuntu

### Distro update error
This is an error that appeared when updating to a new Ubuntu distribution.
Error msg: Software Updater - Not all updates can be installed.   
Solution: `sudo apt-get dist-upgrade`

### Terminal is not opening after installing a new Python version
If you installed a new Python version without the use of `conda`, there might be a mismatch in the python naming in a bin file.   
If it's possible open terminal through vscode or open a virtual terminal (VT) with CTRL + ALT + F3 and run `gnome-ternimal`.
Does it throw a Python error? If yes run `sudo nano /usr/bin/gnome-terminal` and change `#!/usr/bin/python3` to `#!/usr/bin/python3.10` if the version you're currently using is 3.10.  
Exit the VT via CTRL + ALT + F2.

## VSCode
### SSH into an unmanaged machine from remote
> **_Example Usecase:_**  When working from home, connect from you local Mac to the SWC office Linux machine

In your local machine `cd` and ` open .ssh/config` and append the following configurations:
```
Host *
    ServerAliveInterval 60

Host jump-host
    User swcUserID
    HostName ssh.swc.ucl.ac.uk

Host remote-host
    User remoteMachineUsername
    HostName 172.24.243.000
    ProxyCommand ssh -W %h:%p jump-host
```
Make sure to replace `172.24.243.000` with the IP address of your remote machine.
On Ubuntu, you can find the IP address in this way:
* Got to `Settings` then `Network`
* Click on the cogwheel next to your connections (usually `Wired`)
* The `IPv4` is the address you are looking for

If you do not have a config file in your .ssh folder, create one:
```bash
cd .ssh/
touch config
```
Connect to VPN, then use the `Open a remote window` (Remote - SSH extension) tool of vscode and connect to `remote-host`. You will be asked for your SWC and Linux passwords. 

## GitHub
### How to detach a forked repo
If you forked a repo and want to detach it from the original repo, use the [Github chatbot-virtual-assistant](https://support.github.com/contact?tags=rr-forks&subject=Detach%20Fork&flow=detach_fork).
Follow the instructions and wait for the response.

## Python
Update cookiecutter-generated files with cruft

Ref: [original docs](https://cruft.github.io/cruft/) and [github repo](https://github.com/cruft/cruft).

First, `pip install cruft`. 
Then, you have to link your repo to the cookiecutter template by specifying its url.
You will also need to specify the cookiecutter commit hash, i.e. the version of cookiecutter you used to generate the repo. For now, we have to do this manually by comparing the history.
Once you have the hash, you can run `cruft link link.to.your.template`. This will create a `.cruft.json` file in your repo.

Now you can run `cruft diff` to see the changes between your repo and the template. If you are happy with the changes, you can run `cruft update` to update your repo. This will not overwrite all changes you have made, but only the files that have been changed in the template.

In the case `cruft update` is not able to apply all the changes, it will create a `.something.rej` file. You can then manually apply the changes to the files. This is a bit tedious, but it's the only way I have found to do it so far.

I have found that although the link to the repo is correct (`github_repository_url` field), the command `cruft diff` fails, so you might have to change it manually in the `.cruft.json` file to "provide later". 🤷🏻‍♀️

`cruft check` will check if your repo is up to date with the template, and basically will just give you a boolean response.

Once you are satisfied, commit the changes to a new branch and open a PR to merge it into `main`. 🎉

## Licensing
For information about choosing a software license, see the [Licensing page](Licensing.md).
