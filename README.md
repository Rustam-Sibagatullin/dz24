# Домашнее задание: VPN

##  Между двумя виртуалками поднять vpn в режим -tun

созададим Vgarantfile, согалсно методичке, котрый автоматизирует сборку:  

~~~
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
~~~

конфиги серевера и клиента:server.conf, client.conf атвомтически добавляются при разоваричивании VM.  

на серевере заускаем `iperf3 -s &`  
на клиенте запускаем `iperf3 -c 10.10.10.1 -t 40 -i 5`

Результат:
![картинка1](https://github.com/Rustam-Sibagatullin/dz24/blob/master/dev%20tap.JPG "iper tun mode")

***

## Между двумя виртуалками поднять vpn в режим -tup

в conf файлах tap меняем на tup.  
![картинка2](https://github.com/Rustam-Sibagatullin/dz24/blob/master/dev%20tun.JPG)

на серевере заускаем `iperf3 -s &`  
на клиенте запускаем `iperf3 -c 10.10.10.1 -t 40 -i 5`

Результат:
![картинка3](https://github.com/Rustam-Sibagatullin/dz24/blob/master/dev%20tun_1.JPG "iper tup mode")
