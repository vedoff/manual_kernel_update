# Describe VMs
#
home = ENV['HOME']
MACHINES = {
  # VM name "kernel update"
  :"otus-test" => {
              # VM box
              :box_name => "vedoff/centos-7-5",
              # VM CPU count
              :cpus => 2,
              # VM RAM size (Mb)
              :memory => 1024,
              # networks
              :net => [],
              # forwarded ports
              :forwarded_port => [],
:"disks" => {
       :sata1 => {
           :dfile => home + '/VirtualBoxHDD/VHDD/vhdd-01.vdi',
           :size => 1024,
           :port => 1
       },
       :sata2 => {
           :dfile => home + '/VirtualBoxHDD/VHDD/vhdd-02.vdi',
           :size => 1024, # Megabytes
           :port => 2
       },
       :sata3 => {
           :dfile => home + '/VirtualBoxHDD/VHDD/vhdd-03.vdi',
           :size => 1024,
           :port => 3
       },
       :sata4 => {
           :dfile => home + '/VirtualBoxHDD/VHDD/vhdd-04.vdi',
           :size => 1024,
           :port => 4 
       }
    }
  },
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    # Disable shared folders
   # config.vm.synced_folder ".", "/vagrant", disabled: true
  # Add rsync
    config.vm.synced_folder '.', "/vagrant-rsync",
    type: "rsync",
    rsync_auto: "true",
    rsync_exclude: [".git/",".vagrant/"],
   # id: "vagrant"
    rsync__args: ["--verbose", "--rsync-path='sudo rsync'", "--archive", "--delete", "-z"]
    config.vm.provision "shell", path: "vagrant-rsync/createmdadm.sh"
#    config.vm.provision "shell", path: "vagrant-rsync/testmdadm.sh"
    # Apply VM config
    config.vm.define boxname do |box|
      # Set VM base box and hostname
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxname.to_s
      # Additional network config if present
      if boxconfig.key?(:net)
        boxconfig[:net].each do |ipconf|
          box.vm.network "private_network", ipconf
        end
      end
      # Port-forward config if present
      if boxconfig.key?(:forwarded_port)
        boxconfig[:forwarded_port].each do |port|
          box.vm.network "forwarded_port", port
        end
      end
      # VM resources config
      box.vm.provider "virtualbox" do |v|
        # Set VM RAM size and CPU count
        v.memory = boxconfig[:memory]
        v.cpus = boxconfig[:cpus]
	v.customize ["storagectl", :id, "--name", "SATA", "--add", "sata"]

	#Add HDD
	boxconfig[:disks].each do |dname, dconf|
	v.customize ['storageattach', :id,  '--storagectl', 'SATA', '--port', dconf[:port], '--device', 0, '--type', 'hdd', '--medium', dconf[:dfile]]
       end
      end
    end
  end
end
