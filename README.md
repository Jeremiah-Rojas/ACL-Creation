# ACL-Creation

#Network Topology

<img width="752" height="458" alt="image" src="https://github.com/user-attachments/assets/992a8943-ed2c-43a6-8b25-bd239cc37f06" />

</br> The following lab shows how a python script can automate the creation of ACLs (Access Control Lists) which block traffic on a network; they prevent specific computers from communicating to any given system.

### Devices Used:
- Cisco IOSv 15.7 router
- Cisco IOSvL2 15.2.1 switch
- GNS3 Software
- Ubuntu Container (running on VMware machine)


## Configurations

The following configurations can be performed on the "/etc/network/interfaces" configuration file.
<br>Management:<img width="700" height="356" alt="image" src="https://github.com/user-attachments/assets/a5ba58e1-5bb6-4f4d-ac39-ffe5cd93bfa5" />

<br>User 1:<img width="705" height="356" alt="image" src="https://github.com/user-attachments/assets/c334bd2e-050d-4736-8460-df608b9cdef5" />

<br>User 2:<img width="705" height="356" alt="image" src="https://github.com/user-attachments/assets/36c604e7-4792-465d-ada8-01f1847037b6" />

<br>User 3:<img width="705" height="356" alt="image" src="https://github.com/user-attachments/assets/6fe020f4-fa32-42dc-9fda-67c8d80751a9" />

The following configurations can either copied and pasted as a whole bewteen the break points or manually entered.
</br>switch configurations:
```
enable
conf t
host cisco-switch
exit

conf t

vlan 2
name VLAN-2
exit

vlan 3
name VLAN-3
exit

vlan 4
name VLAN-4
exit

vlan 99
name MANAGEMENT
exit

interface g0/1
switchport mode access
switchport access vlan 2
exit

interface g0/2
switchport mode access
switchport access vlan 3
exit

interface g0/3
switchport mode access
switchport access vlan 4
exit

interface g3/3
switchport mode access
switchport access vlan 99
end

conf t
int g0/0
switchport trunk encapsulation dot1q
switchport mode trunk
end
wr

```
<br>Router configurations:
```
conf t
host cisco-router
exit

conf t

interface g0/0
ip address 192.168.0.1 255.255.255.0
exit

interface g0/0.2
encapsulation dot1q 2
ip address 192.168.2.1 255.255.255.0
exit

interface g0/0.3
encapsulation dot1q 3
ip address 192.168.3.1 255.255.255.0
exit

interface g0/0.4
encapsulation dot1q 4
ip address 192.168.4.1 255.255.255.0
exit

interface g0/0.99
encapsulation dot1q 99
ip address 192.168.99.1 255.255.255.0
end


conf t
int g0/0
no shut
end

conf t
username msfadmin pass msfadmin
username msfadmin priv 15

line vty 0 4
login local
transport input all

ip domain-name example.com
crypto key generate rsa
1024

end
wr

```

## Script in Action

Below is the script being run and me entering the specified values. Note that the interface the script asks for is actually a subinterface of the "physical" g0/0 port. This subinterface is necessary when routing between VLANs.
<img width="700" height="400" alt="image" src="https://github.com/user-attachments/assets/69b84ea3-bcaf-43b2-b784-ea5b0cfd5e20" />

</br>These are the configuration results on the router demonstrating that the ACLs have been implemented:
<img width="700" height="400" alt="image" src="https://github.com/user-attachments/assets/acefb565-c738-447a-af78-273bb1616d45" />

</br>This is ping results of each machine showing that no traffic can travel from each user to any address on the 192.168.99.0 subnet. Note: Although ACLs are one-way, if you try ping from the Management computer to any other user, the ping will work but you will not receive a reply back.
</br>User 1:<img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/ea53ef2f-6c83-46c6-9f9b-8cb645bc914a" />
</br>User 2:<img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/7a5670ea-edaa-4932-b21d-7f0973931803" />
</br>User 3:<img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/643ef73e-0489-4129-ac54-e6ea0de81088" />


## Python Script

It is recommended to ssh manually first; this clears out any options you have to agree to that would typically through an error to the python script.
```
# This Script automates the creation of ACLs on a router (the device that handles inter-VLAN routing)
# Note: in your case, a different device may handle inter-VLAN router

from netmiko import ConnectHandler
# ------------------------------ DEVICE DEFINITIONS ------------------------------
# Router Connection Info
iosv = {
    'device_type': 'cisco_ios',
    'ip': '192.168.99.1',
    'username': 'msfadmin',
    'password': 'msfadmin'
}

# User inputs

name = input("Enter the name of the ACL: ")
ACL_name = name.replace(" ", "_")

source_IP = input("From what IP address/range is the traffic coming from: ")
dest_IP = input("From what IP address/range is the traffic going to?: ")

while True:
    ACL_action = input(f"For traffic going from {source_IP} to {dest_IP},\nshould this rule (deny/permit)?: ").lower()
    if ACL_action in ("deny", "permit"):
        break
interface = input("Which interface should the ACL be applied to?: ")

net_connect = ConnectHandler(**iosv)

# Router commands pushed the network device

router_commands = [
    # Internet-facing interface
    f"ip access-list extended {ACL_name}",
    "no 1000",
    f"{ACL_action} ip {source_IP} 0.0.0.255 {dest_IP} 0.0.0.255",
    "1000 permit ip any any",
    "exit",

    f"interface {interface}",
    f"ip access-group {ACL_name} in",
    "end"]

print("Applying router configuration...")
net_connect.send_config_set(router_commands)
net_connect.save_config()
net_connect.disconnect()

print("Configuration Complete!")
```
