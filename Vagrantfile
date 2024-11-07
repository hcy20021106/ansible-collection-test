# -*- mode: ruby -*-
# vi: set ft=ruby :

def command_available?(command)
  system("#{command} > /dev/null 2>&1")
end

# vm_default values can be changed with option "--name", "--cpus", "--memory"
vm_name='ansible-collection-test'

# values between 2 and 8 default is 4
vm_cpus='8' 
# values between 2048 and 8192 default is 4096
vm_memory='8192'

# set default box to Ubuntu 22.04 if not set via commandline
vm_box = "bento/ubuntu-22.04"
if vm_box == 'base'
  # vm_box="bento/debian-12"
  vm_box="bento/ubuntu-22.04"
  # vm_box="bento/ubuntu-24.04"
end

require 'getoptlong'

# Configure GetoptLong to pass through unknown options
#GetoptLong.configure("pass_through")

opts = GetoptLong.new(
  [ '--ansible_type', GetoptLong::OPTIONAL_ARGUMENT ],
  [ '--ansible_tags', GetoptLong::OPTIONAL_ARGUMENT ],
  [ '--cpus', GetoptLong::OPTIONAL_ARGUMENT ],
  [ '--memory', GetoptLong::OPTIONAL_ARGUMENT ],
  [ '--name', GetoptLong::OPTIONAL_ARGUMENT ],
# add options for other commands that you want to use to avoid errors
#  [ '--provision-with', GetoptLong::OPTIONAL_ARGUMENT ],
#  [ '-c', GetoptLong::OPTIONAL_ARGUMENT ],
#  [ '--command', GetoptLong::OPTIONAL_ARGUMENT ]
)

### this line helps for passing standard options to vagrant command e.g. vagrant ssh -c "ls"
opts.ordering=(GetoptLong::REQUIRE_ORDER)  

# Default values for test can be "ansible" or "ansible_local"
ansible_type_Parameter='ansible'
ansible_tags_Parameter=''

opts.each do |opt, arg|
  case opt
    when '--ansible_type'
      ansible_type_Parameter=arg
    when '--ansible_tags'
      ansible_tags_Parameter=arg
    when '--name'
      vm_name=arg
    when '--cpus'
      vm_cpus=arg
    when '--memory'
      vm_memory=arg
  end
end

if (ansible_type_Parameter == 'ansible' && !command_available?("ansible --version"))
  ansible_type_Parameter='ansible_local'
end

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Define a variable to store the system information
  system_info = ""

  # Detect the host system and architecture
  host = RbConfig::CONFIG['host_os']
  case host
  when /mswin|msys|mingw|cygwin|bccwin|wince|emc/
    system_info = "Windows"
  when /darwin|mac os/
    system_info = "Mac OS"
  when /linux/
    system_info = "Linux"
  when /solaris|bsd/
    system_info = "Unix"
  else
    system_info = "Unknown"
  end

  # Append the host architecture
  system_info += " #{RbConfig::CONFIG['host_cpu']}"

  # Write out the system information
  puts "The host system is #{system_info}"



  case system_info
  when /Mac OS arm64/
    # vm_box = vm_box + "-arm64"
    vm_box = vm_box
  when /Linux amd/
    vm_box = vm_box
  else
    vm_box = vm_box
  end

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = vm_box
  # config.vm.box = "bento/ubuntu-24.04"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

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
  unless ansible_type_Parameter == 'ansible_local'
    config.vm.synced_folder ".", "/vagrant", disabled: true
  end


  # Disable the default share of the current code directory. Doing this
  # provides improved isolation between the vagrant box and your host
  # by making sure your Vagrantfile isn't accessable to the vagrant box.
  # If you use this you may want to enable additional shared subfolders as
  # shown above.
  # config.vm.synced_folder ".", "/vagrant", disabled: true

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

  config.vm.hostname =  "#{vm_name}"

  config.vm.post_up_message = "Vm '#{vm_name}' with vagrant box '#{config.vm.box}' is up (successfully)"

  config.trigger.after :up, name: "Finished Message", info: "Machine '#{vm_name}' with vagrant box '#{config.vm.box}' i up sucessfully!"

  # config.vm.customize ["modifyvm", :id, "--memory", 8192]
  # config.vm.customize ["modifyvm", :id, "--cpus", 8]

  # can be used for separate machines for services
  # config.vm.define "#{vm_name}" do |fiveg_core_gnb|
  #   fiveg_core_gnb.vm.provision :shell, inline: "echo 5G-core-gnb"
  #   # ...
  # end

  config.vm.provider "parallels" do |v|
    v.name = "#{vm_name}"
    v.memory = "#{vm_memory}"
    v.cpus = "#{vm_cpus}"
    # v.update_guest_tools = true

    # USB Ettus Westbridge Vendor ID: 0x2500 Product ID: 0x0022 
    # v.customize ["set", :id, "--device-add", "usb", "--vendor-id", "0x2500", "--product-id", "0x0022"]
  end
  
  config.vm.provider "docker" do |v, override|
    override.vm.box = nil #reset vm.box as it is needed to avoid errors
    override.ssh.insert_key = true
    v.image = "bento/ubuntu-22.04"
    v.name = "#{vm_name}"
    v.has_ssh = true
    v.privileged = true
    v.remains_running = true
    v.create_args = ["--cgroupns=host"]
    v.volumes = ["/sys/fs/cgroup:/sys/fs/cgroup"]
    v.cmd = ["/bin/bash", "-c", "echo 'Container started ...'; while true; do date; echo 'still running ...'; sleep 1; done"]
    # v.cmd = ["/bin/bash", "-c", "echo 'Container started ...'; n=1000 ; while [ $n -gt 0 ] do echo $n; sleep 1; n=$((n-1)) done"]
    # v.cmd = ["/bin/bash"]
    # v.vagrant_vagrantfile = "Vagrantfile"
  end
  
  config.vm.provider "vmware" do |v, override|
    if ansible_type_Parameter == 'ansible_local'
      override.vm.synced_folder ".", "/vagrant"
    end
    v.vmx['displayname'] = "#{vm_name}"
    v.vmx["numvcpus"] = "#{vm_cpus}"
    v.vmx["memsize"] = "#{vm_memory}"
    v.gui = true
    # USB Ettus Westbridge Vendor ID: 0x2500 Product ID: 0x0022 
    # v.vmx["usb.autoConnect.device0"]  = "0x2500:0x0022"
  end

  config.vm.provider "vmware_desktop" do |v, override|
    if ansible_type_Parameter == 'ansible_local'
      override.vm.synced_folder ".", "/vagrant"
    end
    v.vmx['displayname'] = "#{vm_name}"
    v.vmx["numvcpus"] = "#{vm_cpus}"
    v.vmx["memsize"] = "#{vm_memory}"
    v.gui = true
    # USB Ettus Westbridge Vendor ID: 0x2500 Product ID: 0x0022 
    # v.vmx["usb.autoConnect.device0"]  = "0x2500:0x0022"
  end

  config.vm.provider "vmware_fusion" do |v, override|
    if ansible_type_Parameter == 'ansible_local'
      override.vm.synced_folder ".", "/vagrant"
    end
    v.vmx['displayname'] = "#{vm_name}"
    v.vmx["numvcpus"] = "#{vm_cpus}"
    v.vmx["memsize"] = "#{vm_memory}"
    v.gui = true
    # vmware.update_guest_tools = true
    # vmware.memory = "#{memory}"
    # vmware.cpus = "#{cpus}"
    # USB Ettus Westbridge Vendor ID: 0x2500 Product ID: 0x0022 
    # v.vmx["usb.autoConnect.device0"]  = "0x2500:0x0022"
  end

  # TODO does not work but we need a way to have common settings for all vmware providers
  # config.vm.provider "vmware", "vmware_desktop", "vmware_fusion" do |v, override|
  #   if ansible_type_Parameter == 'ansible_local'
  #     override.vm.synced_folder ".", "/vagrant"
  #   end
  #   v.vmx['displayname'] = "#{vm_name}"
  #   v.vmx["numvcpus"] = "#{vm_cpus}"
  #   v.vmx["memsize"] = "#{vm_memory}"
  #   v.gui = true
  #   # vmware.update_guest_tools = true
  #   # vmware.memory = "#{memory}"
  #   # vmware.cpus = "#{cpus}"
    
  #   # connect usb device automatically
  #   # USB Ettus Westbridge Vendor ID: 0x2500 Product ID: 0x0022 
  #   v.vmx["usb.autoConnect.device0"]  = "0x2500:0x0022"
  # end

  config.vm.provider "hyperv" do |v, override|
    # config.ssh.insert_key = false
    if ansible_type_Parameter == 'ansible_local'
      override.vm.synced_folder ".", "/vagrant", type: "smb"
    end
    v.vm_integration_services = {
      guest_service_interface: true,
      # CustomVMSRV: true
    }
    v.vmname = "#{vm_name}"
    v.cpus = "#{vm_cpus}"
    v.memory = "#{vm_memory}"
    v.maxmemory = "#{vm_memory}"
  end

  config.vm.provider "virtualbox" do |v, override|
    if ansible_type_Parameter == 'ansible_local'
      override.vm.synced_folder ".", "/vagrant", type: "virtualbox"
    end
    v.name = "#{vm_name}"
    v.cpus = "#{vm_cpus}"
    v.memory = "#{vm_memory}"
    v.check_guest_additions = true
    
    # connect usb device automatically
    # USB Ettus Westbridge Vendor ID: 0x2500 Product ID: 0x0022 
    # v.customize ["modifyvm", :id, "--usb", "on"]
    # v.customize ["modifyvm", :id, "--usbxhci", "on"]
    # v.customize ["modifyvm", :id, "--usbehci", "on"]
    # v.customize ["usbfilter", "add", "0", "--target", :id, "--name", "Ettus B200", "--vendorid", "0x2500", "--productid", "0x0022"]
  end

  config.vm.provider "libvirt" do |v|
    v.cpus = "#{vm_cpus}"
    v.memory = "#{vm_memory}"
  #   v.driver = "kvm"
  #   # v.driver = "qemu"
  #   # v.socket = "/usr/local/var/run/libvirt/libvirt-sock"

  #   # The name of the server, where Libvirtd is running.
  #   # v.host = "localhost"

  #   # If use ssh tunnel to connect to Libvirt.
  #   v.connect_via_ssh = false

  #   # The username and password to access Libvirt. Password is not used when
  #   # connecting via ssh.
  #   v.username = "hellbruh"
  #   # v.password = "secret"

  #   # Libvirt storage pool name, where box image and instance snapshots will
  #   # be stored.
  #   v.storage_pool_name = "default"

  #   # Set a prefix for the machines that's different than the project dir name.
  #   #v.default_prefix = ''
end
  
  # to run provision only vagrant provision --provision-with install
  # config.vm.provision "debug", type: "#{ansible_type_Parameter}" do |ansible|
  #   ansible.playbook = "ansible/helper-view-all-facts.yml"
  # end

  # config.vm.provision "debug", type: "#{ansible_type_Parameter}" do |ansible|
  #   ansible.playbook = "ansible/helper-view-all-facts.yml"
  # end

  # config.vm.provision "default", type: "#{ansible_type_Parameter}" do |ansible|
  #   ansible.playbook = "ansible/default_main_playbook.yml"
  # end

  # config.vm.provision "mine_local", type: "#{ansible_type_Parameter}" do |ansible|
  #   ansible.playbook = "ansible/my_main_playbook.yml"
  # end

  # config.vm.provision "collection", type: "#{ansible_type_Parameter}" do |ansible|
  #    ansible.playbook = "test-allinone.yml"
  # end

  config.vm.provision "collection", type: "#{ansible_type_Parameter}" do |ansible|
     ansible.playbook = "test.yml"
  end

  #  config.vm.provision "collection", type: "#{ansible_type_Parameter}" do |ansible|
#    ansible.playbook = "cosa.ping.ping.yml"
#    ansible.playbook = "collections/ansible_collections/cosa/ping/playbooks/ping.yml"
    # ansible.playbook = "cosa.ping.ping.yml"
    # ansible.playbook = "cosa.mongodb_management.mongodb_setup.yml"
    # ansible.playbook = "collections/ansible_collections/my_namespace/snapshot_management/playbooks/create_snapshot.yml"
    #ansible.inventory_path = "collections/ansible_collections/my_namespace/mongodb_management/inventories/production"
    #ansible.tags = ansible_tags_Parameter
#  end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
