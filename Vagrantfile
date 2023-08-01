# -*- mode: ruby -*-
# vi: set ft=ruby :

# ENV['VAGRANT_EXPERIMENTAL'] = 'typed_triggers'

VAGRANTFILE_API_VERSION = "2"

Vagrant.require_version ">= 2.3.7"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  MACHINE = ["automation","gitlab","gitrun","harbor"]
  CPUS =  [1,2,4,2]
  MEMORY = [1024,6144,4096,2048]
  IP = [100,101,102,103]
  N = 3

  config.vm.box = "arm64-bookwormlvm-12-1-0"
  config.vm.guest = "linux"
  config.ssh.username = "packer"
  config.ssh.password = "1ma63b0rk3d"
  config.ssh.insert_key = false
  config.vm.synced_folder ".", "/vagrant", disabled: true

  (0..N).each do |i|
  config.vm.define MACHINE[i] do |server|

    server.vm.hostname = MACHINE[i]

    server.ssh.host = "192.168.10.#{IP[i]}"

    # server.vm.synced_folder ".", "/vagrant", disabled: true
    server.vm.provider "parallels" do |prl|
      # prl.name = "automation01"
      prl.linked_clone = false
      prl.name = MACHINE[i]
      prl.check_guest_tools = false
      prl.update_guest_tools = false
      prl.cpus = CPUS[i]
      prl.memory = MEMORY[i]
      prl.customize "pre-boot", ["set", :id, ]
      prl.customize "pre-boot", ["set", :id, "--device-del", "sound0"]
      # prl.customize "pre-boot", ["set", :id, "--device-del", "net0"]
      prl.customize "pre-boot", ["set", :id, "--smart-mouse-optimize", "off"]
      prl.customize "pre-boot", ["set", :id, "--keyboard-optimize", "off"]
      prl.customize "pre-boot", ["set", :id, "--sync-host-printers", "off"]
      prl.customize "pre-boot", ["set", :id, "--autostart", "start-host"]
      prl.customize "pre-boot", ["set",
                                  :id,
                                  "--device-add" , "net",
                                  "--type", "bridged",
                                  "--iface", "default",
                                  "--ipadd", "192.168.10.#{IP[i]}",
                                  "--dhcp", "no",
                                  "--gw", "192.168.10.254",
                                  "--nameserver", "192.168.1.1",
                                  "--configure", "yes" ]
    end
  end
  end
end

# "--mac", "82:b4:94:e7:d6:8b",