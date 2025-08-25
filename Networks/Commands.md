# Commands

## Random

ping iitd.ac.in
wget iitd.ac.in
wget or curl can be used to download files from the internet

ping 10.42.83.158 #Pinging phone, was slower 74 vs 34 ms. Need to try in other places  
ping iitd.ac.in - 48.381ms in hostel

ifconfig: To see all info about all interfaces (Can use -a flag to see all interfaces)

ip addr: To see the IP address of all interfaces
ip link: To see the link layer info of all interfaces(MAC Address)

wlan0 - fronthaul connection  
wlan1 - fronthaul conneciton  

nmcli dev show enp0s20f0u4c2: To see info about hostel ethernet connection
iwlist wlp2s0: scanning neighbouring wireless access points #DOESNT WORK ON FEDORA
iwconfig: To see wireless interfaces #DOESNT WORK ON FEDORA

- dig iitd.ac.in: To see the IP address of the domain(DNS lookup utility)
- whois iitd.ac.in: To see who owns the domain(server that serves the website)
- dig <amazon.com> +trace: To see the path taken to reach the domain
- netstat -tuln: To see all the connections that are open
  - -t: Shows TCP connections
  - -u: Shows UDP connections
  - -l: Displays only listening sockets
  - -n: Shows numerical addresses and port numbers instead of hostnames
- ss -tuln: Same as above

wlp0s20f3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.42.81.51  netmask 255.255.252.0  broadcast 10.42.83.255
        inet6 fe80::17b:16aa:729a:be94  prefixlen 64  scopeid 0x20<link>
        ether ee:4c:3e:65:f5:02(Link level identifier)  txqueuelen 1000  (Ethernet)
        RX packets 173614  bytes 242253559 (231.0 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 21020  bytes 9101565 (8.6 MiB)
        TX errors 0  dropped 24 overruns 0  carrier 0  collisions 0

SYN packet means im trying to connect with you(synchronization)
ACK packet means i acknowledge your connection(acknowledgement)
PSH packet means push data to fill bandwidth pipe(actual data)
FIN packet means i am done with the connection(finish)
POST packet has the payload in the packet
ACK tried 3 times, then it gives up

har files to record network traffic. It only involves itself with the application layer

## Packet Capture Tools

### tcpdump

Doing tcpdump on Brave means nothing will be cached. Same for any incognito window

sudo tcpdump -i enp0s20f0u4c2 -w test.pcap: To see packets being sent and received, writing to a .pcap file(packet capture file)
tcpdump -i any: To see packets on all interfaces
tcpdump -i any -c 10: Capture first 10 packets and then exit
tcpdump -D: To see all interfaces
tcpdump -i any -A: To see packets in ASCII
tcpdump -n -i any: Dont resolve hostnames
tcpdump -n -I any: Monitor Mode, will disconnect from the network being used
tcpdump -i any port 8080: To see packets on port 8080
tcpdump host 10.22.168.8: To see packets from a particular host
tcpdump net 10.1.1.1/16: To see packets from a particular network subnet
tcpdump src 10.22.168.8: To see packets from a particular source
tcpdump dst 10.22.168.8: To see packets from a particular destination
tcpdump http: To see packets with HTTP protocol
tcpdump port 8080: Filter traffice based on service
tcpdump portrange 21-23: Filter traffic based on port range
tcpdump -S: To see actual sequence numbers of packets
tcpdump -IPV6: To see IPV6 packets
tcpdump -d test.pcap: To see the contents of a pcap file in human readable form
tcpdump -F filter.pcap: Use the given file as input for filtering
tcpdump -L: Display line types for filtering

### iperf3

- iperf3 can be used to measure bandwidth between two devices.
  - Only on TCP!! not UDP.
  - Used for saturated traffic

iperf -s: Sets up a server and listens for incoming connections analyzing the bandwidth, jitter, packet loss etc.
iperf3 -c 192.168.12.1 -t 5: Connects to the server at 192.168.12.1 and sends data for 5 seconds

### tc

- tc is a traffic shaping tool and can be used to simulate network conditions.
  - It is used to control the queuing, policing, scheduling and dropping of packets.
  - It is used to simulate network conditions like latency, bandwidth, packet loss etc.

Continuation of experiments. Using tc, we capped the maximum bandwidth of the RPi's Internet connection to 1Mbps and varied latency from 100ms to 500ms. For all such instances, we recorded the HAR for loading the TimesofIndia webpage.

(base) ayon@senselab:~$ sudo tc qdisc add dev wlan0 root tbf rate 1mbit burst 32kbit latency 500ms  
(base) ayon@senselab:~$ sudo tc qdisc change dev wlan0 root tbf rate 10mbit burst 32kbit latency 25ms  
(base) ayon@senselab:~$  
(base) ayon@senselab:~$ sudo tc qdisc add dev wlan0 root netem delay 100ms  
(base) ayon@senselab:~$ sudo tc qdisc change dev wlan0 root netem delay 75ms  
(base) ayon@senselab:~$  
(base) ayon@senselab:~$ sudo tc qdisc del dev wlan0 root  

tc qdisc add dev wlan0 root netem delay 100ms  
tc qdisc del dev wlan0 root  
tc qdisc add dev eth0 root tbf rate lmbit burst 32kbit latency 200ms loss 10%  

tc qdisc dev dev eth0 root  

ethtool  

sudo ping -f  

traceroute might give different paths each time its run  
star(*) means the router is not known. 3 stars means it tries 3 times  
try iptables masquerading  
Identify a loaded router  
ARP -s allows you to add entries to the ARP table  
ARP -d allows you to delete entries from the ARP table  
ARP -a allows you to view the ARP table. Hijacking packets
