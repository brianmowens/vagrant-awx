Vagrant.configure("2") do |config|
    
    # Sync host working directory to virtual machine
    config.vm.synced_folder '.', '/vagrant'

    # SSH settings - Uses vagrant insecure key
    config.ssh.insert_key = false

    $install_ansible = <<-SCRIPT
    echo "Installing Ansible"
    apt-get update
    apt-get -y install software-properties-common
    apt-add-repository --yes --update ppa:ansible/ansible
    apt-get -y install ansible
    SCRIPT

    # Vagrant install script
    $install_docker = <<-SCRIPT
    echo "Installing Docker"
    apt-get update
    apt-get -y install apt-transport-https \ 
    ca-certificates curl gnupg-agent software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable"
    apt-get update
    apt-get -y install docker-ce docker-ce-cli containerd.io
    SCRIPT

    $install_pip = <<-SCRIPT
    echo "Installing pip"
    apt-get -y install python-pip
    SCRIPT
    
    $install_docker_compose = <<-SCRIPT
    echo "Installing Docker Compose"
    pip install docker-compose
    SCRIPT

    $clone_awx_repo = <<-SCRIPT
    echo "Cloning AWX Git repository"
    git clone https://github.com/ansible/awx.git /awx
    SCRIPT

    $install_gnu_make = <<-SCRIPT
    echo "Installing GNU Make"
    apt-get -y install build-essential
    SCRIPT

    $install_nodejs8 = <<-SCRIPT
    echo "Installing NodeJS 8"
    curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
    apt-get install -y nodejs
    SCRIPT

    $install_nvm_manager = <<-SCRIPT
    "Installing NVM manager"
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
    SCRIPT

    $run_awx_playbook = <<-SCRIPT
    echo "Running ansible playbook for AWX setup."
    ansible-playbook -i /awx/installer/inventory /awx/installer/install.yml
    SCRIPT

    # VirtualBox.
    config.vm.define "docker-awx" do |virtualbox|
      virtualbox.vm.hostname = "docker-awx"
      virtualbox.vm.box = "ubuntu/xenial64"
      virtualbox.vm.box_version = "20190419.0.0"
      virtualbox.vm.box_check_update = true
      virtualbox.vm.define "docker-awx"
      virtualbox.vm.network "private_network", ip: "10.0.250.252",
          virtualbox__intnet: "NatNetwork"
      config.vm.provider :virtualbox do |v|
        v.gui = false
        v.memory = 4096
        v.cpus = 4
        v.name = "docker-awx"
        v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        v.customize ["modifyvm", :id, "--ioapic", "on"]
      end
      config.vm.provision "shell", inline: $install_ansible
      config.vm.provision "shell", inline: $install_docker
      config.vm.provision "shell", inline: $install_pip
      config.vm.provision "shell", inline: $install_docker_compose
      config.vm.provision "shell", inline: $clone_awx_repo
      config.vm.provision "shell", inline: $install_gnu_make
      config.vm.provision "shell", inline: $install_nodejs8
      config.vm.provision "shell", inline: $install_nvm_manager
      config.vm.provision "shell", inline: $run_awx_playbook
      config.vm.provision "shell", inline: "echo Hello, World"
    end

  end