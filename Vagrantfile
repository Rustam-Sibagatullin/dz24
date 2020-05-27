# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure(2) do |config|
	config.vm.box = "centos/7"
	config.vm.define "server" do |server|
		server.vm.hostname = "server.loc"
		server.vm.network "private_network", ip: "192.168.10.10"
                config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
                #server.vm.provision "file", source: "static_server.conf", destination: "/tmp/static.conf"
                server.vm.provision "shell", inline: <<-SHELL
                yum install -y epel-release
                yum install -y openvpn iperf3
	        setenforce 0
	        openvpn --genkey --secret /etc/openvpn/static.key
	        cp /etc/openvpn/static.key /vagrant/static.key
                cp /vagrant/server.conf /etc/openvpn/server.conf
                systemctl start openvpn@server
	        systemctl enable openvpn@server
                SHELL
                end
	
	config.vm.define "client" do |client|
		client.vm.hostname = "client.loc"
		client.vm.network "private_network", ip: "192.168.10.20"
                #config.vm.synced_folder ".", "/vagrant/"
	        #client.vm.provision "file", source: "static_client.conf", destination: "/tmp/static.conf"
                client.vm.provision "shell", inline: <<-SHELL
                yum install -y epel-release
                yum install -y openvpn iperf3
	        setenforce 0
                cp /vagrant/client.conf /etc/openvpn/server.conf
                cp /vagrant/static.key /etc/openvpn/static.key
                systemctl start openvpn@server
                systemctl enable openvpn@server
                SHELL


end
end

