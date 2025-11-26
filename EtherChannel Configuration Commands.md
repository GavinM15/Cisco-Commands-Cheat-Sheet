
### Verify Interface and Trunk State

- **Mode:** Privileged EXEC (`Switch#`)
    
    - `show interfaces | include Ethernet`  
        Displays interface information filtered for Ethernet ports to verify their status before configuration.  
        Useful for confirming which ports exist and are active.
        
    - `show interface status`  
        Lists all interfaces, their VLANs, duplex, speed, and operational state.  
        Confirms if ports are up and available for EtherChannel.
        
    - `show interfaces trunk`  
        Shows all trunk ports, encapsulation (802.1Q), native VLAN, and allowed VLANs.  
        Used to verify trunk configuration consistency between switches.
        

---

### Configure Trunk Ports

- **Mode:** Interface Configuration (`Switch(config-if)#`)
    
    - `switchport mode trunk`  
        Forces the interface into trunk mode — required for multi-VLAN EtherChannel.  
        Overrides Dynamic Trunking Protocol (DTP) negotiation.
        
    - `switchport nonegotiate`  
        Disables DTP on the port. Use this to stop automatic trunk negotiation when connecting to another manually configured trunk.
        
    - `shutdown` / `no shutdown`  
        Disables/enables an interface. Best practice: shut down before creating a channel group to avoid `err-disabled` state.
        

---

### Create a PAgP EtherChannel (Cisco Proprietary)

- **Mode:** Interface Range Configuration (`Switch(config-if-range)#`)
    
    - `channel-group <number> mode desirable`  
        Adds the selected interfaces to an EtherChannel (Port-Channel).  
        **`mode desirable`** enables _active negotiation_ of Port Aggregation Protocol (PAgP).  
        Both sides must be either `desirable` ↔ `auto` or `desirable` ↔ `desirable`.
        
    - `interface port-channel <number>`  
        Enters configuration for the newly created logical Port-Channel interface.  
        Used to apply VLAN, trunk, or Layer-3 settings to the bundle as a single link.
        

---

### Verify PAgP EtherChannel

- **Mode:** Privileged EXEC (`Switch#`)
    
    - `show etherchannel summary`  
        Displays all EtherChannels, their protocol (PAgP/LACP/static), and member ports.  
        **Key Flags:**
        
        - `S` = Layer 2, `U` = in use, `P` = in port-channel
            
        - `D` = down, `I` = stand-alone, `s` = suspended  
            Use to confirm operational status and port membership.
            
    - `show spanning-tree`  
        Verifies that the Port-Channel is treated as one logical link by STP.  
        Ensures no redundant loop or blocking misconfiguration.
        

---

### Create an LACP EtherChannel (IEEE 802.3ad)

- **Mode:** Interface Range Configuration (`Switch(config-if-range)#`)
    
    - `channel-group <number> mode active`  
        Adds interfaces to an EtherChannel using _Link Aggregation Control Protocol (LACP)_.  
        **`active`** mode initiates negotiation.  
        Compatible combinations:
        
        - `active` ↔ `active`
            
        - `active` ↔ `passive`
            
    - `channel-group <number> mode passive`  
        Adds ports to an EtherChannel that _responds_ to LACP negotiation but doesn’t start it.  
        If both sides are `passive`, the channel will **not** form.
        
    - `channel-group <number> mode on`  
        Statically bundles interfaces without negotiation (no LACP/PAgP).  
        Use only when both sides are configured identically and negotiation is not desired.
        

---

### Verify LACP EtherChannel

- **Mode:** Privileged EXEC (`Switch#`)
    
    - `show etherchannel summary`  
        Confirms ports are grouped and protocol used is LACP.  
        Ports should display `(SU)` for active EtherChannel status.
        
    - `show interfaces trunk`  
        Ensures the logical Port-Channel is trunking with correct VLANs and native VLAN.
        

---

### Configure Redundant EtherChannel Link (Example: Port-Channel 3)

- **Mode:** Interface Range Configuration (`Switch(config-if-range)#`)
    
    - `channel-group <number> mode passive`  
        Creates a standby or secondary EtherChannel that negotiates only when another LACP-capable device is detected.  
        Used for redundancy or staged link aggregation.
        
    - `channel-group <number> mode active`  
        Creates an LACP EtherChannel that always initiates negotiation — ideal for the opposite side of a passive configuration.  
        Ensures the bundle forms automatically.
        

---

### Spanning Tree Adjustment (Root Bridge Configuration)

- **Mode:** Global Configuration (`Switch(config)#`)
    
    - `spanning-tree vlan <vlan-id> root primary`  
        Forces this switch to become the root bridge for the specified VLAN.  
        Automatically sets bridge priority lower than all others.
        
    - `spanning-tree vlan <vlan-id> priority <value>`  
        Manually sets bridge priority (default = 32768). Lower = higher root priority.  
        Example: `spanning-tree vlan 1 priority 24576` makes VLAN 1 prefer this switch as root.
        

---

### Additional Verification Commands

- **Mode:** Privileged EXEC (`Switch#`)
    
    - `show interfaces port-channel <number>`  
        Displays detailed status for the logical EtherChannel interface, including member ports and load-balancing method.
        
    - `show spanning-tree active`  
        Lists the current STP roles and states of each active interface.  
        Confirms Port-Channel participation (e.g., Po1 = Root FWD).
        
    - `show running-config interface port-channel <number>`  
        Verifies configuration applied to the logical interface.
        

---

### Key Compatibility Rules

- **PAgP:**
    
    - `desirable` ↔ `desirable` ✅
        
    - `desirable` ↔ `auto` ✅
        
    - `auto` ↔ `auto` ❌ (no channel formed)
        
- **LACP:**
    
    - `active` ↔ `active` ✅
        
    - `active` ↔ `passive` ✅
        
    - `passive` ↔ `passive` ❌
        
- **Channel Numbers:**  
    Channel-group numbers are _local_ — they do not need to match on both ends (e.g., Po1 on one side ↔ Po5 on the other).