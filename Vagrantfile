# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.require_version ">= 2.3.7"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  MACHINE = ["automation","gitlab","gitrun","harbor"]
  CPUS =  [1,2,6,2]
  MEMORY = [1024,6144,8192,2048]
  IP = [100,101,102,103]
  # MAC = [AA:BB:CC:DD:]
  N = 3

  config.vm.box = "arm64-bookwormext4-12-1-0"
  # config.vm.guest = "linux"
  config.ssh.username = "gnammyx"
  # config.ssh.password = ""
  config.ssh.insert_key = false
  config.ssh.forward_agent = true
  config.ssh.keys_only = false
  config.ssh.private_key_path = "/Users/gnammyx/.ssh/id_rsa"
  # config.vm.synced_folder ".", "/vagrant", disabled: true

  (0..N).each do |i|
  config.vm.define MACHINE[i] do |server|

    server.vm.hostname = MACHINE[i]
    server.ssh.host = "192.168.10.#{IP[i]}"

    # server.vm.network "private_network", type: "dhcp"
    # server.vm.network "public_network", auto_config: true, type: "dhcp", mac: "AA:BB:CC:DD:EE:0#{i}", use_dhcp_assigned_default_route: true
    #, hostname: true, ip: "192.168.10.#{IP[i]}", gateway: "192.168.10.254"
                                        # use_dhcp_assigned_default_route: true

    # server.vm.provision "shell",
    #   run: "always",
    #   privileged: true,
    #   reboot: true,
    #   inline: "ifconfig enp0s6 192.168.10.#{IP[i]} netmask 255.255.255.0 up; route add default gw 192.168.10.254"

    server.vm.synced_folder ".", "/vagrant", disabled: true
    # server.vm.synced_folder "/Users/gnammyx/Parallels/volumes/#{MACHINE[i]}", "/srv", type: "nfs", nfs_udp: false, map_uid: 0, map_gid: 0, host_ip: "192.168.10.10", machine_ip: "192.168.10.#{IP[i]}"

    server.vm.provider "parallels" do |prl|
      prl.linked_clone = false
      prl.name = MACHINE[i]
      prl.check_guest_tools = true
      prl.update_guest_tools = true
      prl.cpus = CPUS[i]
      prl.memory = MEMORY[i]
      prl.customize "post-import", ["set", :id, "--device-del", "sound0"]
      prl.customize "post-import", ["set", :id, "--smart-mouse-optimize", "off"]
      prl.customize "post-import", ["set", :id, "--keyboard-optimize", "off"]
      prl.customize "post-import", ["set", :id, "--sync-host-printers", "off"]
      prl.customize "post-import", ["set", :id, "--autostart", "start-host"]
      prl.customize "pre-boot", ["set",
                                  :id,
                                  "--device-add" , "hdd",
                                  "--image", "/Users/gnammyx/Parallels/volumes/#{MACHINE[i]}.hdd"]
      prl.customize "pre-boot", ["set", :id,
                                  "--device-add", "net",
                                  "--type", "bridged",
                                  "--iface", "default",
                                  "--mac", "AA:BB:CC:DD:EE:0#{i}",
                                  "--ipadd", "192.168.10.#{IP[i]}",
                                  "--dhcp", "no",
                                  "--dhcp6", "no",
                                  "--gw", "192.168.10.254",
                                  "--nameserver", "192.168.1.1",
                                  "--configure", "yes"]
    end
    server.vm.provision :host_shell do |host_shell|
      host_shell.inline = "prlctl set #{MACHINE[i]} --device-set net0 --disconnect"
    end

    server.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "/Users/gnammyx/git/ansible/3_apps_services.yml"
      ansible.inventory_path = "/Users/gnammyx/git/ansible/inventory"
      ansible.version = "latest"
      # ansible.force_remote_user = true
      # ansible.become = true
      # ansible.become_user = "packer"
      # ansible.verbose = "-vv"
    end
  end
  end
end