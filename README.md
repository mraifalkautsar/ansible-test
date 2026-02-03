This repository is made for MrScraper DevOps Technical Test purpose.

## Prerequisites

- Ansible installed
- Access to the target servers with the SSH key specified in `inventory.ini`.

## Usage

To run the main playbook, execute the following command:

```bash
ansible-playbook site.yml
```

## Project Structure 

### Inventory

*   `inventory.ini`: This file defines the servers that Ansible will manage. It lists the IP address and connection user for the application server(s) under the `[app_servers]` group. It also sets global variables, such as the path to the SSH private key and the remote Python interpreter, which are used for all servers.

### Playbook

*   `site.yml`: This is the master playbook that orchestrates the entire deployment. It specifies that the `app_servers` are the target hosts and that the deployment should be executed with elevated privileges (`become: yes`). It then applies the `runtime` and `app` roles in that order.

### Roles

The playbook is divided into several roles for organization:

1.  **`runtime` Role (`roles/runtime/tasks/main.yml`)**
    The tasks of this role:
    * Update the `apt` package cache.
    * Install necessary dependencies like `curl` and `gnupg`.
    * Add the NodeSource repository, which is a common way to install specific versions of Node.js.
    * Install Node.js itself.
    * Install PM2, a popular process manager for Node.js applications, which keeps the application running.
    * Configure PM2 to automatically start the application on server boot.

2.  **`app` Role (`roles/app/tasks/main.yml`)**
    The tasks of this role:
    * Creating a directory on the server for the application code.
    * Cloning the application's source code from a Git repository.
    * Creating a PM2 `ecosystem.config.js` file from a template. This file tells PM2 how to run the application (e.g., what script to start).
    * Installing the application's Node.js dependencies using `npm`.
    * Starting or restarting the application using PM2.
    * Saving the current PM2 process list so that it will be resurrected on reboot.