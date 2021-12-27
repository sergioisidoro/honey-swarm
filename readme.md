# HONEY SWARM 🐝
A production ready, secure, batteries included docker swarm self hosted PaaS (Platform as a service) with portainer and traefik

**This is a work in progress**
# The vision
You can build an entire productivity and startup stack in a few minutes and under 50Eur/month, with a few VPS on DigitalOcean / Hetzner, etc. Services are self hosted, privacy aware, secure, and compliant with most data storage regulations.

### We are built on the shoulders of Giants!
This is heavily inspired and a lot of things replicated from [TADS Boilerplate project](https://github.com/thomvaill/tads-boilerplate), [swarmlet](https://swarmlet.dev/) and other projects like Dokku, and CapRover.

## Important notes
- `public-network` is the network all services need to be if you want them public.

# Quickstart

- Install Ansible on your machine
- Clone this repository
- Create your own inventory 
- Add your inventory 
- Configure your parameters
- Deploy the stack


## Pre-requisites
* You have Python3 installed on your local machine
* You have git installed on your local machine

### 1 - Install ansible on your local machine
```
sudo pip3 install ansible
``` 
For all users, or 
```
pip3 install ansible --user
```
only for you

### 2 - Fork this, and clone your fork, or just clone this!
`git clone https://github.com/sergioisidoro/honey-swarm`

### 3 - Create your own inventory 
Go to your favorite cloud provider, and setup a few Virtual private servers.
Save their IP (and/or internal IP) for later, and make sure your local machine
can access those servers with your private key

### 4 - Add your inventory 
Add at least one "manager nodes" in `ansible/inventories/ENVIRONMENT` as is instructed.

### 5 - Configure your parameters
Change the necessary paramters in `ansible/group_vars/ENVIRONMENT.yml`. Most importantly:
```
domains:
  main: "your-project.com"
```
#### 6 - Add the users who are supposed to have access to the nodes:
- add the users to the `users:` variable like so:
```
users:
 - honey
```

and add their **public** key as a file name `<user>` to `ansible/pubkeys`. So if the user name is `honey` add a file called `honey` containing their public ssh keys

## 6 - Launch your fabulous 💅 stack!
```
ansible-playbook -i ansible/inventories/ENVIRONMENT ansible/provision.yml 
```

## 7 - IMPORTANT - Go to portainer and change your admin password
Go to `portainer.<YOURDOMAIN>` and create an administrator platform

# Todos ("Pull requests welcome")
- [ ] Make the inventory creation automatic with terraform in multiple cloud providers
  - [ ] DigitalOcean
  - [ ] Hetzner

- [ ] Add additional core features, like monitoring with netdata or grafana
- [ ] Secure Traefik dashboard behind HTTP auth