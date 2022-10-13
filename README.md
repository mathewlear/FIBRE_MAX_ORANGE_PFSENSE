# FIBRE MAX ORANGE PFSENSE
Configure Orange FTTH on Netagate 6100 pFsense+ 22.05

Requirements:
- Leox LXT-010H-D ONT "GPON <-> 2.5Gbe"
- Patched DHCP6C: https://github.com/mathewlear/FIBRE_MAX_ORANGE_PFSENSE/blob/main/dhcp6c
- Original DHCP6C in case rollback is needed: https://github.com/mathewlear/FIBRE_MAX_ORANGE_PFSENSE/blob/main/dhcp6c.old
- Generate option-90 using script or VirtIT blog post here: https://wiki.virtit.fr/doku.php/kb:linux:pfsense:remplacer_sa_box_orange_par_un_pfsense

Steps:
- Fresh install pFsense+ 22.05

-  Configuration of LXT-010H-D ONT with:
    - flash set GPON_SN SMBSxxxxxxxx
    - flash set PON_VENDOR_ID SMBS
    - flash set HW_HWVER SMBSSGLB6107

- Configuration of pFsense:

    - IPv4 WAN Interface
        - IPv4 Configuration Type: DHCP
        - Options Check: Advanced Configuration
        - Send options: dhcp-class-identifier "sagem",user-class "+FSVDSL_livebox.Internet.softathome.Livebox4",option-90 <https://bit.ly/3CoVwLq>
        - Request options: subnet-mask,broadcast-address,dhcp-lease-time,dhcp-renewal-time,dhcp-rebinding-time,domain-search,routers,domain-name-servers,option-90
        - Option modifiers: vlan-pcp 6

    - IPv6 WAN Interface
        - Options Check: Advanced Configuration
        - DHCPv6 Prefix Delegation size: None
        - Do not wait for a RA: Check
        - DHCP6 VLAN Priority: Internetwork Control (IC,6)
        - Send options: ia-pd 0, raw-option 6 00:0b:00:11:00:17:00:18, raw-option 15 00:2b:46:53:56:44:53:4c:5f:6c:69:76:65:62:6f:78:2e:49:6e:74:65:72:6e:65:74:2e:73:6f:66:74:61:74:68:6f:6d:65:2e:6c:69:76:65:62:6f:78:33,  raw-option 16 00:00:04:0e:00:05:73:61:67:65:6d, raw-option 11 <https://bit.ly/3CoVwLq>
        - Prefix Delegation: id-assoc pd ID = 0
        - Prefix interface statement: Prefix Interface sla-id = 0, sla-len = 8
        - Prefix Interface: LAN

Notes:
- If all above is configured correctly you should receive DHCPv6 request on wan interface and orange reply with your /56
- Also, your lan interface will auto assign IPv6 address from this /56
        - 
