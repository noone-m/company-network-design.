# Company Network Design

This project involves designing a network for a company consisting of three main departments, each with its own building. The network infrastructure aims to provide connectivity between departments, ensure efficient communication within each department, and implement necessary security measures.

## Topology

![Topology](https://github.com/noone-m/company-network-design./assets/112755676/4a29b05a-0c85-4295-9af4-83261ce1a22d)

## Departments Description

### BR-1 Department
- Location: Building with one floor
- Sections:
  - Human Resources: 2 employees
  - Marketing: 50 employees
  - Accounting: 25 employees
  - Procurement: 13 employees
  - IT: 1 employee

### BR-2 Department
- Location: 50KM away from BR-1
- Same design as BR-1

### BR-3 Department
- Location: 200KM away from BR-1
- Same design as BR-1

### BR-Data Center
- Location: Near BR-1
- Contains:
  - DHCP server
  - DNS server
  - TFTP server

## Internal Network Design
- Each section contains three devices except for HR (2 devices) and IT (1 device).
- Every section has a switch connecting all devices.
- All switches are connected to a Core-Switch.
- Each Core-Switch is connected to the department's router.

## Implementation Requirements
1. Utilize Packet Tracer for network design.
2. Design IP addressing scheme for each section to minimize IP wastage.
3. Employ DHCP server for IP assignment to PCs. Use static IPs for servers and router interfaces.
4. Utilize Ethernet channel between switches and core switches.
5. Apply "Port Security" on one switch for enhanced security.
6. Implement VLAN for all employees and distribute it across switches using VTP.
7. Allow IT section device access to all department routers for SSH configuration.
8. Implement appropriate routing algorithm (static or dynamic) between all routers.
9. Store router settings in TFTP for easy backup and recovery.
10. Utilize "Default route" between department routers and ISP for internet access.
11. Implement "Access Control List" to restrict ping access, so that only the IT device in BR-1 can ping all devices

## Additional Notes
- Not all employees have PCs.
- Ensure redundancy and fault tolerance where applicable.
- Document any assumptions made during the design process.

## Configuration Commands
Commands I've used in this project.
<pre>
# Configure SSH on Router

en
conf t
hostname BR-2
enable password 123
username admin password admin
ip domain-name cisco.com
crypto key generate rsa
line vty 0 15
login local
transport input ssh
exec-timeout 5
exit
ip ssh version 2

# Connect to Router via SSH
ssh -l admin <ip>

# Configure TFTP Server
en
copy running-config tftp
Address or name of remote host []? 192.168.1.44
Destination filename [BR-2-config]? 

# Configure VTP VLAN Trunk
en
conf t
vlan 10
name IT 
vlan 20 
name HR
vlan 30
name Accounting
vlan 40 
name Marketing
vlan 50 
name Procurement
exit
exit
en 
conf t
vtp mode server
vtp domain BR1
vtp password 123
exit
en 
conf t
vtp mode client
vtp domain BR1
vtp password 123

# Configure Inter-VLAN Routing
en 
conf t
interface f0/0.20
encapsulation dot1Q 20
ip address 192.168.1.9 255.255.255.248
exit

# DHCP Configuration
en
conf t
interface f0/0
ip helper-address 192.168.1.43
exit

# Port Security Configuration
en
show port-security
conf t
interface <interface>
switchport mode access
switchport port-security
switchport port-security maximum 1
exit

# Default Route Configuration
en
conf t
ip route 0.0.0.0 0.0.0.0 <interface>

# Access List Configuration in BR2 Router
en
conf t
access-list 20 remark ACL_TO_BR2
access-list 20 permit host 192.168.1.1
access-list 20 deny any
exit
show access-list 20
conf t
interface f0/0.20
ip access-group 20 out
exit
</pre>
## Useful Resources
- [Jermy's IT Lab](https://www.youtube.com/watch?v=XgcGcrLKu1A&list=PLxbwE86jKRgMQ4HTuaJ7yQgA2BoNwY9ct)
- [Access List](https://content.cisco.com/chapter.sjs?uri=/searchable/chapter/content/en/us/td/docs/ios-xml/ios/sec_data_acl/configuration/15-sy/sec-data-acl-15-sy-book/sec-create-ip-apply.html.xml)
