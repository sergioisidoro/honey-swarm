# HONEY SWARM 🐝
A production ready, secure, batteries included docker swarm self hosted PaaS (Platform as a service) with portainer and traefik

**This is a work in progress**
# The vision
You can build an entire productivity and startup stack in a few minutes and under 50Eur/month, with a few VPS on DigitalOcean / Hetzner, etc. Services are self hosted, privacy aware, secure, and compliant with most data storage regulations.

## Features 
- Setup python, ansible and SSH users with Public key for all nodes
- Setup basic ufw and fail2ban with a reasonable set of rules for web deployments
- Setup Docker + Docker swarm with multiple nodes
- Portainer agent + Traefik for management of the stack
- A set of Portainer apps with Traefik routes.
  - analytics
  - gitlab runner
  - mastodon social media

## We are built on the shoulders of Giants!
This is heavily inspired and a lot of things replicated from [TADS Boilerplate project](https://github.com/thomvaill/tads-boilerplate), [swarmlet](https://swarmlet.dev/) and other projects like Dokku, and CapRover.

## Important notes
- `public-network` is the network all services need to be if you want them public so Traefik can find them.
- It is strongly advised to put Portainer behind a VPN or internal network. To block access from the internet, you can add `internal_services_ip_whitelist` to the variables, allowing only certain IPs.
- Remember to put annotations on the `deploy` section so that Traefik can find your services across all nodes.
- By default all non necessary incoming ports are CLOSED in the firewall. SSH is limited. If you want to open other ports
  - set `default_ufw_default_inbound_rule: 'allow'` on your cluster variables to accept traffic from any port - SCARY
  - You can add more ports (eg. if your containers and services are using some other ports) with `ufw_extra_rules` variable. See `roles/hardening/tasks/ufw.yml` for more technical details. - BETTER

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
Make a copy of the `cluster_template` in `ansible` folder, and give it an appropriate name for your `ENVIRONMENT`. ENVIRONMENT can bet the name of the environment, or the name of the cluster

Add all your servers to `ansible/clusters/ENVIRONMENT/inventory` file, following the suggested structure.

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
Change the necessary paramters in `ansible/clusters/ENVIRONMENT/group_vars/all.yml`. Most importantly:
```
domains:
  main: "your-project.com"
```

Make sure the lets encrypt email is valid otherwise you might not be able to issue and renew the certificates.

**If you want to use Portainer Enteprise edition, or customize the portainer images** you should set the variables in the 'all.yml' for the appropriate images. If you chose the EE edition, make sure you have a valid license.

```
portainer_image: "portainer/portainer-ee"
portainer_agent_image: "portainer/agent"
```

## (OPTIONAL) Add the users with ssh access to nodes 🤓
Sometimes you want to allow other developers to access the nodes through SSH. To do this, do the following:

- add the users to the `users:` variable in `all.yml` like so:
```
users:
 - user_1
 - honey
```

and add their **public** key as a file name `<user>` to `ansible/pubkeys`. So if the user name is `honey` add a file called `honey` containing their public ssh keys

## (OPTIONAL) Store all your docker data in your cloud provider storage 📚
If you are using Digital Ocean or Hetzenr, it is possible you might want to store all your docker data in their detachable volumes to facilitate snapshots, backups and duplication. To do so, all you need to do is mount those volumes 

```.yaml
digital_ocean_volumes:
  - volume_id: scsi-0DO_Volume_something-something
    mount_point: /mnt/docker-data-external-volume

## OR

hetzner_volumes:
  - volume_id: scsi-0HC_Volume_1234567
    mount_point: /mnt/docker-data-external-volume
```

using the volume ids specified by the cloud provider, and then use the `data-root` docker daemon configuration settings to point to the mounted location:
```.yaml
docker_daemon_options:
  data-root: /mnt/docker-data-external-volume
```

**NOTE: to have different volumes per node, use `host_vars`, and not `group_vars/all.yml`**

## Launch your fabulous 💅 stack!
```
ansible-playbook -i ansible/clusters/ENVIRONMENT ansible/provision.yml 
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
* traefik.yourdomain.com (optional)

# Todos ("Pull requests welcome") ☑️
- [ ] Add additional core features, like node monitoring with node exporter / prometheus
- [ ] Add more app templates