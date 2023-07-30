# DevOps-project-1
System: Linux Mint 21 (ubuntu-22.04 kernel)
## Create VMs
Install vagrant and virtualbox. Edit the file named Vagrantfile to create 3 VM's. This file will be supplied by default to the command: 
``` vagrant up ``` 

Note: you can use Vagrant with provisions to use a different file than the default one. Each VM has its own file system. To allow for name 
resolution edit the file /etc/hosts/ of the control VM by adding <IP of VM> <Name of VM>.

Firstly you will have to ssh to control ```vagrant ssh 192.168.56.9```
then ```nano /etc/hosts/```
then add the following lines (for this specific project)

``` 
192.168.56.9	control 
192.168.56.10	node1
192.168..56.11	node2
```

