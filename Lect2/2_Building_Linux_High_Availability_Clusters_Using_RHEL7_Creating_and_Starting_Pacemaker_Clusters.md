###2_Building_Linux_High_Availability_Clusters_Using_RHEL7_Creating_and_Starting_Pacemaker_Clusters_medium


- how to start cluster ?
- states of cluster ?
- fensing
- types of fensing

* Important Notes:
    - max number of clustered nodes supported by redhat `16 node`
    - you must give static IPs for all nodes
    - clustering in RHEL6
      - depends on two services `luci`, `recci`
      - cluster communication depends on services like `rgmanager`, `cman`->"cluster manager".
    - clustering in RHEL7
      - `pcs` Main package that when installed, install all cluster packages with it as dependencies  :
        - `pacemaker` :
          - 15 year ago Clustering Linux Project
          - bacame very Important so redhat changed to it from RHEL7 and RHEL6.6
          - responsible for every cluster activity & make 4 things
            1. `CIB` : `Cluster Information Base` (xml file found only in one node`cordinator`) [who is the master node and Slaves] and pushes and updates in confg to other nodes
            2. `BE`: `Policy Engine` force policies between nodes based on `CIB`
            3. `CRM`: `Cluster Resource Management` talk with `LRM`local resource manager to manage resources from node to another ex. stop apache at node a and start it at node b
            4. `STONITH`: killall -9 if some node is frozen it force reboot it.
        - `corosync` :
          - handle cluster communication (communication between nodes and each others)
          - manage `quorum` (voting system) tells if the cluster is healthy or not.

* clustering packages
    - pcs : pacemaker (cluster service)
    - fence-agents-all


* run this in all nodes
    - $ systemctl start pcsd
    - $ systemctl enable pcsd
    - $

note : nodes talks with each others via `ssh` via user named `hacluster`, so ssh keys of this user must be seen by all nodes.
    //so we need to do this on all nodes
    - $ echo "PASSWD" | passwd --stdin hacluster
    - $ firewall-cmd --add-service=high-availability
    - $ firewall-cmd --add-service=high-availability --permanent
* setup the cluster :
    - $ pcs cluster setup --start --name MyCluster nodea nodeb nodec noded
    // if u want to see ring id file
    - less /var/lib/corosync/ring<tab>

* auto enable/auto join to run the cluster after downtime
    - $pcs cluster enable nodea
    - $pcs cluster enable --all //all nodes will join the cluster after restart
    - $pcs cluster status
        `check DC` designated coordinator
        - try to reboot DC
    - get extended status of cluster
      - $ pcs status

    - check `netstat -ntlup` and note port `2224`
    - now access `https:nodea:2224` from the workstation node.
