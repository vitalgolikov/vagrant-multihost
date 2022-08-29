# -*- mode: ruby -*-
# vi: set ft=ruby :

hosts = {
  webhost: {cnt: 1, "ip": "192.168.98.200","hostname": "web.local", ports:[[80, 8000],[22, 122]], file: "web.sh", thick: "true"},
  dbhost:  {cnt: 1, "ip": "192.168.98.210", "hostname": "db.local", ports:[[80, 8100],[22, 222]], file: "db.sh", thick: "false"},
  clnt: {cnt: 0, "ip": "192.168.98.100", "hostname": "clnt.local", ports:[[80, 9100],[22, 322]], file: "clnt.sh", thick: "false"}
}

Vagrant.configure("2") do |config|
   config.vm.box = "bento/ubuntu-20.04"
   hosts.each do |name, cfg|
     rng = (cfg[:cnt]==1) ? [1] : (1..cfg[:cnt])
     rng.each do |cnt|
       iparray = cfg[:ip].split('.').map(&:to_i)
       iparray[3] = iparray[3].to_i+cnt
       fqdnarray = cfg[:hostname].split(".")
       fqdnarray[0] = fqdnarray[0]+cnt.to_s
       config.vm.define "#{name}-#{cnt}" do |machine|
         machine.vm.hostname = fqdnarray.join(".")
         machine.vm.network :private_network, ip: iparray.join(".")
         machine.vm.provider "virtualbox" do |v|
           v.name = "#{name}-#{cnt}"
           v.gui = false
           v.memory =  cfg[:thick] ? "1024" : "256"
	       v.cpus =    cfg[:thick] ?  1 :     1
         end
         cfg[:ports].each do | portmap |
           machine.vm.network "forwarded_port", guest: portmap[0] , host: portmap[1]+cnt
         end

       machine.vm.provision "shell", path: cfg[:file]
       end
     end
   end
end