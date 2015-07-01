# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|

  config.landrush.enabled = true
  config.landrush.tld = 'vm'
  config.landrush.guest_redirect_dns = true
  config.landrush.upstream '8.8.8.8'

  config.vm.define 'cassandra' do |db|
    db.vm.box = 'ubuntu1404lts'
    db.vm.box_url = 'https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box'
    db.vm.hostname = 'cassandra.vagrant.vm'

    db.vm.provider 'virtualbox' do |v|
      v.memory = 3000
      v.cpus = 2
    end

    db.vm.provision 'shell', inline: 'sudo apt-get -y update'
    db.vm.provision 'shell', inline: 'sudo apt-get -y install software-properties-common supervisor'
    db.vm.provision 'shell', inline: 'echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | debconf-set-selections'
    db.vm.provision 'shell', inline: 'sudo add-apt-repository -y ppa:webupd8team/java'
    db.vm.provision 'shell', inline: 'sudo apt-get -y update'
    db.vm.provision 'shell', inline: 'sudo apt-get install -y oracle-java8-installer'
    db.vm.provision 'shell', inline: 'wget -q https://s3-us-west-2.amazonaws.com/packages.dualspark.com/static/cassandra/apache-cassandra-2.1.7-bin.tar.gz -O /tmp/cassandra.tar.gz'
    db.vm.provision 'shell', inline: 'sudo mkdir -p /opt/cassandra'
    db.vm.provision 'shell', inline: 'sudo tar xvf /tmp/cassandra.tar.gz -C /opt/cassandra --strip 1'
    db.vm.provision 'shell', inline: 'ADDRESSES=($(hostname -I));sed -i "s/localhost/${ADDRESSES[1]}/g" /opt/cassandra/conf/cassandra.yaml'
    db.vm.provision 'shell', inline: 'ADDRESSES=($(hostname -I));sed -i "s/127.0.0.1/${ADDRESSES[1]}/g" /opt/cassandra/conf/cassandra.yaml'
    db.vm.provision 'file', source: 'cassandra.supervisor.conf', destination: '/tmp/cassandra.conf'
    db.vm.provision 'shell', inline: 'sudo mv /tmp/cassandra.conf /etc/supervisor/conf.d/cassandra.conf'
    db.vm.provision 'shell', inline: 'sudo service supervisor restart'
  end

  config.vm.define 'kong' do |kong|
    kong.vm.box = 'ubuntu1404lts'
    kong.vm.box_url = 'https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box'

    kong.vm.hostname = 'kong.vagrant.vm'

    kong.vm.provision 'shell', inline: 'sudo apt-get -y update'
    kong.vm.provision 'shell', inline: 'sudo apt-get -y install netcat lua5.1 openssl libpcre3 dnsmasq'
    kong.vm.provision 'shell', inline: 'wget -q https://s3-us-west-2.amazonaws.com/packages.dualspark.com/static/kong/kong-0.3.2.precise_all.deb -O /tmp/kong.deb'
    kong.vm.provision 'shell', inline: 'sudo dpkg -i /tmp/kong.deb'
    kong.vm.provision 'shell', inline: 'sed -i "s/localhost/cassandra.vagrant.vm/g" /etc/kong/kong.yml'
    kong.vm.provision 'shell', inline: 'sudo kong start'

    # go check out how to configure it now: http://getkong.org/docs/0.3.2/getting-started/quickstart/
  end

end
