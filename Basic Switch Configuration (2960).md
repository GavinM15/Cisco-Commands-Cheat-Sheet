 
### Basic Device Settings

- `enable` - Enter privileged EXEC mode
	- `configure terminal` - Enter global configuration mode
		- `hostname <name>` - Set device name
		- `no ip domain-lookup` - Disable DNS lookup to avoid typos freezing the CLI
		- `enable secret <password>` - Set encrypted enable (privileged) password
		- `line console 0` - Enter console line config
			- `password <password>` - Set console password
			- `login` - Require the console password at login
			- `logging synchronous` - Synchronized message output
			- `exec-timeout <0-35791> ` - Timeout in minutes
			- `exit`
		- `line vty 0 15` - Enter VTY (remote) lines config
			- `password`
			- `login`:
				- `login local`  - Local password checking, enable login using the local database
			- `transport`: 
				- `input`
				- `output`
					- `ssh`
					- `telnet`
					- `all` 
					- `none`
			- `exit`
		- `service password-encryption` - Obscure plaintext passwords in the config
		- `banner motd ^message^` - Display a legal warning banner
		- `copy running-config startup-config` - Save the configuration.


### VLAN Configuration

- `enable` 
	- `configure terminal`
		- `vlan <#>` - Create VLAN
			- `name <vlanname>` - Set VLAN name
		- `interface vlan <#>` - Enter SVI for VLAN
			- `ip address <ip> <subnet>` - Assign SVI IP
			- `no shutdown` - Activate the SVI


### Interface Access Mode Configuration

- `enable` 
	- `configure terminal` 
		- `interface <f0/#>` - Select interface to configure
		- `interface range <f0/# - #>`  - Select a range of interfaces
			- `switchport mode access` - Force access mode.
			- `switchport access vlan #` - Put interface in VLAN #
			- `no shutdown` - Ensure the port is enabled


### Interface Trunk Configuration

- `enable` 
	- `configure terminal` 
		- `interface <#>` - Select interface to configure
			- `switchport mode trunk` - Force trunking on interface
			- `switchport trunk native vlan <#>` - Set native VLAN
			- `switchport trunk allowed vlan #,#,... - Only allow the required VLANs
			- `no shutdown` - Ensure the trunk port is enabled
- `show interfaces trunk` - Verify trunk ports, native VLAN, and allowed VLANs