## An automated way of building an Ansible testing laboratory, so you don't have to waste time manually setting up everything. Based on docker. <br>

---
By default it creates 3 containers, one host container where we have Ansible setup and two secondary containers where the commands/playbooks will be performed on. The base image for all containers is Ubuntu and the containers are named the following:
- **ansible-host** - Container where we have Ansible installed
- **ansible-testing-secondary1** - First blank container with Ubuntu installed and some additional packages necessary for the usage
- **ansible-testing-secondary2** - Second blank container with Ubuntu installed and some additional packages necessary for the usage

---
### Setup:
1. Make sure you have docker installed, if not you can download it from [here](https://www.docker.com)
2. Download the `docker-compose.yml` file into your local device.
3. Start the containers by running the following command:
```shell
docker compose up -d
```
4. Start the ssh service on the secondary containers using the following commands:
```shell
docker exec -it ansible_secondary1 service ssh start
docker exec -it ansible_secondary2 service ssh start
```
*Note: In the future I will update the files so that the ssh service starts automatically*

5. Login into the Ansible container using the following command:
```shell
docker exec -it ansible_host bash
```
You can find the inventory file all build up and located in `/root` (*it is called `inventory`*)<br>

An easy way of making sure if everything is properly setup is by going into the `/root` directory and running the following ansible command which will use the ping module on the secondary containers to make sure everything is okay:
```shell
root@ansible-testing-host:~# ansible all -m ping -i inventory
secondary_one | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
secondary_two | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

---
### Adding additonal secondary containers:
If you wish to add extra secondary containers simply modify the `docker-compose.yml` file by copying the following part and increasing the numbers:
```yml
secondary2:
     image: ktonchekliev/kosta:secondary
     container_name: ansible_secondary2
     hostname: ansible-testing-secondary2
     tty: true
```
Example:
```yml
secondary3:
     image: ktonchekliev/kosta:secondary
     container_name: ansible_secondary3
     hostname: ansible-testing-secondary3
     tty: true
```