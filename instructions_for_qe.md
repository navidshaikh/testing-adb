Test cases for ADB and Eclipse integration using vagrant-adbinfo
================================================================

This document enlist the test cases for testing integration of ADB and Eclipse Docker tooling plugin.

Setup environment for testing
-----------------------------

- Setup ADB - <https://github.com/projectatomic/adb-atomic-developer-bundle/blob/master/docs/installing.rst>

- Setup vagrant-adbinfo plugin and Eclipse on the given operating system - https://github.com/navidshaikh/testing-adb

Test cases
----------

- vagrant plugin installation: The vagrant plugin installation should not issue any warning or conflict

- vagrant plugin update: If the vagrant plugin is already installed, the update of vagrant plugin should not issue any warning.
  To update a plugin run `vagrant plugin update vagrant-adbinfo`

- vagrant plugin command line: 
  - Create a workspace directory and export following contents in a Vagrantfile

```
# vi: set ft=ruby :
#Vagrant file for libvirt/kvm and virtualbox provider

Vagrant.configure(2) do |config|
  config.vm.box = "atomicapp/dev"

  config.vm.network "forwarded_port", guest: 2376, host: 2379, auto_correct: true

  config.vm.provider "virtualbox" do |vbox, override|
    vbox.memory = 1024
    vbox.cpus = 2

    # Enable use of more than one virtual CPU in a virtual machine.
    vbox.customize ["modifyvm", :id, "--ioapic", "on"]
  end

end
  
```
 
  - Run `vagrant up --provider virtualbox` (in the same directory where Vagrantfile exist)

  - Once box is up and running, run `vagrant adbinfo`
  - The execution of the plugin does following - 
     - Copies the client side certs from inside the ADB to host machine
     - Finds the port where Docker daemon port inside ADB is forwarded on the host machine
     - Finds the machine id

- Verify the `.docker` directory in the secrets path: 
  - Plugin creates a new directory `.docker` in `.vagrant/machines/default/virtualbox/` directory relative to your Vagrantfile location
  - Plugin copies the certs (`ca.pem, cert.pem, key.pem`) in `.vagrant/machines/default/virtualbox/.docker/` directory

- Verify the Docker daemon forwarded port:
  In Vagrantfile, we mentioned guest port `2376` to be forwarded to host port `2379`, verify it the plugin output, the DOCKER_HOST variable value should be `tcp://127.0.0.1:2379`

- Verify the machine id:
  Run `vagrant global-status` and verify the machine `id` mentioned in the output is same as the one displayed in the plugin output by variable `DOCKER_MACHINE_NAME`

- Verify the integration with Eclipse:
  Now lets connect to Eclipse Docker plugin using the connection details displayed by the plugin



- Run plugin again from the command line (`vagrant adbinfo`) and 

- Run `vagrant adbinfo` in your current working directory

```
$ vagrant adbinfo
Set the following environment variables to enable access to the
docker daemon running inside of the vagrant virtual machine:

export DOCKER_HOST=tcp://127.0.0.1:2379
export DOCKER_CERT_PATH=c:\Users\nshaikh\vagrant-adbinfo\.vagrant\machines\default\virtualbox\.docker\
export DOCKER_TLS_VERIFY=1
export DOCKER_MACHINE_NAME=8606567
```

- Connection details for the `docker` daemon inside ADB are displayed after executing the vagrant adbinfo plugin

- To test the client connection to `docker` daemon inside ADB: In the Vagrantfile, we have mapped host port `2379` to `docker` daemon port (2376) inside ADB, which means that you can access the daemon at (host machine) 127.0.0.1:2379.

- To test out the connection with Eclipse kindly check <https://www.eclipse.org/community/eclipse_newsletter/2015/june/article3.php> and this Video by Xavier Coulon <https://www.youtube.com/watch?v=RUgEgtLux8Q>. More Eclipse Docker Tooling documentations are at <<https://wiki.eclipse.org/Linux_Tools_Project/Docker_Tooling/User_Guide>>
