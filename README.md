# ACL-Creation

#Network Topology

<img width="882" height="578" alt="image" src="https://github.com/user-attachments/assets/992a8943-ed2c-43a6-8b25-bd239cc37f06" />

</br> The following lab shows how a python script can automate the creation of ACLs (Access Control Lists) which block traffic on a network; they prevent specific computers from communicating to any given system.

### Devices Used:
- Cisco IOSv 15.7 router
- Cisco IOSvL2 15.2.1 switch
- GNS3 Software
- Ubuntu Container (running on VMware machine)


## Configurations

The following configurations can be performed on the "/etc/network/interfaces" configuration file.
<br>Management:<img width="1305" height="606" alt="image" src="https://github.com/user-attachments/assets/a5ba58e1-5bb6-4f4d-ac39-ffe5cd93bfa5" />

<br>User 1:<img width="1303" height="607" alt="image" src="https://github.com/user-attachments/assets/c334bd2e-050d-4736-8460-df608b9cdef5" />

<br>User 2:<img width="1303" height="604" alt="image" src="https://github.com/user-attachments/assets/36c604e7-4792-465d-ada8-01f1847037b6" />

<br>User 3:<img width="1300" height="612" alt="image" src="https://github.com/user-attachments/assets/6fe020f4-fa32-42dc-9fda-67c8d80751a9" />





## Script in Action

Below is the script being run and me entering the specified values. Note that the interface the script asks for is actually a subinterface of the "physical" g0/0 port. This subinterface is necessary when routing between VLANs.
<img width="1307" height="868" alt="image" src="https://github.com/user-attachments/assets/69b84ea3-bcaf-43b2-b784-ea5b0cfd5e20" />

</br>These are the configuration results on the router demonstrating that the ACLs have been implemented:
<img width="1304" height="831" alt="image" src="https://github.com/user-attachments/assets/acefb565-c738-447a-af78-273bb1616d45" />

</br>This is ping results of each machine showing that no traffic can travel from each user to any address on the 192.168.99.0 subnet. Note: Although ACLs are one-way, if you try ping from the Managment computer to any other user, the ping will work but you will not receive a reply back.
User 1:<img width="1300" height="360" alt="image" src="https://github.com/user-attachments/assets/ea53ef2f-6c83-46c6-9f9b-8cb645bc914a" />
</br>User 2:<img width="1301" height="341" alt="image" src="https://github.com/user-attachments/assets/7a5670ea-edaa-4932-b21d-7f0973931803" />
</br>User 3:<img width="1299" height="340" alt="image" src="https://github.com/user-attachments/assets/643ef73e-0489-4129-ac54-e6ea0de81088" />



