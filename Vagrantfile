# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.require_version ">= 2.4.1"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  vms = [
    { :name => "automation", :ip => "100" },
    { :name => "gitlab", :ip => "101", :cpus => 2 },
    { :name => "gitrun", :ip => "102" },
    { :name => "harbor", :ip => "103", :rosetta => "on" },
    { :name => "repo", :ip => "104" },
    { :name => "testnode1", :ip => "105", :ram => 512 },
    { :name => "testnode2", :ip => "106", :ram => 512 },
    { :name => "testnode3", :ip => "107", :ram => 512 },
    { :name => "testha1", :ip => "108" },
    { :name => "testha2", :ip => "109" }
  ]

  cpus_default = 1
  ram_default = 1024
  rosetta_default = "off"

  config.vm.box = "bookworm-arm64-ext4-12-5-0"
  config.ssh.username = "gnammyx"
  config.ssh.insert_key = false
  config.ssh.forward_agent = true
  config.ssh.keys_only = false
  config.ssh.private_key_path = "/Users/gnammyx/.ssh/id_rsa"

  vms.each do |vm|
  config.vm.define vm[:name] do |server|
    server.vm.hostname = vm[:name]
    # server.ssh.host = "192.168.10.#{IP[i]}"
    server.ssh.host = vm[:name]

    server.vm.network "public_network", mac: "AA:BB:CC:DD:EE:#{vm[:ip].chars.last(2).join}", use_dhcp_assigned_default_route: true, type: "dhcp"
    server.vm.synced_folder ".", "/vagrant", disabled: true
    # server.vm.synced_folder "/Users/gnammyx/Parallels/volumes/#{vm[:name]}", "/srv", type: "nfs", nfs_udp: false, map_uid: 0, map_gid: 0, host_ip: "192.168.10.10", machine_ip: "192.168.10.#{IP[i]}"

    server.vm.provider "parallels" do |prl|
      prl.linked_clone = true
      prl.name = vm[:name]
      prl.check_guest_tools = false
      prl.update_guest_tools = false
      prl.cpus = vm[:cpus] || cpus_default
      prl.memory = vm[:ram] || ram_default
      prl.customize "post-import", ["set", :id, "--smart-mouse-optimize", "off"]
      prl.customize "post-import", ["set", :id, "--keyboard-optimize", "off"]
      prl.customize "post-import", ["set", :id, "--sync-host-printers", "off"]
      prl.customize "post-import", ["set", :id, "--autostart", "start-host"]
      prl.customize "post-import", ["set", :id, "--autostop", "suspend"]
      prl.customize "post-import", ["set", :id, "--autostart-delay", "30"]
      prl.customize "post-import", ["set", :id, "--rosetta-linux", "#{vm[:rosetta] || rosetta_default}"]
      prl.customize "post-import", ["set",
                                  :id,
                                  "--device-add" , "hdd",
                                  "--image", "/Users/gnammyx/Parallels/volumes/#{vm[:name]}.hdd"]
      prl.customize "pre-boot", ["set", :id, "--device-del", "sound0"]

      # prl.customize "post-comm", ["stop", :id]
    end

    # server.vm.provision "shell",
    #   run: "once",
    #   privileged: true,
    #   reboot: true,
    #   inline: "shutdown -h now"

    # server.vm.provision "host_shell",
    # run: "once",
    # inline: "prlctl stop #{vm[:name]} && prlctl set #{vm[:name]} --device-del net0 && prlctl start #{vm[:name]}"

    server.trigger.after :up do |trigger|
      trigger.ignore = :provision
      trigger.info = "Shutting down"
      trigger.run = {inline: "prlctl stop #{vm[:name]}"}
    end

    server.trigger.after :up do |trigger|
      trigger.ignore = :provision
      trigger.info = "Remove net0"
      trigger.run = {inline: "prlctl set #{vm[:name]} --device-del net0"}
    end

    server.trigger.after :up do |trigger|
      trigger.ignore = :provision
      trigger.info = "Restart vm"
      trigger.run = {inline: "prlctl start #{vm[:name]}"}
    end

  end
  # config.vm.provision "shell",
  #   run: "once",
  #   # privileged: true,
  #   reboot: true,
  #   inline: "shutdown -h now"

  end
end






    # server.vm.provision "ansible" do |ansible|
    #   ansible.compatibility_mode = "2.0"
    #   ansible.playbook = "/Users/gnammyx/git/ansible/2_network_config.yml"
    #   ansible.inventory_path = "/Users/gnammyx/git/ansible/inventory"
    #   ansible.version = "latest"
    #   # ansible.force_remote_user = true
    #   # ansible.become = true
    #   # ansible.become_user = "packer"
    #   # ansible.verbose = "-vv"
    # end

    # server.vm.provision "ansible" do |ansible|
    #   ansible.compatibility_mode = "2.0"
    #   ansible.playbook = "/Users/gnammyx/git/ansible/3_apps_services.yml"
    #   ansible.inventory_path = "/Users/gnammyx/git/ansible/inventory"
    #   ansible.version = "latest"
    #   # ansible.force_remote_user = true
    #   # ansible.become = true
    #   # ansible.become_user = "packer"
    #   # ansible.verbose = "-vv"
    # end

    # server.vm.provision "ansible" do |ansible|
    #   ansible.compatibility_mode = "2.0"
    #   ansible.playbook = "/Users/gnammyx/git/ansible/4_containers.yml"
    #   ansible.inventory_path = "/Users/gnammyx/git/ansible/inventory"
    #   ansible.version = "latest"
    #   # ansible.force_remote_user = true
    #   # ansible.become = true
    #   # ansible.become_user = "packer"
    #   # ansible.verbose = "-vv"
    # end
