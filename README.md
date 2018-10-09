# Cuckoooooo
## make sure you have Ubuntu 18 or newer releasess
## make sure your VM has lager than 15GB hard drive space

How to install and use cuckoo with linux virtual machine

### Cuckoo Installation Problems and Solutions
* [Complete documents](https://cuckoo.sh/docs/installation/host/requirements.html)
* My Enviroment 
    * Host: Ubuntu 18.04.1 LTS
    * Guest: Virtualbox (18.04)

#### Do this before you try to edit configuration files
* Setup host-only network on VM 
    * first create host-only interface on host
    ````
        vboxmanage hostonlyif create
        vboxmanage hostonlyif ipconfig vboxnet0 --ip 192.168.56.1
    ````
    * Now you should see vboxnet0 with ifconfig
    ````
    vboxnet0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.56.1  netmask 255.255.255.0  broadcast 192.168.56.255
        inet6 fe80::800:27ff:fe00:0  prefixlen 64  scopeid 0x20<link>
        ether 0a:00:27:00:00:00  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 3201  bytes 758492 (758.4 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    ````
    * Set the NIC on guest virtual machine
    ````
    vboxmanage modifyvm <yourVmName> --hostonlyadapter1 vboxnet0
    vboxmanage modifyvm <yourVmName> --nic1 hostonly
    ````
    * Now you will have to set up networking on the guest to use the host as a gateway
    you can do this by following the Routing session in the documentation
    * Try ping the VM from the host and ping the host in the VM
    
* Take a snapshot on VM
    * Somehow get $CWD/agent/agent.py on your guest VM
    * run agent.py as root user
    ````
    sudo python agent.py
    ````
    * you can check if agent.py is running by typing 
    ````
    netstat -npl
    ````
    and check if *:8000 is at LISTEN state 
    * now take a snapshot 
    

#### Edit Configuration Files
* You have to at least edit cuckoo.conf and virtualbox.conf
* cuckoo.conf:
    ````
    machinery = virtualbox
    ...
    [resultserver]
    # The Result Server is used to receive in real time the behavioral logs
    # produced by the analyzer.
    # Specify the IP address of the host. The analysis machines should be able
    # to contact the host through such address, so make sure it's valid.
    # NOTE: if you set resultserver IP to 0.0.0.0 you have to set the option
    # `resultserver_ip` for all your virtual machines in machinery configuration.
    ip = <host ip of the host-only network>
    ````
* virtualbox.conf
    ````
    [virtualbox]
    # Specify which VirtualBox mode you want to run your machines on.
    # Can be "gui" or "headless". Please refer to VirtualBox's official
    # documentation to understand the differences.
    mode = headless
    
    # Path to the local installation of the VBoxManage utility.
    path = /usr/bin/VBoxManage 
    # If you are running Cuckoo on Mac OS X you have to change the path as follows:
    # path = /Applications/VirtualBox.app/Contents/MacOS/VBoxManage
    
    # Default network interface.
    interface = <interface name> usually vboxnet0  
    
    # Specify a comma-separated list of available machines to be used. For each
    # specified ID you have to define a dedicated section containing the details
    # on the respective machine. (E.g. cuckoo1,cuckoo2,cuckoo3)
    machines = Ubuntu 
    ....
    [Ubuntu]
    # Specify the label name of the current machine as specified in your
    # VirtualBox configuration.
    label = Ubuntu
    
    # Specify the operating system platform used by current machine
    # [windows/darwin/linux].
    platform = linux
    
    # Specify the IP address of the current virtual machine. Make sure that the
    # IP address is valid and that the host machine is able to reach it. If not,
    # the analysis will fail.
    ip =  <guest IP>
    
    # (Optional) Specify the snapshot name to use. If you do not specify a snapshot
    # name, the VirtualBox MachineManager will use the current snapshot.
    # Example (Snapshot1 is the snapshot name):
    snapshot = <snapshotname>
    
    # (Optional) Specify the name of the network interface that should be used
    # when dumping network traffic from this machine with tcpdump. If specified,
    # overrides the default interface specified in auxiliary.conf
    # Example (vboxnet0 is the interface name):
    interface = <interface name> usually vboxnet0
    ````
### to be continued...
