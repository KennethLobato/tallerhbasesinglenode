Vagrant.configure(2) do |config|
  #Define two virtual machines, ubuntu1 and ubuntu2
  config.vm.define "ubuntu1" do |ubuntu1|
    #Select the image that we want
    ubuntu1.vm.box = "ubuntu/trusty64"
    #Setup a private network that allows both machines comms.
    ubuntu1.vm.network "private_network", ip:"10.0.0.10"
    ubuntu1.vm.hostname = "ubuntu1"
    ubuntu1.vm.provision "shell", inline: <<-SHELL
      echo "<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?> \
<configuration> \
<property> \
<name>hbase.rootdir</name> \
<value>file:///tmp/hbase</value> \
</property> \
<property> \
<name>hbase.zookeeper.property.dataDir</name> \
<value>file:///tmp/zookeeper</value> \
</property> \
</configuration> "> /usr/local/hbase/conf/hbase-site.conf
    export JAVA_HOME="/usr/lib/jvm/java-7-oracle/" && /usr/local/hbase/bin/start-hbase.sh
    SHELL
  end

  config.vm.provider "virtualbox" do |vb|
    #vb.memory = 2048
    #vb.memory = 3072
    vb.memory = 4096
    #vb.memory = 6192
    vb.cpus = 2
  end

  config.vm.provision "shell", inline: <<-SHELL
    #Add Java repository
    sudo apt-add-repository -y ppa:webupd8team/java

    #Update machine
    sudo apt-get update

    #Install outdated software
    sudo apt-get -y upgrade

    #Automatically Accept Java 7 and 8 License
    sudo echo oracle-java7-installer shared/accepted-oracle-license-v1-1 select true | sudo /usr/bin/debconf-set-selections
    sudo echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | sudo /usr/bin/debconf-set-selections

    #Install Java7
    sudo apt-get install -y oracle-java7-installer

    #Setup bashrc
    echo "export JAVA_HOME=/usr/lib/jvm/java-7-oracle/" >> /home/vagrant/.bashrc

    #Disable IPV6
    sudo echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf
    sudo echo "net.ipv6.conf.default.disable_ipv6 = 1" >> /etc/sysctl.conf
    sudo echo "net.ipv6.conf.lo.disable_ipv6 = 1" >> /etc/sysctl.conf

    sudo sysctl -p

    #Configure hostname resolution
    sudo echo "127.0.0.1 localhost" > /etc/hosts
    sudo echo "10.0.0.10 ubuntu1.tallerhadoop1.org ubuntu1" >> /etc/hosts

    if [ ! -f /vagrant/hbase-1.2.4-bin.tar.gz ]
    then
      sudo wget http://apache.rediris.es/hbase/1.2.4/hbase-1.2.4-bin.tar.gz -P /vagrant/
    fi
    if [ -f /vagrant/hbase-1.2.4-bin.tar.gz ]
    then
      if [ ! -d /usr/local/hbase-1.2.4 ]
      then
        sudo tar -zxvf /vagrant/hbase-1.2.4-bin.tar.gz -C /usr/local/
        sudo ln -sf /usr/local/hbase-1.2.4/ /usr/local/hbase
        sudo chown -R vagrant /usr/local/hbase
        sudo chown -R vagrant /usr/local/hbase-1.2.4
        echo "export PATH=$PATH:/usr/local/hbase/bin" >> /home/vagrant/.bashrc
      fi
    fi
  SHELL
end
