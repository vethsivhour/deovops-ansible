# Ansible Docker Environment

This project demonstrates a simple Ansible setup using Docker containers, where one container acts as the Ansible control machine and another as the target server.

## Project Structure

```
.
├── ansible/
│   ├── inventory.ini    # Ansible inventory file
│   └── playbook.yml     # Ansible playbook
├── server1/             # Mount point for server1
├── docker-compose.yml   # Docker Compose configuration
└── README.md           # This file
```

## Prerequisites

- Docker
- Docker Compose

## Setup

1. Clone this repository:
```bash
git clone <repository-url>
cd <repository-name>
```

2. Start the containers:
```bash
docker-compose up -d
```

This will create two containers:
- `control-machine-i4d`: Debian-based container with Ansible installed
- `server1-i4d`: Alpine-based container acting as the target server

## Configuration

### Control Machine
- Uses Debian as base image
- Has Ansible and sshpass installed
- Mounts the `ansible` directory for playbooks and inventory

### Target Server
- Uses Alpine Linux as base image
- Has OpenSSH installed
- Root password is set to `P@ssw0rd1`
- SSH is configured to allow root login

## Ansible Setup

### Inventory
The `inventory.ini` file contains:
```ini
[myservers]
server1-i4d ansible_host=server1-i4d ansible_user=root ansible_ssh_pass=P@ssw0rd1 ansible_port=22
```

### Playbook
The `playbook.yml` includes tasks to:
- Ping the server
- Update package cache
- Install additional packages (curl, vim, htop)
- Create test directories and files
- Display system information

## Running Ansible

To run the Ansible playbook:

```bash
docker exec -it control-machine-i4d sh -c "cd /ansible && ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook playbook.yml -i inventory.ini"
```

## Security Notes

- This setup is for demonstration purposes only
- Root SSH access is enabled (not recommended for production)
- Host key checking is disabled (not recommended for production)
- Passwords are stored in plain text (not recommended for production)

## Troubleshooting

If you encounter issues:

1. Check if containers are running:
```bash
docker-compose ps
```

2. Verify SSH access:
```bash
docker exec -it control-machine-i4d sh -c "sshpass -p 'P@ssw0rd1' ssh -o StrictHostKeyChecking=no root@server1-i4d 'echo Connection successful'"
```

3. Check SSH service on server:
```bash
docker exec -it server1-i4d sh -c "ps aux | grep sshd"
```

4. Reset server configuration:
```bash
docker-compose restart server1-i4d
docker exec -it server1-i4d sh -c "echo 'root:P@ssw0rd1' | chpasswd && sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && /usr/sbin/sshd"
```

## Cleanup

To stop and remove all containers:
```bash
docker-compose down
``` # deovops-ansible
