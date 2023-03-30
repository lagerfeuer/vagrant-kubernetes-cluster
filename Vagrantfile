NUM_WORKER_NODES = 1
IP_NETWORK_FMT = "10.0.0.%d"

cluster = {
  'master': {
    ip: IP_NETWORK_FMT % [10],
    cpus: 2,
    memory: 4096
  }
}
(1..NUM_WORKER_NODES).each do |idx|
  cluster["worker-#{idx}"] = {
    ip: IP_NETWORK_FMT % [10+idx],
    cpus: 1,
    memory: 1024
  }
end

groups = {
  'masters': ['master'],
  'workers': cluster.keys.select { |n| n.start_with?('worker') }
}

Vagrant.configure(2) do |v|
  v.vm.box = "ubuntu/jammy64"

  cluster.each do |host, config|
    v.vm.define host do |node|
      node.vm.hostname = host
      node.vm.network :private_network, ip: config[:ip]

      node.vm.provider "virtualbox" do |vbox|
        vbox.name = "#{host}"
        vbox.cpus = config[:cpus]
        vbox.memory = config[:memory]
      end

      node.vm.provision :ansible do |ansible|
        ansible.playbook = "ansible/bootstrap.yaml"
        ansible.extra_vars = { cluster: cluster, node_ip: config[:ip] }
        ansible.groups = groups
      end

      if "#{host}" == cluster.keys.last
        node.vm.provision :ansible do |ansible|
          ansible.playbook = "ansible/prepare.yaml"
          ansible.limit = 'all'
          ansible.groups = groups
          ansible.verbose = 'v'
        end
      end
    end
  end
end
