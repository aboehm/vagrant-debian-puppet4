# Vagrant virtualbox debian based environment with puppet 4 setup
# Copyright (C) 2016 Alexander Böhm <alxndr.boehm@gmail.com>
# 
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU Affero General Public License as
# published by the Free Software Foundation, either version 3 of the
# License, or (at your option) any later version.
# 
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU Affero General Public License for more details.
# 
# You should have received a copy of the GNU Affero General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.

# use interactive 
USE_GUI      = false

# filesystem synchronisation method
VM_SYNC_TYPE = "rsync"

# optional debian proxy
#DEB_PROXY    = "http://deb.proxy:3142"
DEB_PROXY    = ""

# default values
PUPPET_DIR             = "puppet"
PUPPET_ENVIRONMENT_DIR = PUPPET_DIR + "/environments"
PUPPET_ENVIRONMENT     = "production"
PUPPET_HIERA_CONFIG    = PUPPET_DIR + "/hiera.yaml"

# node setup method
def setup_node(vconfig, name, box, cpus, ram, reload, ip)
  vconfig.vm.define name do |config|
  	config.vm.hostname = name
    config.vm.box = box
    
    # virtualbox setup
    config.vm.provider "virtualbox" do |vb|
      vb.gui = USE_GUI
			vb.cpus = cpus
      vb.memory = ram

      # Ensure NAT is running correctly
      vb.customize ['modifyvm', :id, '--cableconnected1', 'on']
      vb.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
    end

    # configure debian package proxy, if given, and install puppet
    config.vm.provision "shell", inline: <<-SHELL
      if [ "#{DEB_PROXY}" ] ; then
        echo 'Acquire::http::proxy "#{DEB_PROXY}/";' > /etc/apt/apt.conf.d/01proxy
      else
        > /etc/apt/apt.conf.d/01proxy
      fi 

      DPKG_STATUS=`dpkg -s puppetlabs-release-pc1`
      if [ $? -ne 0 ] || [ -z "`echo $DPKG_STATUS |grep Status |grep 'install ok installed'`" ] ; then
        wget -O /tmp/puppetlabs-release.deb https://apt.puppetlabs.com/puppetlabs-release-pc1-`/usr/bin/lsb_release -c -s`.deb
        sudo dpkg -i /tmp/puppetlabs-release.deb
        sudo apt-get update
        sudo apt-get install --no-install-recommends --yes puppet-agent vim
      fi
    SHELL

    if ip != ""
      # No ip adress is given. Only NAT will be used
      config.vm.network "private_network", ip: ip
    end

    config.vm.synced_folder ".", "/vagrant", type: VM_SYNC_TYPE

    # provision with puppet 4
    config.vm.provision "puppet" do |p|
      p.synced_folder_type = VM_SYNC_TYPE
      p.environment_path   = PUPPET_ENVIRONMENT_DIR
			p.environment        = PUPPET_ENVIRONMENT
      p.hiera_config_path  = PUPPET_HIERA_CONFIG
    end

    # if reboot needed, do it
    if reload == true 
      config.vm.provision :reload
    end
  end
end

# automatic reload plugin for boot customizing
unless Vagrant.has_plugin?("vagrant-reload")
  puts "Plugin vagrant-reload is missing, but required!"
  puts ""
  puts "Install it with: vagrant plugin install vagrant-reload"
  puts ""
  exit
end

# node declarations
Vagrant.configure(2) do |config|
  # setup_node(config, "example", VM_BASE, CPUS, RAMSIZE, false, "")
end

# vi: set ft=ruby tabstop=2 shiftwidth=2 expandtab :
