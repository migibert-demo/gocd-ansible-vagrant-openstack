require 'vagrant-openstack-provider'

Vagrant.configure("2") do |config|

  config.vm.box = "openstack"
  config.vm.box_url = "https://github.com/ggiamarchi/vagrant-openstack/raw/master/source/dummy.box"

  config.ssh.shell = "bash"
  config.ssh.username = "stack"

  config.vm.provider :openstack do |os|
    os.username = ENV['OS_USERNAME']
    os.password = ENV['OS_PASSWORD']
    os.tenant_name = ENV['OS_TENANT_NAME']
    os.openstack_auth_url = ENV['OS_AUTH_URL']
    os.openstack_compute_url = ENV['OS_COMPUTE_URL']
    os.openstack_network_url = ENV['OS_NETWORK_URL']    
    os.region = ENV['OS_REGION_NAME']
  end

  config.vm.define 'test' do |test|
    test.vm.provider :openstack do |os|
      os.server_name = "gocd-server"
      os.floating_ip_pool = ENV['OS_FLOATING_IP_POOL']
      os.flavor = ENV['OS_FLAVOR']
      os.image = ENV['OS_IMAGE']
      os.networks = [ENV['OS_NETWORK']]              
    end
  end

  # Install ansible  
  config.vm.provision "shell", path: "installAnsibleOnUbuntu.sh", privileged: "true"

   # update /etc/hosts with hostname
  config.vm.provision "shell" do |s|
       s.inline = "echo $1 $2>> /etc/hosts"
       s.args = "127.0.0.1 'gocd-server'"
       s.privileged = "true"
  end

   # provision and deployment with ansible
  config.vm.provision :ansible do |ansible|
      ansible.playbook = "gocd/gocd.yml"
      ansible.verbose = "vv"
      ansible.limit = 'all'
      ansible.sudo = true
  end
end
