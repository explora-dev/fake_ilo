fake_ilo
========

iLO emulator which can power cycle libvirt VMs. It was written by me to perform tests of oVirt/RHEV Power Management features by installing hosts as libvirt VMs and querying/fencing them using this script for demo purposes.

The script is installed on a libvirt host, inside which an oVirt cluster is deployed as VMs. I
See the below picture as an example:

```
----------------------------------------------------------
| --------------  ---------------  ----------------      |
| |oVirt-host1 |  | obirt-host2 |  | ovirt-engine | -    |
| --------------  ---------------  ----------------  |   |
|       |             |              |               v   |
|       #### KVM host with libvirt ####  <----  fake_ilo |
----------------------------------------------------------
```

You do not install the script directly on oVirt nodes you are testing - it needs to control the "hardware" of this nodes, which in this case is libvirt+KVM.

DISCLAIMER:
-----------
This script was made for testing purposes only with no security in mind. The quality of code may be dangerous for mental health of the observer. Use at your own risk.

INSTALL:
--------

1. copy ilo.py to /usr/local/bin/  and make it executable
2. copy fake_ilo to /etc/init.d/ and make it executable
3. create configuration directory:

 `# mkdir /etc/fake_ilo/`

4. go into the config directory and generate a self-signed certificate by running this command. Data provided in the requested fields is not important: oVirt does not verify certificates:

 `# cd /etc/fake_ilo/`

 `# openssl req -x509 -newkey rsa:2048 -keyout server.key -out server.crt -nodes -days 9999`
5. register the init script (Debian):

 `update-rc.d fake_ilo enable`
 
USAGE:
------
 
 The script starts listening on port 1234 and uses the username sent from the oVirt fencing agent as the name of the VM to query/fence. The password is not important. When a username is not equal to any VM name on the machine, "NaN" is returned, which should produce an error on oVirt side. Logs are written to /var/log/fake_ilo.log
 
 Most of the settings are hardcoded, but the code is pretty simple so they can be changed as needed.
