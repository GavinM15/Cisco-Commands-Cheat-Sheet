### Basic Device Settings

- `enable` - Enter privileged EXEC mode
	- `configure terminal` - Enter global configuration mode
		- `hostname <name>` - Set device name
		- `no ip domain-lookup` - Disable DNS lookup to avoid typos freezing the CLI
		- `ip domain-name <name>` - Define the default domain name 
		- `enable secret <password>` - Set encrypted enable (privileged) password
		- `username <username> secret <password>` - creates a user and password, store password in encrypted form
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
		- `ipv6 unicast-routing` - Enable ipv6 routing
		- `copy running-config startup-config` - Save the configuration.

### IPv4 Configuration

 ##### Global Config Mode
- `interface <interface>` - Enter an interface to configure
	- `description <text>` - Add description
	- `ip address <IPv4 address> <IPv4 mask>` - Set address and subnet mask
	- `no shutdown` - Bring the interface up

### IPv6 Configuration

 ##### Global Config Mode
- `interface <interface>` - Enter an interface to configure
	- `description <text>` - Add description
	- `ipv6 address <IPv6 address/prefix-length>` - Set address and prefix
	- `ipv6 address <IPv6 address> link-local` - Set the link-local address
	- `no shutdown` - Bring the interface up