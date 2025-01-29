# Environment Setup Guide

This guide provides step-by-step instructions for setting up a development environment with four virtual machines using Vagrant and VirtualBox. Each VM has a specific role: Jenkins, Git repository, Configuration Management, and Minikube.

## Prerequisites

Before starting, ensure you have the following installed on your ARM-based macOS machine:

### 1. Install Homebrew
If Homebrew is not installed, run:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 2. Install VirtualBox
To install VirtualBox (ARM-compatible):
```bash
brew install --cask virtualbox
```

### 3. Install Vagrant
To install Vagrant using Homebrew:
```bash
brew install vagrant
```

**NOTE**:
The plugin manager is broken in Vagrant 2.4.2. You may need to manually install version 2.4.3 to make the plugin work

https://developer.hashicorp.com/vagrant/install

### 4. Install Vagrant plugin
Add the Host Manager plugin to use add hostnames to /etc/hosts file
```bash
vagrant plugin install vagrant-hostmanager
````

### 5. Verify Installations
After installation, verify the installations:
- **VirtualBox**:
  ```bash
  virtualbox --help
  ```
- **Vagrant**:
  ```bash
  vagrant --version
  ```

Ensure no errors are reported before proceeding.

---

## Step-by-Step Environment Deployment

1. **Clone the Repository**
   Clone the repository containing the Vagrant configuration:
   ```bash
   git clone git@github.ibm.com:jcalvosu/sre-onboarding-cicd.git
   cd sre-onboarding-cicd
   ```

2. **Initialize the Vagrant Environment**
   Run the following command to bring up the virtual machines:
   ```bash
   vagrant up
   ```

   This command will:
   - Download the necessary Vagrant boxes (if not already downloaded).
   - Configure the network and provision the VMs.

3. **Access the Virtual Machines**
   To SSH into a specific virtual machine, use:
   ```bash
   vagrant ssh <vm-name>
   ```
   For example:
   ```bash
   vagrant ssh jenkins
   ```
   You also can check the status of the Virtual Machines using the command
   ```bash
   vagrant global-status
   ```

4. **Verify Network Communication**
   Ensure the VMs can communicate by pinging their private IPs (default network is 192.168.56.0/24). For example, from the Jenkins VM:
   ```bash
   ping 192.168.56.102  # Pinging the Git repository VM
   ```
   Or use the hostnames:
   - jenkins.local
   - git.local
   - config-mgmt.local
   - minikube.local

5. **Access Jenkins**
   Open a browser and navigate to Jenkins using its private IP and port 8080:
   ```
   http://jenkins.local:8080
   ```
   Retrieve the initial admin password from the Jenkins VM:
   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```
   Copy and paste the password into the Jenkins login screen.

---

## Troubleshooting

### VM Deployment Issues
- **VirtualBox Errors**:
  Ensure the VirtualBox app has permissions in macOS:
  - Go to **System Preferences → Privacy & Security → Full Disk Access**.
  - Add VirtualBox to the list if it’s not already there.

- **Network Issues**:
  Ensure the Host-Only Network is properly configured in VirtualBox.

### Vagrant Issues
- Run `vagrant reload` to restart the VMs.
- Use `vagrant provision` to reapply provisioning scripts.

---

This concludes the setup process. If you encounter issues, consult your instructor or refer to the Vagrant and VirtualBox documentation. Happy learning!
