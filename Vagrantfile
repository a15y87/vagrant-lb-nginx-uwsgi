# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :machine
    config.cache.enable :apt
  end


datanyze_cluster = {
  "nginx-1" => { :ipv4_address => "10.0.1.11"},
  "nginx-2" => { :ipv4_address => "10.0.1.12"},
  "haproxy-1"  => { :ipv4_address => "10.0.1.254"},
}


datanyze_cluster.each_with_index do |(hostname, cfg), index|
  config.vm.define hostname do |node|
    node.vm.hostname = hostname
    node.vm.network :private_network, ip: "#{cfg[:ipv4_address]}"

	  if index == datanyze_cluster.size - 1
        node.vm.provision :ansible do |ansible|
          ansible.host_vars = datanyze_cluster
          ansible.verbose = "vv"
		  ansible.limit = "all"
		  ansible.groups = {
			"nginx" => ["nginx-[1:2]"],
			"haproxy" => ["haproxy-1"],
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
				}
	       	}
		  ansible.playbook = "datanyze.playbook.yml"
      end #ansible provision
    end # if
  end # config.vm.define
end # datanyze_cluster.each_with_index


end
