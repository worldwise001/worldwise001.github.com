---
author: worldwise001
layout: post
title: One Woman Infrastructure - Keeping Things Up to Date
---

I've been running portions of this infrastructure since about 2013 or so. It started with one VPS, and expanded to adding a high-traffic server to host a Tor node, and from there it's expanded to 3 VPSes, 2 servers, and 3 containers, for a total of 8 hosts to maintain full-time. This is a lot! This is going to be the first in a series of posts about what it's like to maintain these many hosts for this long period of itme. This week's theme is updates.

Some of you may know I live on the questionable bleeding-edge and run Arch Linux on all my servers, but the challenge of keeping them up to date specifically revolves around remembering when to log in to update everything. After exploring a few fleet management systems (e.g. [Chef](https://www.chef.io/), [Puppet](https://puppet.com/), etc.) I settled on using [Ansible](https://www.ansible.com/). Specifically the [Ansible Tower](https://www.ansible.com/community/awx-project) project now open-sourced as [AWX](https://github.com/ansible/awx) has been really easy for me to set up and use. My method was as follows:
* Pick a host, and install AWX via [Docker Compose](https://github.com/ansible/awx/blob/devel/INSTALL.md). This is your main area of operations.
* (optional) You may need to log into the AWX docker container and install additional python modules for the AWX features you want.
* Generate a SSH key and store the private key as an AWX secret.
* Deploy the public key as the root user on your fleet. Set the appropriate protections you want (I used key-only login, restricted login via certain hosts, etc.)
* Install python on your fleet.

Within AWX you need to do additional setup pieces:
* Configure your user account
* Set up your host inventory with the list of hosts you want to manage. Bonus: set tags which become useful for your ansible templates later on.
* Add the repository to source for templates.
* Add the templates themselves with their appropriate configs.
* (optional) Configure schedules for those templates so that they get run frequently as Jobs.

From there, I have a series of ansible templates for provisioning:
* [Install base packages](https://github.com/worldwise001/ansible.shh.sh/blob/master/pacman-install.yaml)
* [Lock down SSH](https://github.com/worldwise001/ansible.shh.sh/blob/master/ssh.yaml)
* [Set up some common configs in /etc](https://github.com/worldwise001/ansible.shh.sh/blob/master/config.yaml)
* [Set up certificates (for renewal)](https://github.com/worldwise001/ansible.shh.sh/blob/master/certificate.yaml)
* [Set up iptables and sshguard](https://github.com/worldwise001/ansible.shh.sh/blob/master/iptables.yaml)
* [Set up syslog-ng](https://github.com/worldwise001/ansible.shh.sh/blob/master/syslog.yaml)
* (optional) [Ensure /etc/hosts has static entries](https://github.com/worldwise001/ansible.shh.sh/blob/master/hosts.yaml)

I have also some ansible templates I set on a schedule to keep the hosts up to date automatically:
* ["Safe" upgrade](https://github.com/worldwise001/ansible.shh.sh/blob/master/pacman-upgrade.yaml) (ignores kernel, pacman, glibc)
* ["System" upgrade + reboot](https://github.com/worldwise001/ansible.shh.sh/blob/master/pacman-system.yaml) (only kernel, pacman, glibc)

I'll finish off with some problems I've run into over the past few years from trying to update things (or in some cases, forgetting to update):
* RAID arrays desyncing because a drive is about to fail, causing a mysterious "missing kernel module" issue from updates only hitting one drive while the host flip-flop boots to the unsynced drive
* [Exim RCEs](https://www.bleepingcomputer.com/news/security/new-exim-vulnerability-exposes-servers-to-dos-attacks-rce-risks/) causing mysterious errors as a result of the box [being pwned](https://gist.github.com/worldwise001/3ad37162343a83dfe5eba5e435d12300)
* Grub just mysteriously failing on PV hosts, necessitating a migration from PV to HVM, resulting in addition config needed to be provided to grub to [view the serial console](https://wiki.prgmr.com/mediawiki/index.php/PV_and_HVM_Virtualization)
* Certificate expiries preventing e-mails from being received.
* The random [GLIBC dependency](https://www.google.com/search?client=firefox-b-d&q=GLIBC+not+found) issue that's not captured properly during a partial update that breaks everything.

Happy updating.
