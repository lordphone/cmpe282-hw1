# CMPE 282 - Homework 1: Ansible Webserver Deployment

## Assignment Overview
This project demonstrates the deployment of Apache webservers on two VMs (VM1 and VM2) using Ansible automation. Each VM hosts a web page on port 8080 displaying "Hello World from SJSU-1" or "Hello World from SJSU-2" respectively.

## Project Structure
```
hw1/
├── ansible.cfg                          # Ansible configuration
├── inventory/
│   └── hosts                            # Inventory file defining VM1 and VM2
├── templates/
│   ├── index.html.j2                    # HTML template for web pages
│   ├── apache-8080.conf.j2              # Apache config for Ubuntu/Debian
│   └── apache-8080-redhat.conf.j2       # Apache config for RedHat/CentOS
├── deploy-webserver.yml                 # Main deployment playbook
├── undeploy-webserver.yml               # Undeployment playbook
└── README.md                            # This documentation
```

## Prerequisites

### 1. Virtual Machines Setup
- **VM1** and **VM2** running Ubuntu 20.04+ or CentOS 7+
- SSH access configured with key-based authentication
- VMs should have internet connectivity for package installation

### 2. Ansible Control Machine
- Ansible 2.9+ installed
- SSH keys configured for passwordless access to VMs
- Python 3.6+ installed

### 3. Network Configuration
- Ensure port 8080 is accessible from your browser to both VMs
- If using cloud VMs, configure security groups/firewall rules accordingly

## Setup Instructions

### Step 1: Configure VM Access
1. **Update the inventory file** (`inventory/hosts`):
   ```ini
   [webservers]
   vm1 ansible_host=YOUR_VM1_IP ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/your_private_key
   vm2 ansible_host=YOUR_VM2_IP ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/your_private_key
   ```

2. **Replace the following placeholders**:
   - `YOUR_VM1_IP`: IP address of VM1
   - `YOUR_VM2_IP`: IP address of VM2
   - `ubuntu`: Username for SSH access (could be `centos`, `ec2-user`, etc.)
   - `~/.ssh/your_private_key`: Path to your SSH private key

### Step 2: Test Connectivity
```bash
# Test if Ansible can reach both VMs
ansible webservers -m ping

# Check if sudo access works
ansible webservers -m shell -a "sudo whoami" --become
```

### Step 3: Deploy Webservers
```bash
# Deploy webservers on both VMs
ansible-playbook deploy-webserver.yml

# Check deployment status
ansible-playbook deploy-webserver.yml --check --diff
```

### Step 4: Verify Deployment
1. **Access VM1**: Open browser to `http://VM1_IP:8080`
   - Should display: "Hello World from SJSU-1"

2. **Access VM2**: Open browser to `http://VM2_IP:8080`
   - Should display: "Hello World from SJSU-2"

### Step 5: Undeploy (Optional)
```bash
# Remove webservers and clean up resources
ansible-playbook undeploy-webserver.yml
```

## Playbook Details

### Deploy Playbook (`deploy-webserver.yml`)
**Features:**
- Cross-platform support (Ubuntu/Debian and RedHat/CentOS)
- Installs and configures Apache web server
- Sets up custom virtual host on port 8080
- Deploys VM-specific HTML pages
- Configures firewall rules
- Includes verification checks

**Key Tasks:**
1. Package management (apt/yum)
2. Apache installation and configuration
3. Virtual host setup for port 8080
4. HTML template deployment with VM-specific variables
5. Service management (start/enable)
6. Firewall configuration
7. Connectivity verification

### Undeploy Playbook (`undeploy-webserver.yml`)
**Features:**
- Complete removal of webserver components
- Service cleanup
- Configuration file removal
- Firewall rule cleanup
- Verification of successful removal

## Templates

### HTML Template (`templates/index.html.j2`)
- Responsive design with modern CSS
- VM-specific messaging using Jinja2 variables
- Displays deployment timestamp
- Shows server information

### Apache Configuration Templates
- **Ubuntu/Debian**: `apache-8080.conf.j2`
- **RedHat/CentOS**: `apache-8080-redhat.conf.j2`
- Both configure virtual host on port 8080
- Include proper directory permissions

## Variables Used

### Playbook Variables
- `webserver_port: 8080` - Port for web service
- `webserver_root: /var/www/html` - Document root directory

### Template Variables
- `vm_name` - Set to "VM1" or "VM2" based on inventory hostname
- `vm_number` - Set to "1" or "2" for display
- `ansible_date_time.iso8601` - Deployment timestamp

## Troubleshooting

### Common Issues
1. **SSH Connection Failed**
   - Verify SSH keys are properly configured
   - Check if the correct username is specified
   - Ensure VMs are accessible from control machine

2. **Package Installation Failed**
   - Verify internet connectivity on VMs
   - Check if package repositories are accessible
   - Ensure proper sudo privileges

3. **Port 8080 Not Accessible**
   - Check firewall settings on VMs
   - Verify security group/firewall rules in cloud environment
   - Ensure Apache is listening on port 8080

4. **Permission Denied Errors**
   - Verify sudo access is configured
   - Check file permissions in web directory

### Debugging Commands
```bash
# Check Ansible configuration
ansible --version
ansible-config dump

# Test connectivity with verbose output
ansible webservers -m ping -vvv

# Check Apache status
ansible webservers -m shell -a "systemctl status apache2" --become

# Check if port 8080 is listening
ansible webservers -m shell -a "netstat -tlnp | grep 8080" --become
```

## Security Considerations
- SSH key-based authentication is required
- Firewall rules are configured to allow only port 8080
- Apache runs with minimal privileges
- Regular security updates should be applied to VMs

## Demo Screenshots Checklist
For your Word document submission, include screenshots of:

1. **VM Setup**: Screenshots of both VMs running
2. **Ansible Configuration**: Show inventory file and ansible.cfg
3. **Connectivity Test**: `ansible webservers -m ping` output
4. **Deployment Execution**: Full output of `ansible-playbook deploy-webserver.yml`
5. **Web Page VM1**: Browser showing "Hello World from SJSU-1" at VM1_IP:8080
6. **Web Page VM2**: Browser showing "Hello World from SJSU-2" at VM2_IP:8080
7. **Undeploy Execution**: Output of `ansible-playbook undeploy-webserver.yml`
8. **Verification**: Show that websites are no longer accessible after undeploy

## Repository Structure for GitHub
```
cmpe-282-labs/hw1/
├── ansible.cfg
├── inventory/hosts
├── templates/
├── deploy-webserver.yml
├── undeploy-webserver.yml
└── README.md
```

## Author
[Your Name]  
CMPE 282 - Cloud Technologies  
San Jose State University


