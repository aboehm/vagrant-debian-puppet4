# Vagrant environment for puppet 4 (debian based)

## Overview

A template for a Vagrant environment for puppet 4 based provisiong with debian
based systems.

## Usage

### Basic setup

The `Vagrantfile` contains following constants

* `USE_GUI`: Headless or GUI mode. Default: headless. 
* `VM_SYNC_TYPE`: Sychronisation method of folders. Default: rsync. (see
   https://www.vagrantup.com/docs/synced-folders/basic_usage.html)
* `DEB_PROXY`: Url of a apt proxy. Empty if no proxy used (default).

### Node definition

To create a node, go to the node declarations section and use the setup_node
method. The following declaration will create a instance `example` based on the
`debian/jessie64` box with two cpu cores, 4096 MB of RAM and one ethernet
interface connected over NAT.

~~~
# node declarations
Vagrant.configure(2) do |config|
  setup_node(config, "example", "debian/jessie64", 2, 4096, false, "")
end
~~~

To add a host-only interface give the instance an ip adress e.g 10.0.0.2.

~~~
# node declarations
Vagrant.configure(2) do |config|
  setup_node(config, "example", "debian/jessie64", 2, 4096, false, "10.0.0.2")
end
~~~

For more specialized environments (e.g. grub modification), that need a
restart after provisioning, set `reload` to `true`.

~~~
# node declarations
Vagrant.configure(2) do |config|
  setup_node(config, "example", "debian/jessie64", 2, 4096, true, "")
end
~~~

For multimachine setup, call `setup_node` multiple times.

~~~
# node declarations
Vagrant.configure(2) do |config|
  setup_node(config, "example1", "debian/jessie64", 2, 4096, false, "")
  setup_node(config, "example2", "debian/jessie64", 2, 4096, false, "")
end
~~~

### Puppet provision

Any puppet related files are in `puppet` directory. Node configuration should
be done in the `puppet/manifests/nodes.pp` manifest. Additional modules placed
into `puppet/modules`. Hiera facts could be placed under `puppet/hieradata`.

## License

Copyright (C) 2016 Alexander Böhm <alxndr.boehm@gmail.com> 

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU Affero General Public License as
published by the Free Software Foundation, either version 3 of the
License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU Affero General Public License for more details.

You should have received a copy of the GNU Affero General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.

