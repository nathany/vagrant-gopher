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

GO_VERSION = "1.11"

def provision_linux
  archive = "go#{GO_VERSION}.linux-amd64.tar.gz"

  <<-SCRIPT
  set -x
  set -e
  #{install_go_binary(archive)}
  #{update_profile}
  SCRIPT
end

def provision_bsd
  archive = "go#{GO_VERSION}.freebsd-amd64.tar.gz"

  <<-SCRIPT
  set -x
  set -e
  pkg install -y wget

  #{install_go_binary(archive)}
  #{update_profile}
  SCRIPT
end

def provision_solaris
  <<-SCRIPT
  set -x
  # set -e
  pfexec pkg install build-essential

  #{install_go_source}
  #{update_profile}
  SCRIPT
end

# install Go from a binary
def install_go_binary(archive)
  <<-SCRIPT
  #{wget(archive)}
  tar -C /usr/local -xzf #{archive}
  SCRIPT
end

# install Go from a source
def install_go_source
  source = "go#{GO_VERSION}.src.tar.gz"
  bootstrap = "go1.4-bootstrap-20161024.tar.gz"

  <<-SCRIPT
  mkdir -p /usr/local/go/bootstrap
  #{wget(bootstrap)}
  tar -C /usr/local/go/bootstrap -xzf #{bootstrap}
  bash -c "cd /usr/local/go/bootstrap/go/src && ./make.bash"

  #{wget(source)}
  tar -C /usr/local -xzf #{source}
  bash -c "cd /usr/local/go/src && GOROOT_BOOTSTRAP=/usr/local/go/bootstrap/go ./make.bash"
  SCRIPT
end

# update .profile
def update_profile
  profile = <<-PROFILE
  export GOPATH=$HOME
  export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin
  export CDPATH=.:$GOPATH/src/github.com:$GOPATH/src/golang.org
  PROFILE

  <<-SCRIPT
  echo '#{profile}' >> ~vagrant/.profile
  SCRIPT
end

def wget(file)
  url = "https://dl.google.com/go"

  <<-SCRIPT
  if ! [ -f #{file} ]; then
    wget -nv #{url}/#{file}
  fi
  SCRIPT
end

# location of the Vagrantfile
def src_path
  File.dirname(__FILE__)
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.define "linux" do |linux|
    linux.vm.box = "ubuntu/bionic64"
    linux.vm.synced_folder src_path, "/home/vagrant/src"
    linux.vm.provision :shell, :inline => provision_linux
  end

  # Using NFS for synced/shared folders:
  # * which is unsupported on Windows hosts (though maybe with freeNFS)
  # * and requires a private host-only network
  # * and will prompt for the administrator password of the host
  # * but is said to be faster than VirtualBox shared folders
  config.vm.define "bsd" do |bsd|
    bsd.vm.box = "bento/freebsd-10.4"
    bsd.vm.synced_folder ".", "/vagrant", disabled: true
    bsd.vm.synced_folder src_path, "/home/vagrant/src", type: "nfs"
    bsd.vm.network "private_network", type: "dhcp"
    bsd.vm.provision :shell, :inline => provision_bsd
    bsd.ssh.shell = "sh" # for provisioning
  end

  config.vm.define "solaris" do |solaris|
    solaris.vm.box = "openindiana/hipster"
    solaris.vm.synced_folder src_path, "/export/home/vagrant/src"
    solaris.vm.provision :shell, :inline => provision_solaris
  end

end
