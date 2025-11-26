
### Configure exclude addresses  

- Enter Global Configuration mode  `R1(config)#`
- `ip dhcp excluded-address <ip> <ip>`
	- Excludes the ip's in the range specified

### Create DHCP Pool 

- Enter Global Configuration mode  `R1(config)#`
- `ip dhcp pool <name>` - creates a pool and enters DHCP config mode `R1(dhcp-config)#` 
	- `network <ip> <subnet>` - Defines the address range DHCP will allocate available addresses from excluding any addresses reserved earlier
	- `default-router <ip>` - Specifies the default gateway that DHCP clients will use
	- `dns-server <ip>` - Specifies the DNS server that clients will use
- 


