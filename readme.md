# Introduction to Network Automation with Ansible
Network automation allows you to configure devices faster and with less misconfigurations. Automation also allows for repeatable configurations, letting you write a 
common configuration once and use it in multiple places. Ansible addresses those needs by allowing you to write configurations for almost any device with YAML, a human
readable object notation language. Ansible lets you write easy to read configurations once, and apply them to different devices. 
# Overview of Ansible
Ansible is a open-source automation tool designed for simplifying IT operations. It is agentless, meaning it doesn't require any software on devices that you want to apply
configurations to. Playbooks are consistent regardless of the initial configurations, meaning that it is easy to apply a configuration to a new device, and easy to perform repeated actions across many devices.
# Ansible Playbooks
I developed 5 playbooks for the CSR1000v to automate simple tasks and experiment with Ansible's features.
## Set Hostname
This Ansible playbook prompts the user for a hostname, and will set the host's hostname accordingly. This playbook was my first test of user input in Ansible, for something simple. This worked well, and allowed me to set the hostname of the CSR1000v at will.
## Add User
This Ansible playbook prompts the user for a username and password, and sets up a new user with the provided details. This is an extension of my first user input test, getting 2 inputs instead of one, and using them both in the same command.
## DHCP Server
This Ansible playbook sets up a DHCP pool on the router including setting up a default gateway and DNS server. This ansible playbook can completely set up DHCP for the router, allowing hosts to start connecting to it immediately.
## MOTD Banner
This Ansible playbook uses another task I found, called `ios_banner`, that allows you to configure the banner message of a Cisco device. I used this to set up a simple playbook that sets the banner message to a generic one that I wrote for this lab.
## Setup Router
This Ansible playbook is used to set up a router with some company defaults, such as a default hostname, MOTD, administrators, DNS server, and domain name. This ansible playbook is intended to be a real world showcase of ansible's power, allowing you to set a constant config easily.
# Automation in Action
## Set Hostname
```r
Hostname: R1

PLAY [Configure CSR1kv] *******************************************************************************
TASK [Configure hostname] *******************************************************************************
changed: [CSR1kv]

PLAY RECAP *******************************************************************************
CSR1kv
ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```
Ansible simplifies the configuration by not having to SSH into the router and get into config mode. You can just run the playbook and provide it the hostname you want to have it configure the router.
## Add User
```r
Username: Jim
Password: 

PLAY [Configure CSR1kv] *******************************************************************************

TASK [Configure hostname] *******************************************************************************
changed: [CSR1kv]

PLAY RECAP *******************************************************************************
CSR1kv
ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
This makes it significantly simpler to add users to the Cisco IOS internal database, letting you just punch in the users and it completes. This could even be made into a bash alias, allowing admins to add users to their routers from the commandline. You can even see that the password was obscured, allowing for secure configurations.
## DHCP Server
```r
PLAY [Configure DHCP Server on CSR1kv] *******************************************************************************

TASK [Create DHCP Pool] *******************************************************************************
ok: [CSR1kv]

PLAY RECAP *******************************************************************************
CSR1kv
ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
This playbook makes it much easier to set up a dhcp server on the desired routers. It will also properly check the config, and make sure that if it is already set up, it does not re-run the configuration commands.
## MOTD Banner
```r
PLAY [Configure Banner Message] *******************************************************************************

TASK [Configure MOTD Banner] *******************************************************************************
changed: [CSR1kv]

PLAY RECAP *******************************************************************************
CSR1kv
ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
This makes setting up and maintaining an industry standard MOTD incredibly easy, and allows you to ensure that all of your devices have the same MOTD.
## Setup Router
```r
PLAY [New Router Configuration] *******************************************************************************

TASK [Set Company Default Hostname] ***************************************************************************

changed: [CSR1kv]

TASK [Configure Default MOTD Banner] **************************************************************************
changed: [CSR1kv]

TASK [Configure Admin Users] *******************************************************************************
changed: [CSR1kv]

PLAY [New Router DNS Config] *******************************************************************************

TASK [Set Company DNS server] *******************************************************************************
ok: [CSR1kv]

TASK [Set Company Domain Name] *******************************************************************************
changed: [CSR1kv]

PLAY RECAP *******************************************************************************
CSR1kv
ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
This configuration makes it incredibly easy to put newly acquired devices into a 'configuration' state, where some select admins have access to it and it has a preconfigured setup. This allows you to let admins work on devices as they come in, after a base configuration is applied to it. This can make specific configuration much faster, as common configuration doesn't have to be done.
# Challenges and Lessons Learned
Some challenges I had were with the DHCP server configuration. I couldn't figure out why including all the lines in the `ios_config` task wouldn't work, but I realized I needed to use the parent feature of the `ios_config` task to get it to properly lookup and apply the commands to the routers configuration. From the [documentation](https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_config_module.html#parameter-parents), it seems like this would only affect the idempotency of the ansible playbook, but it gave me an error when it was all in the `lines` of the task.
# Recommendation for Moving Forward with Ansible
I would recommend moving forward with Ansible, as it vastly simplifies device configuration, and allows you to configure almost any device. This automation tool can also help prevent misconfigurations before they happen, and allow you to copy configurations across devices. 