# HONEY SWARM 🐝
A production ready, secure, batteries included docker swarm self hosted PaaS (Platform as a service) with portainer and traefik

**This is a work in progress**
# The vision
You can build an entire productivity and startup stack in a few minutes and under 50Eur/month, with a few VPS on DigitalOcean / Hetzner, etc. Services are self hosted, privacy aware, secure, and compliant with most data storage regulations.

### We are built on the shoulders of Giants!
This is heavily inspired and a lot of things replicated from [TADS Boilerplate project](https://github.com/thomvaill/tads-boilerplate), [swarmlet](https://swarmlet.dev/) and other projects like Dokku, and CapRover. Anf

## Important notes
- `public-network` is the network all services need to be if you want them public so Traefik can find them.
- Remember to put annotations on the `deploy` section so that Traefik can find your services across all nodes.

# 🚀 Quickstart
- Install Ansible on your machine
- Clone this repository
- Create your servers
- Add your servers to the inventory
- Configure your parameters
- Deploy the stack
- Secure your installation.

## Pre-requisites 📦 
* You have Python3 installed on your local machine
* You have git installed on your local machine

## Install ansible on your local machine 📡 
For all users, do `sudo pip3 install ansible`, or if you want to install it only for your user, do `pip3 install ansible --user`


## Duplicate this template / fork it and clone it ↔️
`git clone https://github.com/sergioisidoro/honey-swarm`

## Create your servers 💾 
Go to your favorite cloud provider, and setup a few Virtual private servers.
* Add your public SSH key to the servers so you can access it
* Save each server username and IP address (and/or internal IP)

## Add your servers to the inventory 🗄
Add all your to `ansible/inventories/ENVIRONMENT`, where ENVIRONMENT can bet the name of the environment, or the name of the cluster

```
node-1 ansible_user=root ansible_host=NODE_IP ansible_port=22 dockerswarm_advertise_addr=<INTERNAL_IP>

node-2 ansible_user=root ansible_host=NODE_IP ansible_port=22 dockerswarm_advertise_addr=<INTERNAL_IP>
```

Add at least one "manager nodes" in as is instructed. 

```
[dockerswarm_manager]
node-1
```

If you want the docker nodes to "talk" to each other only in the internal network, make sure you set the `dockerswarm_advertise_addr` to the private IP address instead of the public, and make sure the servers are in the same network and can talk to each other.

## Configure your parameters 📐
Change the necessary paramters in `ansible/group_vars/ENVIRONMENT.yml`. Most importantly:
```
domains:
  main: "your-project.com"
```

Make sure the lets encrypt email is valid otherwise you might not be able to issue and renew the certificates.

## (OPTIONAL) Add the users with ssh access to nodes 🤓
Sometimes you want to allow other developers to access the nodes through SSH. To do this, do the following:

- add the users to the `users:` variable like so:
```
users:
 - user_1
 - honey
```

and add their **public** key as a file name `<user>` to `ansible/pubkeys`. So if the user name is `honey` add a file called `honey` containing their public ssh keys

## Launch your fabulous 💅 stack!
```
ansible-playbook -i ansible/inventories/ENVIRONMENT ansible/provision.yml 
```

This will run the entire setup across all nodes.

## IMPORTANT - Go to portainer and create your admin password 🔐
Go to `portainer.<YOURDOMAIN>` and create an administrator user for the platform. If you don't do this within 5 minutes portainer container will automatically shutdown for security reasons and you'll have to restart the service manually

## (OPTIONAL) Add Honey swarm Portainer templates to portainer settings
The aim of this project is to create a set of pre-made tools to build your startup / project. So it comes with a few pre-made apps that you can use with Portainer app templates.

Go to settings -> And set "App Templates" URL to `https://raw.githubusercontent.com/sergioisidoro/honey-swarm/master/portainer/portainer-templates.json` (or equivalent to your cloned repository) to have access to the pre-made templates for apps.

## Enjoy ✅
User your deployed services:
* portainer.yourdomain.com
* traefik.yourdomain.com

# Todos ("Pull requests welcome") ☑️
- [ ] Add additional core features, like monitoring with node exporter / prometheus
- [ ] Secure Traefik dashboard behind HTTP auth
- [ ] Add more app templates