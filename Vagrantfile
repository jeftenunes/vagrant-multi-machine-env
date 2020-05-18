$script_mysql = <<-SCRIPT
  apt-get update && \
  apt-get install -y mysql-server-5.7 && \
  mysql -e "create user 'phpuser'@'%' identified by 'pass';"
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.define "mysqldb" do |mysql|
    mysql.vm.network "public_network", ip: "192.168.1.27"

    mysql.vm.provision "shell", inline: "cat /configs/id_bionic.pub >> .ssh/authorized_keys"
    mysql.vm.provision "shell", inline: $script_mysql
    mysql.vm.provision "shell", inline: "cat /configs/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf"
    mysql.vm.provision "shell", inline: "service mysql restart"
    mysql.vm.synced_folder "./configs", "/configs"
    mysql.vm.synced_folder ".", "/vagrant", disabled: true
  end
  
  config.vm.define "php" do |web|
    web.vm.network "forwarded_port", guest: 8888, host: 8888
    web.vm.network "public_network", ip: "192.168.1.25"
    web.vm.provision "shell", inline: "sudo apt-get update && sudo apt-get install -y puppet"
    web.vm.provision "puppet" do |puppet|
      puppet.manifests_path = "./configs/manifests"
      puppet.manifest_file = "web.pp" 
    end
  end
end
