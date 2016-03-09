---
layout:     post
title:      Vagrant in Practice
date:       2014-06-08 11:21:29
summary:    Illustrate use of vagrant boxes
categories: development
---

Software developers are very edgy about their tools and pushing for the adoption of a new toolset always poses a major challenge. But there have been tools which creep in, into their  workflows.Vagrant has been one such tool; as is evident from the google worldwide trends .
Vagrant has slowly become a critical toolset for software development teams.
Most often than not , the typical workflow for a software team, follows the below mentioned trajectories.
For new software development projects
1. Development teams zero down on the software stack and the assisting tools.
Usually, this is where the discussion around the adoption of a VIRTUALIZATION PLATFORM comes up.
2. The Team adopts a virtualization platform and plan to use a VMWare workstation/Virtual Box with a fully operational guest OS during development. Here developers have to download the half-gigabyte operating system image and then configure the technology stack. In real world scenarios this is very unlikely and more limited to individual developers.
3. Mature/ Big teams usually are more eager to adopt a scriptable virtualization platform, as this provides a bedrock to the heavy infrastructure required for software development.
All the team needs is a virtualisation software package, something like VMware Workstation or VirtualBox, and some code.
4. Development team standardises  on using Vagrant/Docker as a virtual machine provisioner tool.
Current developer workflow & challenges:
In Software development it becomes critical to have the development environment mirror the production environment. This becomes more of a challenge with growing team size and for long duration projects.As the set of technologies in the stack expand and each big release adds it’s own new piece in the technology stack, teh problem starts to spiral out of control.
Having a highly configurable development environment that can be shared with the development team becomes a critical factor for a successful project.
What is vagrant:
Vagrant is an open-source tool that manages the provisioning and creation of configurable virtual development environments quickly, and in a reproducible way.
vagrant2.png
Advantages
Configure virtual machine hardware and installed technologies
           eg: If memcache is replaced with redis in one release. The vagrant         configuration  changes just needs to be committed to the repo.
All configuration changes can be put under version control and as such tied to releases.
Readily available development environment, mirroring the production environment, eases the friction to development process
Using Vagrant

Vagrant calls for minimal non-intrusive changes to the developer workflow. It is easier to adopt and integrate even for legacy projects where, it's not been part of the development workflow.
Below we go through the sequence of steps required to integrate Vagrant in a software project.
Installing Vagrant

We need to download a few tools for using Vagrant. Vagrant itself supports many different virtual platforms (providers ) such as Amazon EC2, VMware, RackSpace Cloud, and VirtualBox.
Instructions for using VMWare and AWS EC2 provider are available on the vagrant portal and as VirtualBox is a very common virtualization platform, we will be using it for demonstration.
We shall start by downloading and installing both on our local host machines:
        Vagrant         -- Download
        VirtualBox         – Download
Both VirtualBox and Vagrant support all common variants of host operating systems, so developers can use the one that works on their machine and get them installed.
Assuming you are using ubuntu 14.04 LTS as your host OS; the below commands need to be executed on the command line.
Installing Virtualbox:
$sudo apt-get install virtualbox
Installing Vagrant:
$sudo apt-get install vagrant
We need to install the dkms package to ensure that the VirtualBox host kernel modules (vboxdrv, vboxnetflt and vboxnetadp) are properly updated if the Linux kernel version changes during the next apt-get upgrade.
$sudo apt-get install virtualbox-dkms
Getting vagrant machine up:
Vagrant Images are bases OS which you will be using to replicate your production environment.This would be the same as your production environment. The following command will install trusty64 box(packaged OS image) from the vagrant website.
$vagrant box add ubuntu/trusty64 http://files.vagrantup.com/trusty64.box
Configure Project:
$mkdir vagrant_project
$cd vagrant_project
$vagrant init ubuntu/trusty64
This will create a file called Vagrantfile and is the central file for all project configurations.

Configuring Vagrant Instance

Vagrant configuration options are available in following block
Vagrant.configure("API_VERSION_NUMBER")
do |config|
 
end
Logical name of the base image or box used to built the Vagrant instance
config.vm.box= "ubuntu/trusty64"
Networking Configuration

Vagrant provides host-only and bridged networking. Please note that NFS based folders sharing requires host only networking with a static IP.
For example the following will configure a host only network on the Vagrant VM that is assigned a static IP of “192.168.33.10”

config.vm.network:private_network, ip: "192.168.33.10"
Port Forwarding

This option can be used to map ports between host and guest. Using the following, traffic sent to port 8000 on the host machine will be delivered to port 80 on the guest machine.

config.vm.network:forwarded_port, guest: 80, host: 8000

config.vm.network:forwarded_port, guest: 8000, host: 8000

Shared Folders/Synced Folders/NFS

You can create a shared folder mapping between host and guest by adding shared folder option. This maps the directory in guest “/home/vagrant/code” with the host directory “code”.
config.vm.synced_folder"./code", "/home/vagrant/code"
Starting Vagrant Instance
We can start the Vagrant instance by running following command, which will create a fully functional virtual machine.
vagrant up
This will take some time to boot.Once the virtual machine is up and running, using vagrant ssh will drop us into a fully functional terminal shell to the guest machine,
vagrant ssh
vagrant@trusty64://vagrant$
Halting

vagrant halt
This provides a graceful shutdown of virtual machine. To resume working again run vagrant up.
Reloading

vagrant reload
This will restart the virtual machine, applying any configuration change in Vagrantfile and run the provisioner.
Suspending

vagrant suspend
This will save the current running state of your virtual machine and then stop it. To resume working again run vagrant resume.
Destroying

vagrant destroy 
This will delete the complete virtual machine setup from the disk.
Specifying The Provisioner

Provisioners are used to install the software components as required.
We have the option of using the following provisioners
Shell
Ansible
Chef
Puppet
We shall cover the two most common providers, Shell and Chef.
Shell Provisioning:
We need to specify the shell provisioner as below, alongwith the bash script provisioner, which should be available in the root of your project directory (where your Vagrantfile is available).
A sample repo can be reference here.
config.vm.provision "shell", path: "./provisioner"
If any base configuration files are present, like apache host configuration etc. It can be provided as below.
config.vm.provision "file", source: "initial_setup", destination: "/tmp/initial_setup.
The file initial_setup is the bash script and should be located in the root of the project directory (where your Vagrantfile is available). Multiple bash files can be referred
For more option refer here.
Chef Provisioning

If you are new to chef head over here and for here chef provisioning. If you have been working with chef then you can follow the instructions to setup your environment.
By default, Vagrant will look in the “cookbooks” directory relative to the root of the project directory (where your Vagrantfile is available).
The cookbooks directory should have a structure similar to the following:
$ls cookbooks/
apache2/
mysql/
php/
redis/

The cookbooks directory should contain all the folders of the various cookbooks for all the software components required for development.
We need to specify the chef provisioner as below
 
config.vm.provision
:chef_solo do |chef|

And then point to the cookbooks directory
  
chef.cookbooks_path
= ["cookbooks", "~/project/cookbooks"]

Providing the Package List

By default, Vagrant has an empty run list, or the list of recipes or roles for Chef to run. You need to explicitly specify the run list for Vagrant using some basic configuration:
  
config.vm.provision
:chef_solo do |chef|
  
chef.add_recipe("apache")
  
chef.add_recipe("php")
Roles

Chef solo supports roles, which are specified within a roles directory.
  
chef.roles_path= "roles"  
chef.add_role("dev")
Downloading Packaged Cookbooks

Cookbooks which need to be downloaded can be
  
chef.recipe_url= "http://files.trumpet.com/cookbooks.tar.gz"
Provisioning Vagrant Instance

Once provisioner and related configuration details are specified, we can run provisioner using vagrant up or vagrant reload or vagrant provision. Vagrant reload will apply both changes in Vagrantfile as well as provisioner configuration. Running vagrant provision will just apply changes in the provisioner configuration.
Many a times custom Vagrant VMs are needed to be made available to a wider team
Building the Vagrant VMs for distribution
Build a base box with the relevant OS for vagrant.         
Use a configuration manager (e.g. Chef/Shell) to install the software components.         
Distribute the base boxes ( virtual machine images) for the users to develop         locally with vagrant. The distributed box will automatically pull in         source code from certain git repos (e.g. libraries).         
The pre-packaged base boxes, along with all the configuration, can be         made available to software teams.
For all those developers still thinking about adopting Vagrant, they just need to remind themselves of the time when they had to stop mid-way in their latest project and return to debug some priority issues in a legacy project.
In this case the developer workflow can be summarised
Assigned an issue from the issue tracker.         
Use the vagrant VM to clone the current dev repository into the folder it shares with the host OS. The developer can keep using his IDE for developing/debugging.         
Commits changes and tests locally
Push to remote and mark the issue as closed.
Vagrant and Docker have been gaining mass adoption at enterprises and the long term stability provided by these platforms makes it unavoidable for any software development team to do without it.

