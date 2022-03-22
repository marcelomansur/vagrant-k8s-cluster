# Repositório NFS adequado à localização da estação de trabalho
$LOAD_PATH.unshift(File.dirname(__FILE__))
require 'yaml'

dir = File.dirname(File.expand_path(__FILE__))
configuration = YAML.load_file("#{dir}/config.yaml")

# Máquinas virtuais
Vagrant.configure("2") do |config|
  # config.vm.provision "shell", path: "./scripts/satellite_register.sh"
  # Cria diretório compartilhado dentro do guest
  config.vm.synced_folder '.', '/vagrant/shared/', :mount_options => ['dmode=777', 'fmode=777']
  

  configuration["hosts"].each do |server|
    config.vm.define server["name"] do |mv|
      mv.vm.box = server["box"]
      mv.vm.host_name = "vagrant-#{server["name"]}.#{configuration["DOMAIN"]}"
      # Configura atributos do Virtualbox.
      mv.vm.provider "virtualbox" do |virtualbox|
        virtualbox.name = "vagrant-#{server["name"]}"
        virtualbox.memory = server["ram"]
        virtualbox.cpus = server["cpu"]
      end
      server["rede"].each do |ip|
        mv.vm.network "private_network", ip: ip
      end unless server["rede"].nil?
      server["porta"].each do |port|
        mv.vm.network "forwarded_port", guest: 8080, host: port
      end unless server["porta"].nil?
    end
  end
  
  config.vm.provision "shell", path: "./scripts/setup-hosts.sh" do |s|
    s.args = ["enp0s8"]
  end

  config.vm.provision "shell", path: "./scripts/all-nodes-install.sh" do |s|
    s.args = ["enp0s8"]
  end

end
