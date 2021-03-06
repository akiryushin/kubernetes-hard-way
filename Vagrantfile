# -*- mode: ruby -*-
# vi: set ft=ruby :


$commonCentosScript = <<-SCRIPT
yum update
SCRIPT


Vagrant.configure("2") do |config|

    config.vm.box = "centos/7"
    ENV["LANG"] = "en_US.UTF-8"
    ENV["LC_ALL"] = "en_US.UTF-8"

    NodeCount = 1
    MasterCount = 2

    config.vm.define "deploy" do |deploy|
        deploy.vm.network "private_network", ip: "10.240.0.10"
        deploy.vm.hostname = "deploy-10.240.0.10.kuber.net"
        deploy.vm.provider "virtualbox" do |vb|
            vb.name = "deploy"
            vb.gui = false
            vb.cpus = 1
            vb.memory = "1024"
        end

        deploy.vm.provision "shell", inline: $commonUbuntuScript
    end

    config.vm.define "proxy" do |proxy|
        proxy.vm.network "private_network", ip: "10.240.0.12"
        proxy.vm.hostname = "lb-10.240.0.11.kuber.net"
        proxy.vm.provider "virtualbox" do |vb|
            vb.name = "lb"
            vb.gui = false
            vb.cpus = 1
            vb.memory = "1024"
        end

        proxy.vm.provision "shell", inline: $commonUbuntuScript
    end

    (0..MasterCount).each do |i|
        config.vm.define "master-#{i}" do |master|
            master.vm.network "private_network", ip: "10.240.0.3#{i}"
            master.vm.hostname = "master-10.240.0.3#{i}.kuber.net"

            master.vm.provider "virtualbox" do |vb|
                vb.name = "master-#{i}"
                vb.gui = false
                vb.cpus = 2
                vb.memory = "2048"
            end

            master.vm.provision "shell", inline: $commonUbuntuScript
        end
    end

    (0..NodeCount).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.network "private_network", ip: "10.240.0.4#{i}"
            node.vm.hostname = "node-10.240.0.4#{i}.akiryushin.net"

            node.vm.provider "virtualbox" do |vb|
                vb.name = "node-#{i}"
                vb.gui = false
                vb.cpus = 2
                vb.memory = "2048"
            end

            node.vm.provision "shell", inline: $commonUbuntuScript
        end
    end

end
