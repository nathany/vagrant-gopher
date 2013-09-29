# Vagrant Gopher

A Vagrantfile for running Go VMs.

### Requirements

[Vagrant][], [VirtualBox][] and a [Go workspace][workspace].

### Usage

Copy this [Vagrantfile][] to src/Vagrantfile:

```bash
src$ curl -O https://raw.github.com/gophertown/vagrant-gopher/master/Vagrantfile
```

Then run `vagrant up` from any subfolder. 

This will mount your src as a shared folder inside the Linux/BSD VMs, while creating new bin/pkg folders to avoid collisions (particularly bin).

Use `vagrant ssh linux` or `vagrant ssh bsd` to login and look around, or run a single command like `vagrant ssh linux -c 'go help'`. 

You will likely need to change directories. CDPATH is configured for the major source code hosts to save a few keystrokes, eg. `-c 'cd gophertown/looper; go test ./...'`.

Use `vagrant halt` to shutdown or `vagrant destroy` to free up disk space.

See the [Vagrant Command Line documentation][cli] for details.

### Notes

* Currently this shares the src/ folder into `$HOME/src` on the virtual machine (`$HOME/go/src` [did not work](https://github.com/mitchellh/vagrant/issues/2257)).
* It would be nice to have a wrapper/plugin around [the ssh command](https://github.com/mitchellh/vagrant/tree/master/plugins/commands/ssh) that runs commands based on the current folder.
* Shared folders aren't compatible with tools that watch files inside the VM using fsnotify.
* 64-bit boxes are used to support the Go race detector.

[Vagrant]: http://www.vagrantup.com/
[VirtualBox]: https://www.virtualbox.org/
[cli]: http://docs.vagrantup.com/v2/cli/index.html
[workspace]: http://golang.org/doc/code.html
[Vagrantfile]: https://raw.github.com/gophertown/vagrant-gopher/master/Vagrantfile
