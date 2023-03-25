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
    
    
## Troubleshooting

#### Change Management Mode

    unhide viptela_internal
    !
    no system is-vmanaged

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

