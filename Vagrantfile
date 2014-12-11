VAGRANTFILE_API_VERSION = "2"

Vagrant.require_version ">= 1.6.3"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.define "boot2docker"

  config.vm.box = "yungsang/boot2docker"

  config.vm.synced_folder ".", "/vagrant"
  # Or you can use NFS as before
  config.vm.network "private_network", ip: "192.168.59.103"
  # config.vm.synced_folder ".", "/vagrant", type: "nfs"

  # Uncomment below to use more than one instance at once
   config.vm.network :forwarded_port, guest: 2375, host: 2375, auto_correct: true

  # Fix busybox/udhcpc issue
  config.vm.provision :shell do |s|
    s.inline = <<-EOT
      # clean all ps -a in docker
      sudo docker rm $(sudo docker ps –a -q)
      if ! grep -qs ^nameserver /etc/resolv.conf; then
        sudo /sbin/udhcpc
      fi
      cat /etc/resolv.conf
    EOT
  end

  # Adjust datetime after suspend and resume
  config.vm.provision :shell do |s|
    s.inline = <<-EOT
      sudo /usr/local/bin/ntpclient -s -h pool.ntp.org
      date
    EOT
  end

  config.vm.provision :docker do |d|
  #  d.pull_images "yungsang/busybox"
  #  d.run "simple-echo",
  #    image: "yungsang/busybox",
  #    args: "-p 8080:8080",
  #    cmd: "nc -p 8080 -l -l -e echo hello world!"
    d.pull_images "jaesharp/orli-ubuntu-1204-chef-client-d"
    d.run "chef-client",
       image: "jaesharp/orli-ubuntu-1204-chef-client-d",
       daemonize: true
    #d.image = "ubuntu"
    #d.build_dir = "."
  end

  config.vm.network :forwarded_port, guest: 8080, host: 8080
end
