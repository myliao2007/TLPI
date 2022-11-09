# netsnmp\_o4MIxRHA

## NetSNMP

## Porting

## Partial Build

```
sudo make package/net-snmp/clean
sudo make package/net-snmp/compile
sudo make package/net-snmp/install
```

## Note

```bash
aaron@aaron-Virtual-Machine:~/net-snmp-5.6.2.1$ sudo ./net-snmp-create-v3-user
Enter a SNMPv3 user name to create:
test
Enter authentication pass-phrase:
123456
Enter encryption pass-phrase:
[press return to reuse the authentication pass-phrase]
123456
adding the following line to /var/net-snmp/snmpd.conf:
createUser test MD5 "123456" DES 123456
adding the following line to /usr/local/share/snmp/snmpd.conf:
rwuser test
```

#### MIB browser

ireasoning mib browser

Verification

```
snmpwalk -v 1 -c public 127.0.0.1 system

snmpwalk -v3 -a MD5 -x DES -c public 127.0.0.1 system

snmpwalk -v3  -l authPriv -u aaron4 -a SHA -A 12345678 -x AES -X 12345678 127.0.0.1

snmpset -v1 -c public 192.168.192.70 IP-MIB::ipForwarding.0 i 0

snmpset -v 2c -c public 192.168.192.70 IP-MIB::ipForwarding.0 i 0

snmpget -v 2c -c public 192.168.192.70 IP-MIB::ipForwarding.0

snmpget -v3  -l authPriv -u aaron3 -a SHA -A 12345678  -x AES -X  12345678 192.168.192.70 IP-MIB::ipForwarding.0

snmpwalk -v3 -a SHA -A 12345678 -x AES -X 12345678 -u aaron3 -l authPriv 127.0.0.1

snmpget -v3  -l noauthnoPriv -u aaron  192.168.192.70 SNMPv2-MIB::sysLocation.0 

snmpget -v3  -l authnoPriv -u aaron1 -a MD5 -A 12345678 192.168.192.70 IP-MIB::ipForwarding.0

snmpget -v3 -e 0x80001F8803143FC300189E -l authPriv -u aaron4 -a SHA -A 12345678 -x AES -X  12345678 192.168.192.70 IP-MIB::ipForwarding.0
                 
snmpset -v3  -l authPriv -u aaron3 -a SHA -A 12345678  -x AES -X  12345678 192.168.192.70 IP-MIB::ipForwarding.0 i 1

snmpset -v3  -l authPriv -u aaron3 -a SHA -A 12345678 -x AES -X  12345678 192.168.192.70 SNMPv2-MIB::sysLocation.0 s hello

snmpset -v3  -l authPriv -u hihi -a SHA-512 -A '1234567890!!' -x AES -X '1234567890!!' 192.168.192.70 SNMPv2-MIB::sysLocation.0 s hello

```

Get Engine ID

snmpwalk -v 1 -c public 127.0.0.1 1.3.6.1.6.3.10.2.1.1.0

snmpd.conf

view    systemview    included   .1.3.6.1.2.1.1&#x20;

view    systemview    included   .1.3.6.1.2.1.25.1.1&#x20;

view all   included  .1&#x20;

![](https://t25627165.p.clickup-attachments.com/t25627165/42ff4b94-aeaa-4d98-bdd4-c3e04033a5c9/image.png)

**Workable configuration**

[snmpd-2022-1018](https://t25627165.p.clickup-attachments.com/t25627165/03b89528-b206-4e6d-b036-f5d0161f7837/snmpd-2022-1018)

**自動產生一個 netsnmp 組態檔**

```
sudo snmpconf -i -g basic_setup
```

## Reference

* [https://net-snmp.sourceforge.io/docs/man/snmpd.examples.html](https://net-snmp.sourceforge.io/docs/man/snmpd.examples.html)
* [https://github.com/redBorder/net-snmp/blob/master/configure.d/config\_project\_with\_enable](https://github.com/redBorder/net-snmp/blob/master/configure.d/config\_project\_with\_enable)
