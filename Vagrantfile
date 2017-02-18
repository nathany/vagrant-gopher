# -*- mode: ruby -*-
# vi: set ft=ruby :

# # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # #
#
# Place this Vagrantfile in your src folder and run:
#
#     vagrant up
#
# # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # #

# Vagrantfile API/syntax version.
VAGRANTFILE_API_VERSION = "2"

Vagrant.require_version ">= 1.5.0"

# See http://dl.golang.org/dl/
GO_ARCHIVES = {
  "linux" => "go1.8.linux-amd64.tar.gz",
  "bsd" => "go1.8.freebsd-amd64.tar.gz"
}

INSTALL = {
  "linux" => "apt-get update -qq; apt-get install -qq -y git",
  "bsd" => "pkg install -y wget git"
}

# location of the Vagrantfile
def src_path
  File.dirname(__FILE__)
end

# shell script to bootstrap Go
def bootstrap(box)
  install = INSTALL[box]
  archive = GO_ARCHIVES[box]

  profile = <<-PROFILE
    export GOPATH=$HOME
    export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin
    export CDPATH=.:$GOPATH/src/github.com:$GOPATH/src/golang.org
  PROFILE

  <<-SCRIPT
  #{install}

  if ! [ -f /home/vagrant/#{archive} ]; then
    response=$(wget -nv https://storage.googleapis.com/golang/#{archive})
  fi
  tar -C /usr/local -xzf #{archive}

  echo '#{profile}' >> /home/vagrant/.profile

  echo "\nRun: vagrant ssh #{box} -c 'cd project/path; go test ./...'"
  SCRIPT
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.define "linux" do |linux|
    linux.vm.box = "ubuntu/trusty64"
    linux.vm.synced_folder src_path, "/home/vagrant/src"
    linux.vm.provision :shell, :inline => bootstrap("linux")
  end

  # Using NFS for synced/shared folders:
  # * which is unsupported on Windows hosts (though maybe with freeNFS)
  # * and requires a private host-only network
  # * and will prompt for the administrator password of the host
  # * but is said to be faster than VirtualBox shared folders
  config.vm.define "bsd" do |bsd|
    bsd.vm.box = "bento/freebsd-10.3"
    bsd.vm.synced_folder ".", "/vagrant", :disabled => true
    bsd.vm.synced_folder src_path, "/home/vagrant/src", :nfs => true
    bsd.vm.network :private_network, :ip => '10.1.10.5'
    bsd.vm.provision :shell, :inline => bootstrap("bsd")
    bsd.ssh.shell = "sh" # for provisioning
  end

end
