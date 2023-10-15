# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # VM s0.infra
  config.vm.define "s0" do |s0|
    s0.vm.box = "ubuntu/bionic64"
    s0.vm.hostname = "s0.infra"

    s0.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 1
      vb.name = "s0.infra"
    end

    s0.vm.network "private_network", type: "dhcp", auto_config: false
    s0.vm.network "public_network"

    s0.vm.provision "shell", inline: <<-SHELL
      apt-get update && apt-get upgrade -y
      apt-get install -y dnsmasq haproxy
      echo 'dhcp-range=192.168.50.50,192.168.50.150,12h' >> /etc/dnsmasq.conf
      echo 'server=1.1.1.1' >> /etc/dnsmasq.conf
      echo 'domain=infra' >> /etc/dnsmasq.conf
      echo 'no-resolv' >> /etc/dnsmasq.conf
      service dnsmasq restart

      cat <<-EOL > /etc/haproxy/haproxy.cfg
      global
          log /dev/log local0
          log /dev/log local1 notice
          chroot /var/lib/haproxy
          stats timeout 30s
          user haproxy
          group haproxy
          daemon
      
      defaults
          log global
          mode http
          option httplog
          option dontlognull
          timeout connect 5000
          timeout client 50000
          timeout server 50000
      
      frontend http_front
         bind *:80
         stats uri /haproxy?stats
         default_backend http_back
      
      backend http_back
         balance roundrobin
         server s1 192.168.50.11:80 check
         server s2 192.168.50.12:80 check
      EOL

      service haproxy restart
    SHELL
  end

  # VM s1.infra
  config.vm.define "s1" do |s1|
    s1.vm.box = "ubuntu/bionic64"
    s1.vm.hostname = "s1.infra"
      
    s1.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
      vb.name = "s1.infra"
    end
      
    s1.vm.network "private_network", ip: "192.168.50.11"
    s1.vm.network "public_network"

    s1.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2 php libapache2-mod-php mysql-client nfs-common

      # Configuration Apache pour WordPress
      rm /etc/apache2/sites-available/000-default.conf
      cat <<EOL > /etc/apache2/sites-available/wordpress
      <VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        ServerName devops.com
        ServerAlias www.devops.com
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>

      <VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        ServerName devsec.com
        ServerAlias www.devsec.com
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>

      <VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        ServerName devsecops.com
        ServerAlias www.devsecops.com
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>
      EOL

      a2ensite wordpress
      systemctl reload apache2

      mkdir /mnt/nfs
      echo "s4.infra:/home/data /mnt/nfs nfs defaults 0 0" >> /etc/fstab
      mount -a
    SHELL
  end

  # VM s2.infra
  config.vm.define "s2" do |s2|
    s2.vm.box = "ubuntu/bionic64"
    s2.vm.hostname = "s2.infra"
      
    s2.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
      vb.name = "s2.infra"
    end
      
    s2.vm.network "private_network", ip: "192.168.50.12"
    s2.vm.network "public_network"

    s2.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2 php libapache2-mod-php mysql-client nfs-common

      # Configuration Apache pour WordPress
      rm /etc/apache2/sites-available/000-default.conf
      cat <<EOL > /etc/apache2/sites-available/wordpress
      <VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        ServerName devops.com
        ServerAlias www.devops.com
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>

      <VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        ServerName devsec.com
        ServerAlias www.devsec.com
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>

      <VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        ServerName devsecops.com
        ServerAlias www.devsecops.com
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>
      EOL

      a2ensite wordpress
      systemctl reload apache2

      mkdir /mnt/nfs
      echo "s4.infra:/home/data /mnt/nfs nfs defaults 0 0" >> /etc/fstab
      mount -a
    SHELL
  end

  # VM s3.infra
  config.vm.define "s3" do |s3|
    s3.vm.box = "ubuntu/bionic64"
    s3.vm.hostname = "s3.infra"
        
    s3.vm.provider "virtualbox" do |vb|
      vb.memory = "4096"
      vb.cpus = 2
      vb.name = "s3.infra"
    end
        
    s3.vm.network "private_network", ip: "192.168.50.13"
    s3.vm.network "public_network"

    s3.vm.provision "shell", inline: <<-SHELL
      apt-get update
      export DEBIAN_FRONTEND=noninteractive
      apt-get install -y mariadb-server
      # (Votre configuration MariaDB ici)

      systemctl enable mysql
      systemctl start mysql
    SHELL
  end

  # VM s4.infra
  config.vm.define "s4" do |s4|
    s4.vm.box = "ubuntu/bionic64"
    s4.vm.hostname = "s4.infra"
    
    s4.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 1
      vb.name = "s4.infra"
    end
        
    s4.vm.network "private_network", ip: "192.168.50.14"
    s4.vm.network "public_network"

    s4.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y nfs-kernel-server
      mkdir -p /home/data
      echo "/home/data 192.168.50.0/24(rw,sync,no_subtree_check)" >> /etc/exports
      systemctl restart nfs-kernel-server
    SHELL
  end
end