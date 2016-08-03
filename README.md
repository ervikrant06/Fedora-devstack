### Fedora-devstack
### Installation steps for devstack on Fedora
### Tested on Fedora 24

Step 1 : Install the Fedora machine.

Step 2 : Install the git-core package.

# dnf install -y git-core

Step 3 : Clone the devstack repostiory.

# git clone https://git.openstack.org/openstack-dev/devstack

It will create a devstack directory with below content in the root home director.

[root@dhcp6-23 ~]# cd devstack/
data/      exercises/ files/     .git/      lib/       samples/   tools/     
doc/       extras.d/  gate/      inc/       pkg/       tests/   

Step 4 : Need to modify the local.conf file present in below path to install various openstack components, we will do that in Step 7 to avoid redundant work of copying this file. 

[root@dhcp6-23 samples]# pwd
/root/devstack/samples
[root@dhcp6-23 samples]# ls
local.conf  local.sh

Step 5 : Before doing the modification to that file, first run a script to create stack user.

# sh /root/devstack/tools/create-stack-user.sh

Step 6 : Login as stack user and then clone the devstack repository again.

[root@dhcp6-23 samples]# su - stack
[stack@dhcp6-23 ~]$ pwd
/opt/stack
[stack@dhcp6-23 ~]$ git clone https://git.openstack.org/openstack-dev/devstack

Step 7 : Create local.conf file with below content. Need to replace IP  and name of PUBLIC_INTERFACE according to your environment.

[stack@dhcp6-23 devstack]$ cat local.conf 
[[local|localrc]]                                                                                                
HOST_IP=10.65.6.23                                                                                              
SERVICE_HOST=10.65.6.23                                                                                         
MYSQL_HOST=localhost                                                                                             
RABBIT_HOST=10.65.6.23                                                                                          
GLANCE_HOSTPORT=10.65.6.23:9292                                                                                 
ADMIN_PASSWORD=redhat                                                                                            
DATABASE_PASSWORD=redhat                                                                                         
RABBIT_PASSWORD=redhat                                                                                           
SERVICE_PASSWORD=redhat                                                                                          
                                                                                                                 
# Do not use Nova-Network                                                                                        
disable_service n-net                                                                                            
# Enable Neutron                                                                                                 
ENABLED_SERVICES+=,q-svc,q-dhcp,q-meta,q-agt,q-l3                                                                
                                                                                                                 
## Neutron options                                                                                               
Q_USE_SECGROUP=True                                                                                              
FLOATING_RANGE="10.65.10.0/24"                                                                                   
FIXED_RANGE="10.0.0.0/24"                                                                                        
Q_FLOATING_ALLOCATION_POOL=start=10.65.10.250,end=10.65.10.254                                                   
PUBLIC_NETWORK_GATEWAY="10.65.15.254"                                                                            
PUBLIC_INTERFACE=ens3                                                                                      
                                                                                                                 
# Open vSwitch provider networking configuration                                                                 
Q_USE_PROVIDERNET_FOR_PUBLIC=True                                                                                
OVS_PHYSICAL_BRIDGE=br-ex                                                                                        
PUBLIC_BRIDGE=br-ex                                                                                              
OVS_BRIDGE_MAPPINGS=public:br-ex                                                                                 
                                                                                                                 
# Enable swift                                                                                                   
# ENABLED_SERVICES+=,s-proxy,s-object,s-container,s-account                                                        
# SWIFT_HASH=12345                                                                                                 
                                                                                                                 
# enable gnocchi                                                                                                 
enable_plugin gnocchi https://github.com/openstack/gnocchi master                                                
enable_service gnocchi-api,gnocchi-metricd                                                                       
# gnocchi-grafana support                                                                                        
enable_service gnocchi-grafana

# manila
enable_plugin manila https://github.com/openstack/manila

Step 8 : Run the setup script and grab a cup of coffee. It will take approx 25-30 mins to complete the installation.

[stack@dhcp6-23 ~]$ cd devstack/
[stack@dhcp6-23 devstack]$ ./setup.py

Step 9 : Once the installation is completed below sort of message will come.

This is your host IP address: 10.65.6.23
This is your host IPv6 address: ::1
Horizon is now available at http://10.65.6.23/dashboard
Keystone is serving at http://10.65.6.23/identity/
The default users are: admin and demo
The password: redhat
2016-08-03 14:14:06.922 | WARNING: 
2016-08-03 14:14:06.923 | Using lib/neutron-legacy is deprecated, and it will be removed in the future
2016-08-03 14:14:06.976 | 'MANILA_MULTI_BACKEND' is deprecated, it makes influence only when is set to True and 'MANILA_ENABLED_BACKENDS' is not set. Use 'MANILA_ENABLED_BACKENDS' instead if you want to use custom setting. Set there a list of back end names to be enabled.
2016-08-03 14:14:06.980 |  To configure custom back ends use (any opt in any group can be set in this way) following: MANILA_OPTGROUP_foo_bar=value where 'foo' is name of config group and 'bar' is name of option.
2016-08-03 14:14:06.993 | 
2016-08-03 14:14:06.993 | stack.sh completed in 5115 seconds.

Step 10 : openrc file will be present in devstack directory. Need to source this file to issue the openstack commands.

[stack@dhcp6-23 devstack]$ source openrc 
WARNING: setting legacy OS_TENANT_NAME to support cli tools.
[stack@dhcp6-23 devstack]$ nova list
+----+------+--------+------------+-------------+----------+
| ID | Name | Status | Task State | Power State | Networks |
+----+------+--------+------------+-------------+----------+
+----+------+--------+------------+-------------+----------+
