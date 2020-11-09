# -*- mode: ruby -*-
# vi: set ft=ruby :


$commonCentosScript = <<-SCRIPT
yum update
exit 0
SCRIPT

$commonUbuntuScript = <<-SCRIPT
apt-get update
exit 0
SCRIPT

ssh_pub_key  = File.readlines("./ssh-key/kuber.pub").first.strip
ssh_priv_key = File.read("./ssh-key/kuber")
ssh_config   = File.read("./ssh-key/config")
$sshKeyProvision = <<-SCRIPT
echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
echo "#{ssh_priv_key}" > /home/vagrant/.ssh/kuber
echo "#{ssh_config}" > /home/vagrant/.ssh/config
chmod 600 /home/vagrant/.ssh/kuber
chmod 600 ~/.ssh/config
chown -R vagrant:vagrant /home/vagrant
SCRIPT

ENV['VAGRANT_NO_PARALLEL'] = 'yes'
ENV["LANG"]      = "en_US.UTF-8"
ENV["LC_ALL"]    = "en_US.UTF-8"

Vagrant.configure("2") do |config|


    NodeCount        = 1
    MasterCount      = 1

    config.vm.define "deploy" do |deploy|
        deploy.vm.network "private_network", ip: "10.240.0.101"
        deploy.vm.hostname = "deploy-10-240-0-10.kuber.net"
        deploy.vm.provider "virtualbox" do |vb|
            vb.name   = "deploy"
            vb.gui    = false
            vb.cpus   = 1
            vb.memory = "1024"
        end

        deploy.vm.provision "shell", inline: $sshKeyProvision, privileged: false
        deploy.vm.provision "shell", inline: $commonCentosScript

    end

    config.vm.define "proxy" do |proxy|
        proxy.vm.box    = "centos/7"
        proxy.vm.network "private_network", ip: "10.240.0.100"
        proxy.vm.box    = "centos/7"
        proxy.vm.hostname = "lb-10-240-0-11.kuber.net"
        proxy.vm.provider "virtualbox" do |vb|
            vb.name   = "lb"
            vb.gui    = false
            vb.cpus   = 1
            vb.memory = "512"
        end

        proxy.vm.provision "shell", inline: $sshKeyProvision, privileged: false
        proxy.vm.provision "shell", inline: $commonCentosScript

    end

    (0..MasterCount).each do |i|
        config.vm.define "master-#{i}" do |master|
            master.vm.box    = "ubuntu/bionic64"
            master.vm.network "private_network", ip: "10.240.0.3#{i}"
            master.vm.hostname = "master-10-240-0-3#{i}.kuber.net"

            master.vm.provider "virtualbox" do |vb|
                vb.name   = "master-#{i}"
                vb.gui    = false
                vb.cpus   = 2
                vb.memory = "2048"
            end

            master.vm.provision "shell", inline: $sshKeyProvision, privileged: false
            master.vm.provision "shell", inline: $commonUbuntuScript

        end
    end

    (0..NodeCount).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.box    = "ubuntu/bionic64"
            node.vm.network "private_network", ip: "10.240.0.4#{i}"
            node.vm.hostname = "node-10-240-0-4#{i}.akiryushin.net"

            node.vm.provider "virtualbox" do |vb|
                vb.name   = "node-#{i}"
                vb.gui    = false
                vb.cpus   = 2
                vb.memory = "2048"
            end

            node.vm.provision "shell", inline: $sshKeyProvision, privileged: false
            node.vm.provision "shell", inline: $commonUbuntuScript

        end
    end

end
