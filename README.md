# DevOps-project-1
System: Linux Mint 21 (ubuntu-22.04 kernel)
## Create VMs
Install vagrant and virtualbox (Vagrant is a tool to manage VM-based environments. Vagrant boxes are prebuilt base images that can be imported 
into Vagrant). In this project, use the file named Vagrantfile to create 3 VM's. Then run the command:

```
vagrant up 
```

This file Vagrantfile will be supplied by default to the command.


Note: you can use Vagrant with provisions to use a different file than the default one. Each VM has its own file system. To allow for name 
resolution edit the file /etc/hosts/ of the control VM.

Firstly you will have to ssh to control VM from the command line
```
vagrant ssh 192.168.56.9
```

then eidt /etc/hosts
```
nano /etc/hosts
```
then add the following lines (for this specific project)

``` 
192.168.56.9	control 
192.168.56.10	node1
192.168..56.11	node2
```

To check name resolution ping to any VM:
```
ping node1
```

## Use Ansible 
Ansible is a suite of software tools that enables infrastructure as code. It is open-source and the suite includes software provisioning, 
configuration management, and application deployment functionality. Ansible has two formats of its files:
- Generic formats used for what is called Ansible inventory file in which we define the VMs to be communicated with.
- .yml format used for ansible playbook files.

First you will need to create myhosts file (see ansible directory in this repository)
```
mkdir ansible
cd ansible/
```

To make all nodes echo their names on command console:

```
ansible nodes -i myhosts -m command -a hostname
```

To install python on all nodes:

```
ansible nodes -i myhosts -m command -a 'sudo apt-get -y install python-simplejson'
```

You can install docker on all VMs:

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

To check, run:

```
docker run hello-world
```

It is apparant it becomes rather easy to install applications on multiple VMs using ansible.

## Containerizing application with Docker
It is very common that every application or service is containerized now. You don't install bare application software or services anymore. The applicationin this lab is a simple application that prints out the host name when prompted. The application is a simple Flask application (Flask is a light weight web framework) and is saved in app.py (see docker directory in this repository).

### Scenario 1: running application under docker-compose
![mod1](https://github.com/Sherif-Elshafei/DevOps-project-1/assets/4324447/00b21da6-a453-48bf-b89b-1e35daaa4402)

We use a docker compose file named docker-compose.yml (see docker directory in this repository). To make a node execute the application under docker-compose:

```
cd docker
docker-compose up
```

this will supply the file docker-compose.yml

### Scenario 2: running application under docker swarm orchestration
![mod2](https://github.com/Sherif-Elshafei/DevOps-project-1/assets/4324447/9d3c1672-514f-4578-beb8-090697072a13)

We can make nodes join a docker swarm orchestration (basically to control more than one server running the application). First swarm has to be created and nodes joined. 

```
ansible-playbook -i myhosts -K swarm.yml
```

then a swarm node can run the application:

```
docker stack deploy --compose-file docker-compose.yml myapp
#myapp is the application name
```

this will be associated with modification to docker-compose.yml file. For running a containerized application under docker-compose is different from running the application as a member of docker swarm.

To check what is running, go to control station and run the command
```
curl node1:5000
#curl node2:5000 #to check what is running on node2
```

## Some useful docker command
- To find out which containers are running
```
docker ps
```

- To find out which nodes are connected to a swarm
```
docker node ls
```

- To remove a node from a swarm
```
#from the node itself run this:
docker swarm leave
```

- To run a container from a node under docker-compose
```
#from the node itself run this:
docker-compose up
```

- To stop a node running an application under docker-compose
```
#from the node itself run this:
docker-compose down
```

- To see which swarm nodes are running *myapp*
```
docker stack services myapp
```

- To display the specifics of the created services *myapp-web*
```
docker service ps myapp-web
```

- To create replicas of a service *myapp-web* (note: replicas will be balanced)
```
docker service scale myapp-web=6
```



