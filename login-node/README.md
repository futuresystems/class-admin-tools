# Description

This stands up a login node in an OpenStack project.


# Motivation

In the case that there is a limit of floating ips available, standing up a login node within a network allows only a single floating ip tobe used.
Initially, this came about for the FutureSystems fg491 when attempting to transition some users to the Chameleon Cloud.


# Requirements

- [ldapget](github.com/futuresystems/ldapget)
- Python
- Ansible


# Usage

1. use `ldapget` to download the public keys for the students in the project.
   eg. `ldapget --host localhost --port 9389 --project fg491 -outdir public_keys`

1. start an instance on OpenStack

   ```
   nova boot \
     --image $IMAGE \
     --flavor m1.small \
     --key-name $HOSTNAME \
     --nic net-id=$(nova network-list | grep "${OS_PROJECT_NAME}-net" | awk '{print $2}') \
     $NAME
   ```

   where:
   
   - `$IMAGE` is appropriately replaced with a CentOS7 image.
   - `$HOSTNAME` is the key name (I name my keys by the hostname of the machine on which they reside)
   - `$NAME` is the instance name, eg `login`
   
1. assign a floating ip

1. add the floating ip to the inventory:

   ```
   echo $FLOATING_IP >inventory
   ```
   
1. run ansible to deploy

   ```
   ansible-playbook site.yml -l $CLOUD_USER
   ```
   
   where `$CLOUD_USER` is the username to use to log into the VM.
