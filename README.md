🏭 vxlan\_evpn\_fabric
======================

🪄 Configure VXLAN EVPN fabric in multisite clos-network on Cisco NXOS platform.<br>
🧪 Tested on Cisco Nexus C93180YC-FX and C92348GC-X.

👇 **Underlay:**
- OSPF routing.
- P2P L3 links.
- ECMP.
- PIM Sparse with Anycast RP.
<img src="./files/vxlan_evpn_underlay.png" alt="Underlay" width="600"/>

👇 **Overlay:**
- iBGP EVPN control plane.
- Multicast-replication (BUM).
<img src="./files/vxlan_evpn_overlay.png" alt="Overlay" width="600"/>

👇 **DCI:**
- Border-leafs in vPC.
- eBGP in DCI underlay.
- eBGP in DCI overlay.
- Ingress-replication (BUM).
- MACsec encryption.
<img src="./files/vxlan_evpn_dci.png" alt="L2 Host Segment" width="600"/>

👇 **L2 Host Segment:**
- No distributed anycast gateway.
- No VLAN SVI.
- No VRF.
- No ARP suppression.
<img src="./files/vxlan_evpn_host_segment_l2.png" alt="L2 Host Segment" width="600"/>

👇 **L3 Host Segment:**
- Distributed anycast gateway.
- Multi-tenancy (VRFs).
- ARP suppression at leafs.
<img src="./files/vxlan_evpn_host_segment_l3.png" alt="L2 Host Segment" width="600"/>

👇 **Broadcast, unknown Unicast and Multicast:**<br>
- Multisite BUM is always ingress replicate (unicast).
<img src="./files/vxlan_evpn_bum.png" alt="BUM traffic" width="600"/>

📚 **Resources:**
- [Cisco Nexus VXLAN configuration guide](https://www.cisco.com/c/en/us/td/docs/dcn/nx-os/nexus9000/101x/configuration/vxlan/cisco-nexus-9000-series-nx-os-vxlan-configuration-guide-release-101x/m_n9k_software_preface.html)<br>
- [Cisco VXLAN-EVPN Multi-Site Design and Deployment whitepaper](https://www.cisco.com/c/en/us/products/collateral/switches/nexus-9000-series-switches/white-paper-c11-739942.html#Introduction)<br>
- [Ansible documentation for Cisco Nexus](https://docs.ansible.com/ansible/latest/collections/cisco/nxos/index.html)<br>

Requirements
------------

💿 [Cisco NXOS Collection](https://galaxy.ansible.com/cisco/nxos) <br>

Role Variables
--------------

defaults/main.yml:
- ospf\_area\_id
- ospf\_process\_id
- pim\_anycast\_rp\_ip
- pim\_group\_range
- bgp\_neighbors
- anycast\_gw\_mac
- vrfs
- host\_segments\_l3
- host\_segments\_l2

vars/main.yml:
- features
- mtu

vars/[spine,leaf,border-leaf].yml:
- fabric\_interfaces

vars/spine.yml:
- bgp\_route\_reflector
- pim\_rp

vars/border-leaf.yml:
- rmap\_redist\_direct\_name
- rmap\_redist\_direct\_tag
- macsec\_key
- nve\_infra\_vlan

hostvars/[spine,leaf,border-leaf].yml:
- location
- network\_function
- bgp\_asn
- rid\_ip

hostvars/[leaf,border-leaf].yml:
- vtep\_ip

hostvars/border-leaf.yml:
- vtep\_vpc\_vip
- nve\_infra\_vlan
- dci\_vip
- dci\_interfaces
  - name
  - ip
  - bgp\_neighbor\_ip
  - bgp\_neighbor\_name
  - bgp\_remote\_asn

Dependencies
------------

💿 [cisco\_nxos\_vpc](https://galaxy.ansible.com/jiholland/cisco_nxos_vpc)

Example Playbook
----------------

    ---
    - name: Build VXLAN-EVPN fabric
      hosts: "{{ target }}"
      gather_facts: false

      roles:
        
        - role: cisco_nxos_vpc
          tags: vpc
          when: network_function == 'border-leaf'

        - role: vxlan_evpn_fabric
          tags: vxlan

License
-------

BSD

Author Information
------------------

Jørn Ivar Holland
