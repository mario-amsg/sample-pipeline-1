# Vagrantfile for setting up 4 VMs on VirtualBox with ARM Mac host
Vagrant.configure("2") do |config|
  # Define a private network for communication
  network = "192.168.56."

  # Shared configurations for all VMs
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
  end

  # Jenkins VM
  config.vm.define "jenkins" do |jenkins|
    jenkins.vm.box = "net9/ubuntu-24.04-arm64"
    jenkins.vm.hostname = "jenkins.local"
    jenkins.vm.network "private_network", ip: "#{network}101"
    jenkins.hostmanager.aliases = ["jenkins.local"]
    jenkins.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y openjdk-21-jdk
      curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
      echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
      sudo apt-get update
      sudo apt-get install -y jenkins
      sudo systemctl enable jenkins
      sudo systemctl start jenkins
    SHELL
  end

  # Git Repository VM
  config.vm.define "git" do |git|
    git.vm.box = "net9/ubuntu-24.04-arm64"
    git.vm.hostname = "git.local"
    git.vm.network "private_network", ip: "#{network}102"
    git.hostmanager.aliases = ["git.local"]
    git.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y git
    SHELL
  end

  # Configuration Management VM
  config.vm.define "config-mgmt" do |config_mgmt|
    config_mgmt.vm.box = "net9/ubuntu-24.04-arm64"
    config_mgmt.vm.hostname = "config-mgmt.local"
    config_mgmt.vm.network "private_network", ip: "#{network}103"
    config_mgmt.hostmanager.aliases = ["config-mgmt.local"]
    config_mgmt.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y ansible
    SHELL
  end

  # Minikube VM
  config.vm.define "minikube" do |minikube|
    minikube.vm.box = "net9/ubuntu-24.04-arm64"
    minikube.vm.hostname = "minikube.local"
    minikube.vm.network "private_network", ip: "#{network}104"
    minikube.hostmanager.aliases = ["minikube.local"]
    minikube.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y curl apt-transport-https
      curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-arm64
      sudo install minikube-linux-arm64 /usr/local/bin/minikube && rm minikube-linux-arm64
      curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/arm64/kubectl"
      chmod +x kubectl
      mkdir -p $HOME/.local/bin
      mv ./kubectl $HOME/.local/bin/kubectl
      export PATH="${PATH}:${HOME}/.local/bin"
      sudo apt-get update
      sudo apt-get install -y docker.io
      sudo usermod -aG docker $USER
      minikube config set driver docker
    SHELL
  end

  # Enable hostmanager plugin
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
end
