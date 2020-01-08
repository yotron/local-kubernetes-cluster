# Size of the cluster created by Vagrant
num_instances=3

# Change basename of the VM
instance_name_prefix="k8s-node"


Vagrant.configure("2") do |config|
  # always use Vagrants insecure key
  config.ssh.insert_key = false

  config.vm.box =  "bento/ubuntu-16.04"

  config.vm.provider :virtualbox do |v|
    v.check_guest_additions = false
    v.memory = 2048
    v.cpus = 2
    v.functional_vboxsf     = false
  end

  # plugin conflict
  if Vagrant.has_plugin?("vagrant-vbguest") then
    config.vbguest.auto_update = false
  end

  # Set up each box
  (1..num_instances).each do |i|
    instanceNum = num_instances + 1 - i
    if instanceNum == 1
      vm_name = "k8s-master"
    else
      vm_name = "%s-%02d" % [instance_name_prefix, instanceNum-1]
    end
    config.vm.synced_folder '.', '/vagrant', disabled: true
    config.vm.define vm_name do |host|
      host.vm.hostname = vm_name

      ip = "172.18.18.#{instanceNum+100}"
      host.vm.network :private_network, ip: ip
      host.vm.provision "dashboard", type: "ansible" do |ansible|
        ansible.inventory_path = "ansible/inventories/vagrant.ini"
        #ansible.verbose = "vvv"
        ansible.playbook = "ansible/kubernetes.yml"
        ansible.raw_arguments = ["--force-handlers"]
        ansible.force_remote_user = true
        ansible.become = true
        ansible.host_key_checking = false
      end
      # This is a workaround when Kubernetes will restart completly. The secret keys (Tokens) and the container must be recreated.
      # The complete restart of Kubernetes is not a common scenario in production mode.
      if(vm_name == "k8s-master") then
          $script = <<-SCRIPT
            kubectl replace --force -f ~/manifests/calico.yml && \
            kubectl replace --force -f ~/manifests/k8s-dashboard.yml && \
            kubectl replace --force -f ~/manifests/coredns.yml
           SCRIPT
           host.vm.provision "shell", run: "always", privileged: false, inline: $script
       end
    end
  end
end
