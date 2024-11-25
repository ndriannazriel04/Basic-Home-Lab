## PEERING ISP WITH GROUP MEMBERS USING EBGP

### ISP1 (MY ISP)
router bgp 19
bgp router-id 2.2.2.2 
neighbor 100.100.99.1 remote-as 29
network 100.100.99.0 mask 255.255.255.252
network 150.99.0.0 mask 255.255.0.0

router bgp 19
bgp log-neighbor-changes
address-family ipv6
neighbor 2001:100:100:99:: remote-as 29
neighbor 2001:100:100:99:: activate
network 2001:100:100:99::/127
network 2001:150:99::/48


## Configure Local AAA

### Requirements 
- Only users from VLAN 101 can initiate SSH sessions to the ISP router.
- Users must authenticate using locally defined credentials.

aaa new-model
aaa authentication login SSH_LOGIN local

username admin privilege 15 secret your_password

access-list 101 permit ip 142.99.2.0 0.0.0.127 any

line vty 0 4
access-class 101 in
login authentication SSH_LOGIN
transport input ssh

ssh admin@142.99.2.1

## Reconfigure DSW4 After Changing

vtp mode client
vtp domain ANDRIAN
vtp password ANDRIAN

interface <>
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan all

## Enable Port Security

- Apply **only** on access port

### DSW4
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown/protect/restrict
switchport port-security mac-address <MAC_ADDRESS>

show port-security

## Change Native Vlan 99
Apply this change to **all trunk interfaces** on switches DSW1 to DSW4 (10 interfaces total).

vlan 99
name NATIVE

interface <>
switchport trunk native vlan 99

## Enable Spanning Tree Port-Fast And BDPU Guard

### DSW3
interface <>
spanning-tree portfast
spanning-tree bpduguard enable

spanning-tree interface <> detail
show spanning-tree summary

## Configure R2 as DHCP Server
Ensure service dhcp is configured on dsw1 and dsw2

### R2
ip dhcp pool vlan102
network 
default-router 
dns-server 8.8.8.8
domain-name example.com

ip dhcp excluded-address 

show ip dhcp binding




