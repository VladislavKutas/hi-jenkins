
Vagrant.configure("2") do |config|

  config.vm.box = "debian/contrib-buster64"

  config.vm.define "jenkins" do | jenkins |

    # Provider
    config.vm.provider "virtualbox" do |v|
      v.name = "jenkins"
      v.memory = 4096
      v.cpus = 2
    end

    config.vm.hostname = "jenkins"
    config.vm.network "private_network", ip: "192.168.1.101"
    config.vm.network "forwarded_port", guest: 8080, host: 8080

   # Run Ansible provisioner to install Jenkins and its dependencies
    config.vm.provision "install_jenkins", type:'ansible' do |ansible|
      ansible.verbose = "v"
      ansible.inventory_path = 'provisioning/hosts.yaml'
      ansible.playbook = "provisioning/install_jenkins.yaml"  
  end

     #Run Ansible provisioner to copy dependencies for source code analysis tools
    config.vm.provision "copy_sca", type:'ansible' do |ansible|
      ansible.verbose = "v"
      ansible.inventory_path = 'provisioning/hosts.yaml'
      ansible.playbook = "provisioning/provide_dependencies.yaml" 
  end

  end
  
  config.ssh.insert_key = false
  
  #config.vm.synced_folder "./shared_files", "/home/vagrant/shared_files", type: "virtualbox", :owner => 'vagrant', :group => 'vagrant', :mount_options => ['dmode=775', 'fmode=775']

  # Disable VirtualBox Guest Additions check because this box already has it.
    if Vagrant.has_plugin?("vagrant-vbguest") then
          config.vbguest.auto_update = false
    end

end