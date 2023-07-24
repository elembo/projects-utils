# Cisco SD-WAN

## Initial Config

#### Boostrap
    system
     system-ip             1.1.1.4
     overlay-id            1
     site-id               [site_id]
     port-offset           0
     control-session-pps   300
     admin-tech-on-failure
     sp-organization-name  "[org_name]"
     organization-name     "[org_name]"
     port-hop
     track-transport
     track-default-gateway
     console-baud-rate     9600
     no on-demand enable
     on-demand idle-timeout 10
     vbond [vbond_fqdn] port 12346
    
    ip host [vbond_fqdn] [vbond_ip_addredd]
    
    interface Tunnel[if_index_interface]
     no shutdown
     ip unnumbered [intf_a/b/c]
     no ip redirects
     tunnel source [intf_a/b/c]
     tunnel mode sdwan
    
    sdwan
     interface [intf_a/b/c]
      tunnel-interface
       encapsulation ipsec weight 1
       no border
       color [sdwan_color] restrict

#### Add Virtual Router

     request platform software sdwan vedge_cloud activate chassis-number [chassis_id] token [token]


## Verify Commands

#### QoS

    !-------------------------------------------------------------------
    ! QoS Verify Commands
    !-------------------------------------------------------------------
    !
    show sdwan policy access-list-associations
    show sdwan policy access-list-counters
    show sdwan policy access-list-names
    show sdwan policy access-list-policers
    show sdwan policy data-policy-filter
    show sdwan policy rewrite-associations
    show policy-map interface GigabitEthernet0/0/2 out | inc Clas|packet
    !
    !
    !-------------------------------------------------------------------
    ! Per Tunnel QoS Verify Commands
    !-------------------------------------------------------------------
    !
    show platform software sdwan qos template
    show platform software sdwan qos policy
    show platform software sdwan qos target
    show policy-map interface GigabitEthernet 0/0/1
    !
    
#### ZBF

     show sdwan zbfw zonepair-statistics
     show platform hardware qfp active feature firewall zonepair 1
     show platform software firewall fp active pairs
     show platform software firewall fp active vpn-bindings
     show platform software firewall fp active zones
     show platform hardware qfp active feature firewall datapath zonepair 1 1
      
     show platform hardware qfp active feature firewall memory
     show object-group
     show sdwan zonebfwdp sessions
     show sdwan zbfw drop-statistics
      
     show platform software firewall fp active sessions zone-pair ZONE_PAIR_NAME
     show platform software firewall fp active statistics
      
     show platform software object-manager fp active pending-issue-update
     show platform software object-manager fp active error-object
     show platform software object-manager fp active pending-ackupdate
     show platform software object-manager fp active statistics
     
     show platform hardware qfp active feature firewall zonepair 1
     show plat hard qfp act cl fe cl tcam cce 10913312 zone-pair 1 brief
     
     show platform  hardware qfp active classification class-group-manager class-group client cce all
     show platform  hardware qfp active classification class-group-manager class-group client cce name RULE NAME
     show platform  hardware qfp active classification feature-manager statistics
     
     show platform software access-list F0 summary
     show platform hardware qfp active feature acl control 
     
     show platform hardware qfp active feature sdwan datapath cloudexpress stats
    
## Troubleshooting

#### Change Management Mode

    unhide viptela_internal
    !
    no system is-vmanaged

#### Fix C8300 TenGig Port

    !-------------------------------------------------------------------
    ! Request Token for System Shell
    !-------------------------------------------------------------------
    !
    request consent-token generate-challenge shell-access auth-timeout 120
    !
    !
    !-------------------------------------------------------------------
    ! Provide Token for System Shell
    !-------------------------------------------------------------------
    !
    request consent-token accept-response shell-access [token]
    !
    !
    !-------------------------------------------------------------------
    ! Request Access for System Shell
    !-------------------------------------------------------------------
    !
    request platform software system shell
    !
    !
    !-------------------------------------------------------------------
    ! This will move us to the normal router prompt
    !-------------------------------------------------------------------
    !
    confd_cli -P 3010 -C -noaaa -g sdwan-oper
    !
    !
    !-------------------------------------------------------------------
    ! Disable CDB
    !-------------------------------------------------------------------
    !
    cdb-maintenance recovery-method ios-processing-disabled
    !
    !
    !-------------------------------------------------------------------
    ! Then Apply the Config Below
    !-------------------------------------------------------------------
    !
    config
    !
    interface TenGigabitEthernet0/0/4
     no negotiation
    !
    interface TenGigabitEthernet0/0/5
     no negotiation
    ! 
    commit
    !
    end
    !
    !
    !-------------------------------------------------------------------
    ! Get Back to the Router Prompt
    !-------------------------------------------------------------------
    !
    confd_cli -P 3010 -C -noaaa -g sdwan-oper
    !
    !
    !-------------------------------------------------------------------
    ! Enable CDB
    !-------------------------------------------------------------------
    ! 
    cdb-maintenance recovery-method normal-operation


#### Transfer Files Between vManage and cEdge

    !-------------------------------------------------------------------
    ! Transfer from cEdge to vManage
    !-------------------------------------------------------------------
    !
    request execute vpn 0 scp -P 830 admin@[system_ip]:/bootflash/ciscosdwan.txt /home/admin
    !
    !
    !-------------------------------------------------------------------
    ! Transfer from vManage to cEdge
    !-------------------------------------------------------------------
    !
    request execute vpn 0 scp -P 830 /home/admin/ciscosdwan.txt admin@[system_ip]/bootflash/vmanage-admin

#### Debugs
    
    !-------------------------------------------------------------------
    ! Debug Packets Commands
    !-------------------------------------------------------------------
    !
    debug platform packet-trace packet 2048 circular fia-trace data-size 2048
    debug platform packet-trace copy packet both size 2048 L3
    debug platform condition ipv4 10.189.4.105/32 in
    debug plat cond start
    !
    debug plat cond stop
    !
    show platform packet-trace summary
    !
    debug platform packet-trace drop code 187
    debug platform packet-trace packet 16
    debug platform condition ipv4 both
    debug plat cond start
    !

