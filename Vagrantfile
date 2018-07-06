# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV["LC_ALL"] = "en_US.UTF-8"

numberOfPublish = 1
hostPublishPort = 4503

ansible_groups = {}
ansible_host_vars = {}

ansible_groups["publish_group"] = ["publish-[1:#{numberOfPublish}]"]


Vagrant.configure("2") do |config|

    config.vm.define "author" do |author|
        author.vm.box = "centos/7"
        author.vm.network "forwarded_port", guest: 4502, host: 4502, host_ip: "127.0.0.1"
        author.vm.network "private_network", type: "dhcp"

        author.vm.provider "virtualbox" do |vb|
            vb.name = "author"
            vb.memory = 1024
            vb.cpus = 2
        end
        author.vm.hostname = "author"

        # ansible_host_vars["author"] = {"aem_runmode" => "author",
        #                                 "aem_port" => "4502"}
        ansible_groups["author_group"] = ["author"]    
    end

    (1..numberOfPublish).each do |id|
        config.vm.define "publish-#{id}" do |publish|
            publish.vm.box = "centos/7"
            publish.vm.network "forwarded_port", guest: 4503, host: "#{hostPublishPort += 1}", host_ip: "127.0.0.1"
            publish.vm.network "private_network", type: "dhcp"

            publish.vm.provider "virtualbox" do |vb|
                vb.name = "publish-#{id}"
                vb.memory = 1024
                vb.cpus = 2
            end
            publish.vm.hostname = "publish"

            # ansible_host_vars["publish"] = {"aem_runmode" => "publish",
            #                                 "aem_port" => "4503"}
        end
    end

    config.vm.define "workaround" do |workaround|
        workaround.vm.box = "hashicorp/precise64"
        workaround.vm.provider "virtualbox" do |vb|
            vb.name = "workaround"
            vb.memory = 256
            vb.cpus = 1
        end
        workaround.vm.provision "ansible" do |ansible|
            ansible.limit = "all:!workaround"
            ansible.groups = ansible_groups
            ansible.host_vars = ansible_host_vars
            ansible.playbook = "main.yml"
            ansible.host_key_checking = false
            # ansible.verbose = "-vvv"
            # ansible.tags = "common-users"
        end
    end


end
