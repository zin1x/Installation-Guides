# Pfsense - OpenVPN Client Setup Guide


## Requirements
  - Pfsense CE 2.4.4
  - OpenVPN server configured using https://github.com/angristan/openvpn-install (default configurations)
  - Passwordless OpenVPN client configuration file (e.g. client.ovpn)

## Objective
Allow Clients behind the PFsense to be able to access the internet via the OpenVPN session that PFSense have established with the OpenVPN Server
> Clients ---> PFsense ---> OpenVPN Server ---> Internet

## PFSense Configurations

### System/Certificate Manager/CAs
- Add
- Create/Edit CA
	- Descrptive name - Enter a Descriptive Name (e.g. MyVPNCA)
	- Method - Import an existing Certificate Authority
- Existing Certificate Authority
	- Certificate data - Copy Paste the contents within <ca> </ca> from .ovpn file
	- Certificate Private Key(Optional) - (Blank)
	- Serial for next certificate - (Blank)

### System/Certificate Manager/Certificates
- Add
- Add/Sign a New Certificate
	- Method - Import an existing Certificate
	- Descriptive name - Enter a descriptive name (e.g. MYVPNCA Cert)
- Import Certificate
	- Certificate data - Copy Paste the contents within <cert> </cert> from .ovpn file
	- Private key data - Copy Paste the contents within <key> </key> from .ovpn file

### Interfaces
- Assignments
- Interface Assignments
	- Click on the non-assigned Network port (e.g. OPT1) and give it a name (e.g. MyVPNInterface)

### Firewall
- NAT
- Outbound
	- Outbound NAT Mode
		Select Manual Outbound NAT rule generation
- Mappings
	- Copy all of the default mappings for IPV4 (e.g. those with description 'Auto created rule for ....')
	- Edit all of them, change the Interface to 'MyVPNInterface'
	- Disable all of the original ones

- Rules
	- LAN
	- Disable the IPv6 rule
	- Edit the IPv4 rule
		- Edit Firewall Rule
			- Protocol - Any (or whichever that you need)
		- Extra Options
		- Display Advanced
			- Gateway - Select MyVPNInterface_VPNV4...

### VPN
- Clients
- Add
- General Information
	- Server mode  	- Peer to Peer (SSL/TLS)
	- Protocol	- UDP on IPv4 only
	- Device mode	- tun - Layer 3 Tunnel Mode
	- Interface 	- WAN
	- Local port	- (Blank)
	- Server host or address - IP of OVPN Server
	- Server port	- Port of OVPN server
	- Proxy host or address - (Blank)
	- Proxy port	- (Blank)
	- Proxy Authentication - none
	- Description	- (Blank)
- User Authentication Settings
	- Username 	- (Blank)
	- Password	- (Blank)
	- Authentication retry - (unticked)
- Cryptographic Settings
	- TLS Configuration - Tick 'Use a TLS Key'
	- TLS Key	- Copy paste the contents within <tls-crypt> </tls-crypt>, startingg and ending with ---- BEGIN OpenVPN ...etc
	- TLS Key Usage Mode - **TLS Encryption and Authentication**
	- Peer Certificate Authority - Select the CA entry you created previously (e.g. MyVPNCA)
	- Client Certificate	- Select the Certificate entry you created previously (e.g. MyVPNCA Cert)
	- Encryption Algorithim - Match with the entry in .ovpn file (e.g. select AES-128-GCM(128 bit key, 128 bit block) for `cipher AES-128-GCM`)
	- Enable NCP		- (untick)
	- NCP Algorithms	- (blank)
	- Auth digest algorithim - Match with the entry in .ovpn file (e.g. select SHA256 (256-bit) for `auth SHA256`)
	- Hardware Crypto	- No Hardware Crypto Acceleration

- Tunnel Settings
	- IPv4 Tunnel Network	- (Blank)
	- IPv6 Tunnel Network	- (Blank)
	- IPv4 Remote network(s) - (Blank)
	- IPv6 Remote network(s) - (Blank)
	- Limit outgoing bandwidth - (Blank)
	- Compression			- **Omit Preference (Use OpenVPN Default)**
	- Topology			- Subnet - One IP address per client in a common subnet
	- Type-of-Service		- (unticked)
	- Don't pull routes		- (unticked)
	- Dont add/remove routes	- (unticked)
- Advanced Configuration
	- Custom options		- (Blank)
	- UDP Fast I/O			- (Unticked)
	- Send/Receive Buffer		- Default
	- Gateway Creation		- (Unselected)
	- Verbosity level		- Default

### Status/OpenVPN
 Check for the status of the PFSense's OpenVPN connection under `Client Instance Statistics` 
