/ip pool
add name=pppoe-active ranges=10.1.1.2-10.1.1.254
/ip pool
add name=pppoe-expired ranges=10.1.2.1-10.1.2.254

/interface pppoe-server server
add service-name=pppoe_service interface=ether2 disabled=no

/ppp profile
add name=pppoe-active local-address=10.1.1.1 remote-address=pppoe-active rate-limit=5M/5M
/ppp
add name=pppoe-expired local-address=10.1.1.1 remote-address=pppoe-expired

/ppp secret
add name=user1 password=123 profile=pppoe-active

/ip proxy
set enabled=yes port=8080

/ip firewall nat
add chain=dstnat src-address-list=isolir protocol=tcp dst-port=80 action=redirect to-ports=8080

/ip proxy access
add src-address=10.1.2.0/24 action=deny
