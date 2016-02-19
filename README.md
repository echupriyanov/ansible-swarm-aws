This set of playbooks is used to deploy a Swarm cluster in AWS together with Flocker as volume plugin and using etcd as KV store for Docker and Swarm

*** WORK IN PROGRESS ***

Disclaimer: this is rather messy pile of playbooks, but they do their work, though setup if not really functional.
I've opened 2 issues in Githubm regarding this setup:
* https://github.com/docker/compose/issues/2958
* https://github.com/docker/swarm/issues/1847

One more issue is steady growth of opened flocker.sock file on nodes with swarm agent running, but I will do some more research before reporting.

Versions used:
* Ubuntu 14.04.4
* Docker Engine 1.10.1
* etcd 2.2.5
* Docker Compose 1.6
* Flocker 1.10.2

Quick notes

Preparation
* set AWS region and ssh keyname in group_vars/all.yml
* put AWS access and secret keys for Flocker in ansible-vault protected vars file in roles/docker-noda/defaults/secretvars.yml like this:
```
---
  aws_accesskey: AKIAKAIAKAI
  aws_secretkey: 37780ea436530e0b6053f7f1380e9c327bb8a6dc
```

Now everything should be ready to deploy.


Order of execution:

1. Initialize cfssl-based PKI in cfssl folder
  1. `ansible-playbook initcfsslca.yml`
1. Set up etcd cluster (3 nodes)
  1. `ansible-playbook etcd-create.yml`
  1. `ansible-playbook etcd-setup.yml`
1. Set up Flocker control node (1 node)
  1. `ansible-playbook flocker-create.yml`
  1. `ansible-playbook flocker-setup.yml`
1. Set up Swarm master nodes (2 nodes)
  1. `ansible-playbook swarm-masters-create.yml`
  1. `ansible-playbook swarm-masters-setup.yml`
1. Set up Swarm worker nodes (2 nodes)
  1. `ansible-playbook swarm-nodes-create.yml`
  1. `ansible-playbook swarm-nodes-setup.yml`

At this point everything should be up and running.
