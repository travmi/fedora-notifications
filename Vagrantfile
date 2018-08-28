# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
 config.vm.box = "fedora/28-cloud-base"

 # Forward traffic on the host to the development server on the guest
 config.vm.network "forwarded_port", guest: 5000, host: 5000
 config.vm.network "forwarded_port", guest: 6667, host: 6667
 config.vm.network "forwarded_port", guest: 15672, host: 15672

 if Vagrant.has_plugin?("vagrant-hostmanager")
     config.hostmanager.enabled = true
     config.hostmanager.manage_host = true
 end

 # Vagrant can share the source directory using rsync, NFS, or SSHFS (with the vagrant-sshfs
 # plugin). By default it rsyncs the current working directory to /vagrant.
 #
 # If you would prefer to use NFS to share the directory uncomment this and configure NFS
 # config.vm.synced_folder ".", "/vagrant", type: "nfs", nfs_version: 4, nfs_udp: false
 config.vm.synced_folder ".", "/vagrant", disabled: true
 config.vm.synced_folder ".", "/home/vagrant/fedora-notifications", type: "sshfs", sshfs_opts_append: "-o nonempty"
 config.vm.synced_folder "../fedora-messaging", "/home/vagrant/fedora-messaging", type: "sshfs", sshfs_opts_append: "-o nonempty"

 # To cache update packages (which is helpful if frequently doing `vagrant destroy && vagrant up`)
 # you can create a local directory and share it to the guest's DNF cache. The directory needs to
 # exist, so create it before you uncomment the line below.
 #
 # config.vm.synced_folder ".dnf-cache", "/var/cache/dnf", type: "sshfs", sshfs_opts_append: "-o nonempty"

 # Comment this line if you would like to disable the automatic update during provisioning
 config.vm.provision "shell", inline: "sudo dnf upgrade -y"

 # bootstrap and run with ansible
 config.vm.provision "shell", inline: "sudo dnf -y install python2-dnf libselinux-python"
 config.vm.provision "ansible" do |ansible|
     ansible.playbook = "devel/ansible/vagrant-playbook.yml"
 end


 config.vm.define "fn" do |fn|
    fn.vm.host_name = "fn.example.com"

    fn.vm.provider :libvirt do |domain|
        # Season to taste
        domain.cpus = 4
        domain.graphics_type = "spice"
        domain.memory = 4096
        domain.video_type = "qxl"

        # Uncomment the following line if you would like to enable libvirt's unsafe cache
        # mode. It is called unsafe for a reason, as it causes the virtual host to ignore all
        # fsync() calls from the guest. Only do this if you are comfortable with the possibility of
        # your development guest becoming corrupted (in which case you should only need to do a
        # vagrant destroy and vagrant up to get a new one).
        #
        # domain.volume_cache = "unsafe"
    end
 end
end
