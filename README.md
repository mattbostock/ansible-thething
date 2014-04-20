# Ansible configuration for thething.mattbostock.com

This the [Ansible](http://www.ansible.com/home) configuration for my
VM, thething.mattbostock.com.

'The Thing' is a reference to the [excellent 1982 film](http://www.imdb.com/title/tt0084787/)
of the same name.

It's also my first foray with Ansible.

## What it does

The playbooks here:

- Provision a new VM from [Digital Ocean](https://www.digitalocean.com/)
- Do some basic configuration of Ubuntu for security
- Configure an instance of the [ZNC IRC bouncer](http://wiki.znc.in/ZNC)
- Configure an instance of [Ghost](https://ghost.org/), the blogging
platform

Each daemon runs inside a [Docker](https://www.docker.io/) container.

Note that I'm fronting Ghost with [Fastly](http://www.fastly.com/) CDN
so haven't opted to proxy it through a web server for simplicity.

## Configuration

I've made use file lookups and vars_prompt to pull in configuration
variables, such as:

- A ZNC username and password
- An SSL PEM file for ZNC

The main reason for this is that it allows me to host this repo publicly
without exposing any secrets. I also don't expect to need to run this
playbook often enough that entering these variables would be a hassle.

## How to run

This repo relies on [librarian-ansible](https://github.com/bcoe/librarian-ansible)
to pull in external playbooks. Unfortunately librarian-ansible is
written in Ruby rather than Python (Ansible uses Python), so I recommend
using [Bundler](http://bundler.io/) to install the librarian-ansible
gem.

There are two playbooks used; `provision.yml` creates and bootstraps the
Digital Ocean VM and `site.yml` installs the services. These need to be
run separately, to allow the Digital Ocean [inventory script](hosts/digital_ocean.py)
to refresh the list of hosts once the VM has been created.

```
pip install -r requirements.txt
bundle install
librarian-ansible install
export DO_API_KEY=<Digital Ocean API key here>
export DO_CLIENT_ID=<Digital Ocean client ID here>
ANSIBLE_ROLES_PATH=librarian_roles/ ansible-playbook -i hosts provision.yml
ANSIBLE_ROLES_PATH=librarian_roles/ ansible-playbook -i hosts site.yml
```

## TODO

- Restrict Ghost on port 80 to Fastly edge nodes (AKA dark origin)
- Configure bitlbee and Campervan for ZNC
- Figure out how to run provision.yml and site.yml together in one run
