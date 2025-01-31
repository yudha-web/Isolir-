/ip pool
add name=pppoe-active ranges=10.1.1.2-10.1.1.254

/ip pool
add name=pppoe-expired ranges=10.1.2.1-10.1.2.254

/interface pppoe-server server
add service-name=pppoe_server interface=ether2 disabled=no

/ppp profile
add name=pppoe-active local-address=10.1.1.1 remote-address=pppoe-active rate-limit=5M/5M

/ppp profile
add name=pppoe-expired local-address=10.1.1.1 remote-address=pppoe-expired address-list=pppoe-expired

/ppp secret
add name=user1 password=123 profile=pppoe-active service=pppoe

/ip proxy
set enabled=yes port=8080

/ip firewall nat
add chain=dstnat src-address-list=pppoe-expired protocol=tcp dst-port=80 action=redirect to-ports=8080

/ip proxy access
add src-address=10.1.2.0/24 action=deny

/system clock
set time-zone-name=Asia/Jakarta

/ip firewall filter
add chain=forward protocol=tcp dst-port=443 src-address-list=pppoe-expired action=drop

/system scheduler
add name=user1 start-time=00:00:00 on-event="
/ppp secret set user1 profile=pppoe-expired; 
/ppp active remove [find name=user1]; 
/interface pppoe-client remove [find name=pppoe-user1];"
