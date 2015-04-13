#Cassandra deployment on raspberry pi cluster#
This ansible script installs cassandra on a cluster of raspberry pi's. The OS image from hypriot http://blog.hypriot.com/ has been installed on the authors cluster which does not have a copy of the jdk or python preinstalled. Hence the script also install's the jdk from oracle.

For controlling multiple nodes it is recommended that passwordless ssh login is used. Instructions can be found here http://www.thegeekstuff.com/2008/11/3-steps-to-perform-ssh-login-without-password-using-ssh-keygen-ssh-copy-id/

If the machine running ansible is macosx then https://github.com/brockgr/csshx is recommended. This allows bash commands to be entered onto multiple hosts. To invoke csshx enter

```bash
csshx root@172.16.2.[51-58]
```

Enter your own address range above.

Select the csshx master window and enter

```bash
apt-get install python
```

To install jdk and  type the following from your ansible workstation command line
```bash
ansible-playbook -i production --tags=install_jdk,install_cassandra setup.yml
```

To remove jdk and cassandra type the following

```bash
ansible-playbook -i production --tags=remove_jdk,remove_cassandra setup.yml
```

To install only cassandra then type the following

```bash
ansible-playbook -i production --tags=install_cassandra setup.yml
```

To remove only cassandra then type the following

```bash
ansible-playbook -i production --tags=remove_cassandra setup.yml
```

User configurable variables are stored in /group_vars/all

The cassandra nodes are listed in /production in the appropriate groups. Any changes to reflect your node configuration need to be made there.

The apache cassandra logs are stored in /var/log/apache-cassandra.log

After the script has finished the seed nodes must be started first. type the following on each seed node
```bash
service cassandra start
```

type the folowing to see if the node has started and joined the cluster
```bash
/opt/cassandra/bin/nodetool status
```

Then you will see 

```bash
Datacenter: datacenter1
=======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address      Load       Tokens  Owns (effective)  Host ID                               Rack
UN  172.16.2.51  194.76 KB  256     25.1%             babe1a04-cabd-4a5d-b80e-e208dafcb606  rack1
```
Start the second seed node followed by 

```bash
/opt/cassandra/bin/nodetool status
```

to see if the node has joined the cluster.

Repeat the steps until all the nodes are up and normal.

If the cluster needs restarting then ensure the seeds nodes are always started first, are up and normal, followed by the non seed nodes. If a node does not start then manually start it again.

www.datastax.com and planetcassandra.org are great sources of information. Also cassandra.apache.org has links to the mailing lists.

For information about ansible visit www.ansible.com