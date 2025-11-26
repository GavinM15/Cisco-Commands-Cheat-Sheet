
### EtherChannel Troubleshooting

- `show interfaces status` - Display interface status, VLAN assignment, duplex, and speed
    - **Mode:** Privileged EXEC
    - Used to verify that all EtherChannel member interfaces are active and configured with identical speed and duplex settings.

- `show interfaces trunk` - Display all trunk interfaces and their VLAN details
    - **Mode:** Privileged EXEC
    - Confirms that EtherChannel member ports are in trunk mode and share identical native VLAN and allowed VLAN settings.
    - If any port is in access mode, fix it:
        - `configure terminal`
            - `interface range fa0/21 - 22`
                - `switchport mode trunk` - Set the interfaces to trunk mode
                - `switchport trunk native vlan 1` - Set the native VLAN
                - `switchport trunk allowed vlan all` - Allow all VLANs on the trunk
                - `end

- `show running-config interface <interface>` - Display configuration for a specific interface
    - **Mode:** Privileged EXEC
    - Used to compare configuration between EtherChannel member ports to confirm identical `switchport` and `channel-group` settings.

- `show etherchannel summary` - Display all EtherChannel groups, protocols, and port state
    - **Mode:** Privileged EXEC
    - Shows the protocol (LACP/PAgP), channel group, and status of each member interface.
    - `(P)` indicates ports are properly bundled; `(I)` indicates ports are not part of the bundle and must be corrected.

- `show running-config | include channel-group` - Display all channel-group configuration
    - **Mode:** Privileged EXEC
    - Confirms each interface is assigned to the correct channel group and mode (active/passive for LACP).

- `show etherchannel port` - Display detailed port-channel information
    - **Mode:** Privileged EXEC
    - Shows which ports belong to which EtherChannel and details about negotiation, partner IDs, and aggregation status.

- `show interfaces port-channel <number>` - Display logical Port-Channel interface status
    - **Mode:** Privileged EXEC
    - Verifies the operational state, VLANs, and speed of the Port-Channel interface.

- `show spanning-tree interface port-channel <number>` - Display STP status of the Port-Channel
    - **Mode:** Privileged EXEC
    - Confirms the Port-Channel is treated as a single logical interface in Spanning Tree Protocol, ensuring no loops.
- **Fixing a port stuck in (I) state:**
    - `configure terminal`
        - `interface range fa0/21 - 22`
            - `shutdown` - Disable the interfaces to reset them
            - `no channel-group 1` - Remove from the current channel group
            - `channel-group 1 mode active` - Re-add the interfaces using LACP mode
            - `no shutdown` - Re-enable the interfaces
            - `end`
        
- **Fixing protocol mismatch (PAgP → LACP):**
    - `configure terminal`
        - `interface range fa0/21 - 22`
            - `shutdown` - Disable the interfaces before changes
            - `no channel-group 1` - Remove old PAgP configuration
            - `channel-group 1 mode active` - Rebuild the EtherChannel using LACP (IEEE 802.3ad)
            - `no shutdown` - Enable interfaces after reconfiguration
            - `end`
                
- **Quick recovery procedure (rebuild broken EtherChannel):**
    
    - `configure terminal`
        
        - `interface range fa0/21 - 22`
            
            - `shutdown` - Disable the interfaces
            - `no channel-group 1` - Remove current configuration
            - `no interface port-channel 1` - Delete the logical Port-Channel
            - `channel-group 1 mode active` - Recreate using LACP
            - `no shutdown` - Bring the interfaces back online
            - `end`
- `show interfaces trunk` - Verify trunking after rebuilding the EtherChannel
    
    - **Mode:** Privileged EXEC
        
    - Confirms the Port-Channel is trunking correctly and all member interfaces share identical trunk settings.
        
- `show etherchannel summary` - Final verification of EtherChannel
    
    - **Mode:** Privileged EXEC
        
    - Confirms all ports are in the `(P)` state and the protocol shows **LACP** instead of PAgP.