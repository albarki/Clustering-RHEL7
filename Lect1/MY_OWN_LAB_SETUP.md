###Setup_MY_OWN_LAB Linux Mint17 (4 cores - 8G RAM)
- install kvm
    ```sh
    $sudo apt-get install qemu-kvm libvirt-bin bridge-utils virt-manager
    $sudo adduser root libvirtd
    $sudo apt-get install qemu-system
    $sudo apt-get install qemu-utils
    $sudo  apt-get install python-spice-client-gtk
    $sudo apt-get install virt-viewer
    $sudo -i
    $virt-manager
    ```
- create Virtual bridge
    ```
    $ service network-manager stop
    $ nano /etc/network/interfaces
      // add this code
      auto lo
      iface lo inet loopback
      auto eth0
      iface eth0 inet manual

      auto br0
        iface br0 inet dhcp
        bridge_ports eth0
        bridge_stp off
        bridge_fd 0
        bridge_maxwait 0
    $service network-manger restart
    $ service networking restart
    $ brctl show  
    ```
- create RHEL7 local repo

- create one vm
    //using GUI install RHEL7
- clone other 3 vm

END OF LAB
