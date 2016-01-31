### 1_Building_Linux_High_availability_Clusters_using_RHEL7_lab_setup
- lab setup
  - installed RHEL VM above it there are `4-5` VMs
    - the main REHL VM called `Cluster Workstation`
      - 4 cores
      - 8 G RAM
      - Network: Host-Only or Nat
      - virtual disk : 200G
      - Enable VT: which enable nested visualization (enable vm in vm)

```
//Technical_steps
- begin with install RHEL in `Workstation`
  - disable kdump
  - Hostname: Workstation.mostafa.com
  - / size : All 200G
```

- why install RHEL vm first as base VM then install above it 4-5 VMs using kvm?
  - kvm allow capabilites not found in VMware like
    - fin-sing of VMs
      - fin-sing: remove power cable and then connect it.
    - we will manage VM at low level from power point of view


- what is Cluster:
  - group of machines `doing the same task`
    - high availability
      - Example: protocols in cisco `VRRP`, `HSRP`, `GLPP`
        - make two `GW` devices check each others so if primary down the second one take control with the same ip (Virtual IP) of the primary.
        - running two or more apache machines with the same Virtual IP.
          - what if the apache itself is down not the whole VM.
          - what if the storage backend itself is not accessible.
          - so the problem is not just sharing virtual ip but its `resource group` (VIP - Apache - NFS share) sharing.

```
//Technical_steps
- Now Main VM was finished installing RHEL7
- install vm tools
- make local repo in /mnt
- open Terminal
   $ yum install httpd
   $ cd /var/www/html
   $ systemctl enable httpd.service
   $ systemctl restart httpd.service
   $ systemctl status httpd.service
   $ nmcli connection show
   $ yum search kvm
   $ yum search libvirt
   $ yum install virt-manager libvirt
   $ yum search kvm
   $ yum install qemu-kvm-tools
   $ yum search bridge-utils
   $ yum install bridge-utils
   $ nmcli connection add type bridge con-name br0 ifname br0 autoconnect yes
   $ nmcli connection show
   $ cd /etc/sysconfig/network-scripts
   $ vim ifcfg-eno16777
      BOOTPROTO = "dhcp"
      BRIDGE= "br0"
   $ nmtui
      change IPV4 Configuration to Automatic
   $ nmcli connection show
   $ nmcli connection down en0
   $ nmcli connection down br0
   $ nmcli connection up br0
   $ nmcli connection show
   $ nmcli connection up eno
   $ nmcli connection show br0
   $ route -n //to get default gw
   $ nmcli connection modify br0 ipv4.addresses 192.168.188.100/32 ipv4.gateway 192.168.188.2 ipv4.method manual ipv4.dns 192.168.188.2 connection.autoconnect
   $ nmcli connection down br0
   $ nmcli connection up br0
   $ nmcli connection modify eno1677 ipv4.method shared
   $

```
- Types of Clusters:
  - high performance Clusters (HPC) called grid computing
	    - if we have 6 machine with 128G and 6 cores then we got like a big machine with 1.5T and 36 cores
	    - idea : get huge computing machine from small computing machines
	    - Example : pwolf clusters
  - load balancing
	    - like HA proxy
	    - like varnish
	    - like nginx
  - high availability cluster
	    - Our Work here
	    - we have 3 servers
	    - one main server is running if something wrong happened with the running server then another sever take control and troubleshoot the error of the main server.
	    - downtime = the time required to change control to another server (seconds)
    - types :
      - active active : all servers is running
      - active passive : only one server is running at same time
- you dont need to cluster services where client can configure himself to serve from alot of place like dns and ntp, client can configure alot of dns and alot of ntp at the same time, so if the first dns down then the client act with the second configured dns and so on.

```Technical_steps
// now u can install vm as u want
- open from GUI system tools > Virtual Machine Manager
// we will install only one vm then clone it to 4 other vm
- create vm with
  - 1 cpu
  - 1G ram
  - 10 G disk
  - minimal install
  - Network : bridge
```
- create brdige in the cluster-workstartion
```
$ nmcli connection show
$ nmcli connection delete br0
$ ifconfig
$ nmcli connection delete eno
$ nmcli connection add con-name br0 ifname br0 autoconnect yes type bridge
$ nmcli connection add type bridge-slave ifname eno con-name eno-bridge autoconnect yes master br0
$ nmcli connection show
$ ifconfig
$ nmcli connection modify br0 ipv4.method auto
$ systemctl restart NetworkManager.service
$ ifconfig
```
- you can do the above steps with nmtui
  - add > bridge > profile name : br0 , Device : br0

- now clone the machine to 4 new vms
  - node a
  - node b
  - node c
  - noce d

- setup repos in Workstation and all nodes
  - create high availability repo

- install cluster
```
//location al nodes
$firewall-cmd --add-service=httpd --permanant
$ yum search pcs
$ yum install -y bash-completion pcs fence-agent-all
```


Collected by "Ibrahim Albarki" send feedback to ibrahiemalbarki@gmail.com
01/27/2016, 3:15PM
