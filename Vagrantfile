
Vagrant.configure("2") do |config|

  config.vm.define "jenkins" do | jenkins |

    config.vm.box = "debian/contrib-buster64"

  # Disable VirtualBox Guest Additions check because this box already has it.
    if Vagrant.has_plugin?("vagrant-vbguest") then
          config.vbguest.auto_update = false
    end

    # VM Provider settings
    config.vm.provider "virtualbox" do |v|
      v.name = "jenkins"
      v.memory = 4096
      v.cpus = 2
    end

    config.vm.hostname = "jenkins"
    config.vm.network "private_network", ip: "192.168.1.101"
    config.vm.network "forwarded_port", guest: 8080, host: 8080

   # Run Ansible provisioner to install and configure Jenkins and provide dependencies
    config.vm.provision "install_jenkins", type:'ansible' do |ansible|
      ansible.verbose = "vv"
      ansible.vault_password_file = "provisioning/github_keys.pass"
      ansible.inventory_path = 'provisioning/ansible/hosts.yaml'
      ansible.playbook = "provisioning/ansible/provision_jenkins.yaml"  
  end

  end
  
  # config.ssh.insert_key = false


end