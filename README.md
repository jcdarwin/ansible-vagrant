#Vagrant

1. Read these first:

	[http://docs.vagrantup.com/v2/getting-started/project_setup.html](http://docs.vagrantup.com/v2/getting-started/project_setup.html)

	[http://julien.ponge.org/blog/scalable-and-understandable-provisioning-with-ansible-and-vagrant/](http://julien.ponge.org/blog/scalable-and-understandable-provisioning-with-ansible-and-vagrant/)

	[http://blog.dhananjaynene.com/2013/10/hands-on-ansible-tutorial/](http://blog.dhananjaynene.com/2013/10/hands-on-ansible-tutorial/)

	[http://www.stavros.io/posts/example-provisioning-and-deployment-ansible/](http://www.stavros.io/posts/example-provisioning-and-deployment-ansible/)

	[http://www.stavros.io/posts/automated-large-scale-deployments-ansibles-pull-mo/](http://www.stavros.io/posts/automated-large-scale-deployments-ansibles-pull-mo/)

	[http://lextoumbourou.com/blog/posts/getting-started-with-ansible/](http://lextoumbourou.com/blog/posts/getting-started-with-ansible/)

	[https://coderwall.com/p/lxchtg](https://coderwall.com/p/lxchtg)

1. Install vagrant

		$ mkdir /workspace/vagrant
		$ cd /workspace/vagrant
		$ vagrant init

1. Download and install [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

1. Edit the vagrant file

		# -*- mode: ruby -*-
		# vi: set ft=ruby :

		# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
		VAGRANTFILE_API_VERSION = "2"

		Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
			# http://blog.dhananjaynene.com/2013/10/hands-on-ansible-tutorial/
			config.vm.box = "precise32"
			config.vm.box_url = "http://files.vagrantup.com/precise32.box"

			config.vm.provision "shell", inline: "echo Hello"

			config.vm.define "web" do |web|
				web.vm.box = "precise32"
				web.vm.hostname = "web"
				web.vm.network "private_network", ip: "192.168.101.11"
			end

			config.vm.define "db" do |db|
				db.vm.box = "precise32"
				db.vm.hostname = "db"
				db.vm.network "private_network", ip: "192.168.101.12"
			end
		end

1. Start vagrant and SSH into it

		$ vagrant up web
		$ vagrant ssh web

1. Enable a passwordless sudo, on both the machines

		$ sudo visudo
		# add the following line near the end of the file
		%vagrant ALL=(ALL:ALL) NOPASSWD: ALL

#Ansible

1. [Install ansible](http://www.ansibleworks.com/docs/intro_installation.html)

		$ cd /workspace
		$ git clone git://github.com/ansible/ansible.git
		$ cd ./ansible
		$ source ./hacking/env-setup

1. Install easy_install and pip

		$ brew install easy_install
		$ sudo easy_install pip

1. Install python dependencies

		$ sudo pip install paramiko PyYAML jinja2

1. Check ansible can see the hosts

		$ ansible -i hosts -a hostname web
		$ ansible -i hosts -a "sudo ifconfig" web
		$ ansible webservers -m command -a "uptime" -i hosts
		$ ansible all -m ping -i hosts -l webservers

1. Create our playbook files, as per those in this directory

1. Run ansible locally to create the key files

		$ ./localhost.sh
		# ansible-playbook -vvv -i hosts localhosts.yml --connection=local -K

1. Run ansible to provision the hosts

		$ ansible-playbook -i hosts site.yml

#SSH

* To determine the running VMs:

		$ VBoxManage list runningvms

* To determine the port to ssh in on, start VirtualBox and examine Setings > Network > Port Forwarding 
to find the Host Port that is mapped to a Guest Port of 22


* To determine where Vagrant's keyfile has been created (where server name is suppied if this is a multi-server install):

		$ vagrant ssh-config [server name] | grep IdentityFile  | awk '{print $2}'

* To login using normal SSH 

  	* with a password

			sudo ssh -l jasondarwin -p 2200 127.0.0.1

	* with public/private keys

			sudo ssh -vvv -i /Users/jasondarwin/workspace/ansible-vagrant/.user_add/keys/jasondarwin -l jasondarwin -p 2200 127.0.0.1
