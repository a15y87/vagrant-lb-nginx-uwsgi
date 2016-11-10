# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

N = 2
(1..N).each do |machine_id|
  config.vm.define "nginx#{machine_id}" do |nginx|
  end
end


config.vm.define "haproxy1" do |haproxy|
  haproxy.vm.network :forwarded_port, guest: 80, host: 8080, id: "haproxy1"
  haproxy.vm.network :forwarded_port, guest: 81, host: 8081, id: "haproxy1-stats"
end


config.vm.provision "ansible" do |ansible|
   ansible.limit = 'all'
   ansible.playbook = "datanyze.playbook.yml"
   ansible.groups = {
		"nginx" => ["nginx[1:#{N}]"],
		"haproxy" => ["haproxy1"],
		"all_groups:children" => ["haproxy", "nginx"],
		"nginx:vars" => {"proxy_proto_port" => 88, "http_proto_port" => 80},
		"haproxy:vars" => { "haproxy_config" => "datanyze.cfg" }
	}
end

end
