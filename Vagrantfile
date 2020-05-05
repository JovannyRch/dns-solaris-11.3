# -*- mode: ruby -*-
# vi: set ft=ruby :

BOX = "gdams/solaris11.3"
Vagrant.configure("2") do |config|



  config.vm.define "server" do |subconfig|
    subconfig.vm.box = BOX
    subconfig.vm.box_version = "1.0.0"
    subconfig.vm.network "public_network", use_dhcp_assigned_default_route: true
    subconfig.vm.hostname = "server-jovanny"
    subconfig.vm.provision "shell", inline: <<-SHELL
	      echo Soy el servidor
        pkg install pkg:/service/network/dns/bind
        mkdir /var/named/
        mkdir /var/named/data
        mkdir -p /var/run/namedb
        mkdir -p /etc/namedb/master
        mkdir -p /etc/namedb/working
        cp /actualizar_servidor /
        cp /vagrant/named.conf /etc/
        cp /vagrant/forward.localdomain /var/named/
        cp /vagrant/reverse.localdomain /var/named/
        named-checkconf /etc/named.conf
        named-checkzone localdomain /var/named/forward.localdomain
        named-checkzone localdomain /var/named/reverse.localdomain
        svcadm enable network/dns/server:default
        svcadm refresh network/dns/server:default
        svcs | grep dns/server
    SHELL
  end

  config.vm.define "client" do |subconfig|
    subconfig.vm.box = BOX
    subconfig.vm.box_version = "1.0.0"
    subconfig.vm.hostname = "client-jovanny"
    subconfig.vm.network "public_network", use_dhcp_assigned_default_route: true
    subconfig.vm.provision "shell", inline: <<-SHELL
        echo Soy el cliente
        pkg install service/network/dns/bind
        svccfg -s network/dns/client setprop config/nameserver = net_address: 192.168.0.104
        svccfg -s network/dns/client setprop config/domain = astring: midominio
        svccfg -s network/dns/client setprop config/search = astring: midominio
        svccfg -s name-service/switch setprop config/ipnodes = astring: '"files dns"'
        svccfg -s name-service/switch setprop config/host = astring: '"files dns"'
        svccfg -s network/dns/client listprop config
        svcadm enable dns/client
        nslookup dnsserver
    SHELL
  end

  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
