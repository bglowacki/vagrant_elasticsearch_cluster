# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  ["es-client-01", "es-client-02", "es-master-01", "es-master-02", "es-master-03", "es-data-01", "es-data-02", "es-data-03", "es-data-04"].each_with_index do |machine_name, index|
    config.vm.define machine_name do |machine|
      machine.vm.box = "hashicorp/precise32"
      machine.vm.hostname = machine_name
      machine.vm.network "private_network", ip: "192.168.33.#{index + 1}"
      machine.hostmanager.aliases = [machine_name]


      machine.vm.provision "shell", inline: "yes | sudo apt-get update"
      machine.vm.provision "shell", inline: "yes | sudo apt-get install curl"
      machine.vm.provision "shell", inline: "yes | sudo apt-get install openjdk-7-jre"
      machine.vm.provision "shell", inline: "yes | wget https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-1.7.2.deb"
      machine.vm.provision "shell", inline: "yes | sudo dpkg -i elasticsearch-1.7.2.deb"
      machine.vm.provision "shell", inline: "yes | sudo update-rc.d elasticsearch defaults"
      machine.vm.provision "shell", inline: "yes | echo 'cluster.name: my_cluster' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | echo 'node.name: #{machine_name}' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | echo 'network.publish_host: 192.168.33.#{index + 1}' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | echo 'network.bind_host: 0.0.0.0' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | echo 'discovery.zen.ping.multicast.enabled: false' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | echo 'discovery.zen.ping.unicast.hosts: [\"es-client-01\", \"es-client-02\", \"es-master-01\", \"es-master-02\", \"es-master-03\", \"es-data-01\", \"es-data-02\", \"es-data-03\", \"es-data-04\"]' >> /etc/elasticsearch/elasticsearch.yml"
    end
  end

  ["es-client-01", "es-client-02"].each do |machine_name|
    config.vm.define machine_name do |machine|
      machine.vm.provision "shell", inline: "yes | echo 'node.client: true' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | echo 'node.data: false' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | echo 'node.master: false' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | sudo service elasticsearch restart"
    end
  end
  ["es-master-01", "es-master-02", "es-master-03"].each do |machine_name|
    config.vm.define machine_name do |machine|
      machine.vm.provision "shell", inline: "yes | echo 'node.client: false' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | echo 'node.data: false' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | echo 'node.master: true' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | sudo service elasticsearch restart"
    end
  end
  ["es-data-01", "es-data-02", "es-data-03", "es-data-04"].each do |machine_name|
    config.vm.define machine_name do |machine|
      machine.vm.provision "shell", inline: "yes | echo 'node.client: false' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | echo 'node.data: true' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | echo 'node.master: false' >> /etc/elasticsearch/elasticsearch.yml"
      machine.vm.provision "shell", inline: "yes | sudo service elasticsearch restart"
    end
  end



  config.vm.define "es-client-01" do |machine|
    machine.vm.network "forwarded_port", guest: 9200, host: 9201
    machine.vm.provision "shell", inline: "yes | /usr/share/elasticsearch/bin/plugin install mobz/elasticsearch-head"
    machine.vm.provision "shell", inline: "yes | sudo service elasticsearch restart"
    machine.vm.provision "shell", inline: "yes | curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -"
    machine.vm.provision "shell", inline: "yes | sudo apt-get install -y nodejs"
    machine.vm.provision "shell", inline: "sudo npm install elasticdump -g"
    # machine.vm.provision "shell", inline: "elasticdump --bulk=true --input=/vagrant/index.json --output=http://localhost:9200/"
  end

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y apache2
  # SHELL
end
