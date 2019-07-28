#### Five W's
This naive set of Ansible playbooks is intended to get up and running an SDRPlay listening post of certain configuration.
Installation scripts are for a of SoapyRemote capable OpenWebRX machine.
I just documented my own installation with the better recovery and resilience in mind (too much knowledge to remember, too long notes to keep)

The solution has been tested and provisioned for this configuration:
  - 2x SDRPlay RSP1a
  - 1..2 x RTL-SDR

The software toolpath has not been tested for other SDRs.
Everything from pothosware that is compiling, is nice.

The decision (productivity, speed, portability, web security):
  - there shall be two machines - one for the OpenWebRX webserver, and another being the host SDRs are connected to.
  - the connectivity btw the machine relies on SoapyRemote (a piece from the extensive Pothosware Project).

Another decision - to use Ansible.
  - I am an Ansible novice, there are a few of challenging operations I haven't yet learned to automatize with it
     + well, manual override there.
  - the official Ansible style (regarding variables, dir layout, modularity) is mostly not kept 
     + thus no upload to Ansible Galaxy (would be a warranted shame ;) )

More decisions:
The OS shall be Debian.
sudo is to be avoided except for one third party operation otherwise impossible
The Soapy related packages shall be compiled at the place from the fresh source.

There are two important dependencies kept as locally as warez files and reused on the need
  - These particular fine-tuned OpenWebRx configuration files, for 3 instances of OpenWebRx
  - SDRPlay API installation binary, downloaded from the vendor site.
     + hard to automatize, the D/L uses CAPTCHA

=====================

#### The basic workflow:

a) install a fresh Debian (netinstall).
  - when taskselect: no desktop env, no print srv. Just ssh server plus standard system utilities.
  - write down the (bootstrap) user and root information, you will need these later

b) on the control host, install Ansible.
  - your Ansible installation needs some "style" to be chosen; if no dependancies (like employer's Amazon infrastructure) then do keep this one simple and stupid. E.g. I erased the global conf from /etc/ansible and created a very simple local ansible.cfg.

c) using Ansible means, connect to the target(s) and install a deploy user - script 00. This time you should use your bootstrap user.

d) as a deploy user, use ansible to install the further software. There are some crucial moments:
  - Vendor API installation script insists on sudo (at least v 2.13.1).
  - manually fiddling with the ansible.conf might be needed for this (sudo vs su)

#### Where I spent most of the time:

  - not understanding the *.yaml files syntax is positional (like with Python code). One extra space in the end of a line and you are lost. One space missing in front of "- name" and you are lost once again. Better install and use yamllint.
  - differences btw various Ansible versions(path/dest, reboot). You will loose your time b/c certain nice stackexchanged syntaxes just don't work as they once did (b/c Ansible has evolved much).
  - keep off from the Ansible official manuals as long as you can - they are useful only if you already understand what you are doing.
  - some most important Ansible syntaxes are not too widely discussed (like why should become_method be described in the [privilege_escalation] and not in [default] section of the ansible.cfg. Or how to do fileinline with multiple items. Or that connection: local exist at all. Thnx for bloggers!
  - It is now over a year while SDRPlay vendor is not helping the pothosware developers to make a transit to their 3.0.1 version of API. That results git cloning and then compiling SoapySDRPlay will fail without meaningful comments. But the secret is simple: keep using the old 2.3.1 version of the SDRPlay API.
  - the order you compile and install the software is important. Next items need the previous items installed

#### Other possible difficulties?

  - Get yr HW working (antennas, cables, etc)
  - Work through the Ansible idiosyncrasies. It wasn't a human who created this ... possibily an alien or sysadmin.
  - Linux admin qualifications would be useful - if smth goes not according to playbook, you have to debug it out.
  - Security of the internet connected machine will certainly need some serious trimming and there is no single and simple guide for the purpose.

#### USB Resurrection

Warning - USB devices sometimes manage to remain alive after a cold restart.
Why? Due to ATX “+5 V standby” which some of the USB connectors tend to use.
For a more divine provisioning, a separate PSU would be needed (via USB hub).
