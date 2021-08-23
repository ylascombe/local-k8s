Vagrant.configure("2") do |config|
  #config.vm.box = "ubuntu/impish64" # not compatible with docker provisionner :-(
  config.vm.box = "ubuntu/hirsute64"
  config.vm.network "forwarded_port", guest: 80, host: 80
  config.vm.network "forwarded_port", guest: 443, host: 443
  config.vm.network "forwarded_port", guest: 8888, host: 8888
  #config.vm.network :hostonly, "192.168.50.4"
  config.vm.network "private_network", ip: "192.168.121.60"
  config.vm.hostname = "mycluster.algolia"

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true

  config.vm.provider "virtualbox" do |v|
    v.memory = 4096
    v.cpus = 4
  end

  # Install Docker
  config.vm.provision :docker # did not work with this ubuntu version :-(

  # config.vm.provision "ansible" do |ansible|
  #   ansible.playbook = "ansible/playbook.yml"
  #   ansible.galaxy_role_file = "ansible/requirements.yml"
  #   #ansible.install_mode = "pip_args_only"
  #   #ansible.pip_args = "-r /ansible/requirements.txt"
  # end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "ansible/playbook.yml"
    #ansible.install_mode = "pip_args_only"
    #ansible.pip_args = "-r /ansible/requirements.txt"
    #ansible.extra_vars = { ansible_python_interpreter:"/usr/bin/python3" }
    #ansible.install_mode = "pip3" # fixed
    #ansible.version = "2.9.13" # fixed
    ##ansible.install_mode = "pip3_args_onlyppp"
    ##ansible.pip_args = "-r requirements.txt"
    ansible.compatibility_mode = "2.0"
    ansible.extra_vars = { ansible_python_interpreter:"/usr/bin/python3" }
  end
end

