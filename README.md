# Vagrant Load Balancer Setup

This project creates a simple load balancing environment using Vagrant and VirtualBox. It sets up three virtual machines: two Apache web servers and one Nginx load balancer.

## Infrastructure Overview

- **Load Balancer**: Nginx server that distributes traffic between two web servers
  - IP: 192.168.56.100
  - Accessible from host at: http://localhost:8080

- **Web Server 1**: Apache server with custom content
  - IP: 192.168.56.101

- **Web Server 2**: Apache server with custom content
  - IP: 192.168.56.102

## Requirements

- [Vagrant](https://www.vagrantup.com/downloads)
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

## Setup Instructions

1. Clone this repository:
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```

2. Start the virtual machines:
   ```bash
   vagrant up
   ```

3. Access the environment:
   - Visit http://localhost:8080 in your browser to access the load-balanced environment
   - The load balancer will distribute requests between the two web servers

4. Customize web content:
   - Add your HTML files to the `./web_content` directory which is synced to `/var/www/html` on both web servers

## VM Details

All VMs use Ubuntu 20.04 (Focal Fossa) with 512MB RAM and 1 CPU each.

## Management Commands

- Start all VMs: `vagrant up`
- Start a specific VM: `vagrant up <vm-name>` (e.g., `vagrant up lb`)
- SSH into a VM: `vagrant ssh <vm-name>` (e.g., `vagrant ssh web1`)
- Stop all VMs: `vagrant halt`
- Delete all VMs: `vagrant destroy`

## Configuration Details

The Vagrantfile configures:
- Shared folders between host and guest machines
- Network settings for internal communication
- Automatic provisioning of web servers and load balancer
- Custom HTML content for each server