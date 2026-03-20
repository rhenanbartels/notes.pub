# Log network traffic

```
# ip route | grep default -> ens4
sudo tcpdump -i ens4 -n -w <file.pcap>
```


# Total network traffic of an IP address

```
sudo tcpdump -r <file.pcap> -n -q | grep <ip> | awk '{sum += $NF} END {print "Total Gb", sum/1024/1024/1024}'
```
