# servers.com role

Currently implemented:
- inventorizing
- OS installation

# Usage

You have to define ```servers_com_username``` and ```servers_com_password```, for some additional vault support check out [this](https://github.com/gitinsky/vaultkeychain) repo.
I recommend adding localhost to hosts and running this role on this host:

```yml
- hosts: localhost
  become: no
  gather_facts: no
  roles:
      - role: servers.com
      servers_com_inventorize: yes
      servers_com_reinstall: test.example.org
```

## Inventorizing

Set ```servers_com_inventorize``` to ```true``` and run the role. Local ansible.cfg file with inventory location is required. Inventory has to be a directory.

- Role will create a ```01_servers.com.yml``` inside your inventory directory and put all the discovered hosts to it in a ```[servers.com]``` group. You cane change this file name with ```servers_com_inventory_name``` variable
- For each host a directory named by host title will be created in host_vars. It will contain ```servers_com_info.json``` with some usefull facts. In case you've already had files for these hosts in host_vars your inventory could get broken as ansible doesn't allow using both folder and file as host_vars, simply move your file to the new directory to fix

## OS installation

To order OS installation you have to prepare ```install_config``` dictionary first. To do so prepare your installation in an admin web interface but before firing the installation open debug console in your browser. Once you order the installation this request should appear in the network section of the console. Copy it ```as curl``` and grab json from the ```data-binary``` parameter, it should start like this: ```--data-binary '{"data":{"disks":[{"disks...````. Rename the ```data``` key to ```install_config``` and put this json to you host_vars or group_vars. This file has to have ```.json``` extension, for example ```host_vars/test.example.org/install.json``` and start like this:

```json
{
    "install_config": {
        "disks": [
            {
```

That's it, now just set ```servers_com_reinstall``` to the name of the server and run your playbook. If you didn't run role for inventorizing earlier it will run these tasks as well to find out id of your server. I recommend commenting out ```servers_com_reinstall``` variable after successful ordering of installation to avoid accidental reinstallations.
