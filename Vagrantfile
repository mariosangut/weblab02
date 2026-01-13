# coding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "bento/debian-12"

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = 2048
    vb.cpus = 2
  end

  # Común a todas las máquinas
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update -y
  SHELL

  ####################################################################
  # dns.sistema.sol
  ####################################################################
  
  config.vm.define "dns" do |dns|

    dns.vm.hostname = "dns.sistema.sol"

    dns.vm.network "private_network",
      ip: "192.168.56.100",
      hostname: true,
      virtualbox__intnet: "internal"

    dns.vm.provision "shell", inline: <<-SHELL
      apt-get update -y
      apt-get install -y bind9 bind9-utils bind9-doc

      # DNS
      echo "nameserver 127.0.0.1" > /etc/resolv.conf
      echo "search sistema.sol" >> /etc/resolv.conf


      # Bind
      cp -v /vagrant/config/dns/named /etc/default/
      cp -v /vagrant/config/dns/named.conf.options /etc/bind
      cp -v /vagrant/config/dns/named.conf.local /etc/bind
      cp -v /vagrant/config/dns/db.192.168.56 /var/lib/bind
      cp -v /vagrant/config/dns/db.sistema.sol /var/lib/bind

      systemctl restart bind9
      systemctl status bind9 || true
    SHELL

  end

  ####################################################################
  # tierra.sistema.sol
  ####################################################################

  config.vm.define "tierra" do |tierra|

    tierra.vm.hostname = "tierra.sistema.sol"

    tierra.vm.network "private_network",
      ip: "192.168.56.101",
      hostname: true,
      virtualbox__intnet: "internal"

    # Acceso desde el host
    tierra.vm.network "forwarded_port", guest: 80,  host: 8080
    tierra.vm.network "forwarded_port", guest: 443, host: 8081

    tierra.vm.provision "shell", inline: <<-SHELL
      apt-get update -y
      apt-get install -y apache2

      # DNS
      echo "nameserver 192.168.56.100" > /etc/resolv.conf
      echo "search sistema.sol" >> /etc/resolv.conf
      
      # Root 
      cp -r /vagrant/config/tierra/discovery.sistema.sol /var/www/
    
      # VirtualHost 
      cp -v /vagrant/config/tierra/apache2.conf /etc/apache2
      cp -v /vagrant/config/tierra/discovery.sistema.sol.conf /etc/apache2/sites-available
      a2ensite discovery.sistema.sol.conf
      
      # Habilitar modulos necesarios
      a2enmod auth_basic
      a2enmod authz_groupfile
      a2enmod auth_digest

      # Servicios 
      systemctl reload apache2
      systemctl status apache2 || true
    SHELL

  end

end