# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

N = 2
(1..N).each do |machine_id|
  config.vm.define "nginx#{machine_id}" do |nginx|
  nginx.vm.hostname = "nginx#{machine_id}"
  nginx.vm.network :private_network, ip: "10.0.0.1#{machine_id}"
  end
end


config.vm.define "haproxy1" do |haproxy|
  haproxy.vm.network :private_network, ip: "10.0.0.254"
  haproxy.vm.hostname = "haproxy1"
  haproxy.vm.network "forwarded_port", guest: 80, host: 8080, protocol: "tcp"
  haproxy.vm.network "forwarded_port", guest: 81, host: 8081, protocol: "tcp"
end


config.vm.provision "ansible" do |ansible|
   ansible.limit = "datanyze"
   ansible.playbook = "datanyze.playbook.yml"
   ansible.groups = {
		"nginx" => ["nginx[1:#{N}]"],
		"haproxy" => ["haproxy1"],
		"datanyze:children" => ["haproxy", "nginx"],
		"datanyze:vars" => {
						"haproxy_config" => "datanyze.cfg",
						"proxy_proto_port" => 88,
						"http_proto_port" => 80,
						"app_user" => "www-data",
						"nginx_conf" => "nginx_uwsgi.conf",
						"logfile" => "/var/log/datanyze-logs/test-access.log",
						"site_conf" => "datanyze_uwsgi.conf",
						"logrotate"  => "datanyze_uwsgi_logrotate.conf",
						"logrotate_days" => 7,
						"uwsgi_app_socket" => "127.0.0.1:8088",
						"uwsgi_app" => "test-datanyze",
						"uwsgi_app_file" => "test-datanyze.py",
						},
	}
end

end
