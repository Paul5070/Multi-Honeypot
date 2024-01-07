T-Pot - The All In One Multi Honeypot Platform
![image](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/f76db101-78eb-4e57-8a31-39292701a97a)
T-Pot is the all in one, optionally distributed, multiarch (amd64, arm64) honeypot plattform, supporting 20+ honeypots and countless visualization options using the Elastic Stack, animated live attack maps and lots of security tools to further improve the deception experience.

Running in a VM
T-Pot is reported to run with the following hypervisors, however not each and every combination is tested.

UTM (Intel & Apple Silicon)
VirtualBox
VMWare vSphere / ESXi
VMWare Fusion and VMWare Workstation
KVM is reported to work as well.
Some configuration hints:

While Intel versions run stable, Apple Silicon (arm64) support for Debian has known issues which in UTM may require switching Display to Console Only during initial installation of T-Pot / Debian and afterwards back to Full Graphics.
During configuration you may need to enable promiscuous mode for the network interface in order for fatt, suricata and p0f to work properly.
If you want to use a wifi card as a primary NIC for T-Pot, please be aware that not all network interface drivers support all wireless cards. In VirtualBox e.g. you have to choose the "MT SERVER" model of the NIC.

Running on Hardware
T-Pot is tested on and known to run with ...

IntelNUC series (only some tested)
Some generic Intel hardware
Since the number of possible hardware combinations is too high to make general recommendations. If you are unsure, you should test the hardware with the T-Pot ISO image or use the post install method.


Running in a Cloud
T-Pot is tested on and known to run on ...

Telekom OTC using the post install method
Amazon AWS using the post install method (somehow limited)
Some users report working installations on other clouds and hosters, i.e. Azure and GCP. Hardware requirements may be different. If you are unsure you should research issues and discussions and run some functional tests. Cloud support is a community developed feature and hyperscalers are known to adjust linux images, so expect some necessary adjustments on your end.


Required Ports
Besides the ports generally needed by the OS, i.e. obtaining a DHCP lease, DNS, etc. T-Pot will require the following ports for incoming / outgoing connections. Review the T-Pot Architecture for a visual representation. Also some ports will show up as duplicates, which is fine since used in different editions.

Port	Protocol	Direction	Description
80, 443	tcp	outgoing	T-Pot Management: Install, Updates, Logs (i.e. Debian, GitHub, DockerHub, PyPi, Sicherheitstacho, etc.
64294	tcp	incoming	T-Pot Management: Access to Cockpit
64295	tcp	incoming	T-Pot Management: Access to SSH
64297	tcp	incoming	T-Pot Management Access to NGINX reverse proxy
5555	tcp	incoming	Honeypot: ADBHoney
5000	udp	incoming	Honeypot: CiscoASA
8443	tcp	incoming	Honeypot: CiscoASA
443	tcp	incoming	Honeypot: CitrixHoneypot
80, 102, 502, 1025, 2404, 10001, 44818, 47808, 50100	tcp	incoming	Honeypot: Conpot
161, 623	udp	incoming	Honeypot: Conpot
22, 23	tcp	incoming	Honeypot: Cowrie
19, 53, 123, 1900	udp	incoming	Honeypot: Ddospot
11112	tcp	incoming	Honeypot: Dicompot
21, 42, 135, 443, 445, 1433, 1723, 1883, 3306, 8081	tcp	incoming	Honeypot: Dionaea
69	udp	incoming	Honeypot: Dionaea
9200	tcp	incoming	Honeypot: Elasticpot
22	tcp	incoming	Honeypot: Endlessh
21, 22, 23, 25, 80, 110, 143, 443, 993, 995, 1080, 5432, 5900	tcp	incoming	Honeypot: Heralding
21, 22, 23, 25, 80, 110, 143, 389, 443, 445, 1080, 1433, 1521, 3306, 5432, 5900, 6379, 8080, 9200, 11211	tcp	incoming	Honeypot: qHoneypots
53, 123, 161	udp	incoming	Honeypot: qHoneypots
631	tcp	incoming	Honeypot: IPPHoney
80, 443, 8080, 9200, 25565	tcp	incoming	Honeypot: Log4Pot
25	tcp	incoming	Honeypot: Mailoney
2575	tcp	incoming	Honeypot: Medpot
6379	tcp	incoming	Honeypot: Redishoneypot
5060	udp	incoming	Honeypot: SentryPeer
80	tcp	incoming	Honeypot: Snare (Tanner)
Ports and availability of SaaS services may vary based on your geographical location. Also during the first install outgoing ICMP / TRACEROUTE is required additionally to find the closest and fastest mirror to you.

Remote Access and Tools
T-Pot comes with some pre-installed services and tools which will make some of your research tasks or accessing T-Pot remote a lot easier.


SSH and Cockpit
According to the User Types you can login from your browser and access Cockpit: https://<your.ip>:64294 or via SSH to access the command line: ssh -l [tsec,<os_username>] -p 64295 <your.ip>:

user: [tsec or <os_username>]
pass: [password]
Especially if you do not have a SSH client at hand and still want to access the machine with a command line option you can do so by accessing Cockpit. You can also add two factor authentication to Cockpit just by running 2fa.sh on the command line.
![cockpit_a](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/c66b1865-4d10-4449-a804-93584b98925e)
![cockpit_b](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/d1bfe4eb-b8bc-4d0d-ad5f-3d2d39b4e54f)

T-Pot Landing Page
According to the User Types you can open the T-Pot Landing Page from your browser via https://<your.ip>:64297:

user: [<web_user>]
pass: [password]
![tpotwebui](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/5ffbbddb-7252-4880-8814-71588c8b38a1)

Kibana Dashboard
On the T-Pot Landing Page just click on Kibana and you will be forwarded to Kibana. You can select from a large variety of dashboards and visualizations all tailored to the T-Pot supported honeypots.
![kibana_a](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/60dc4d14-66ce-44bf-830d-2380004ba761)

Attack Map
On the T-Pot Landing Page just click on Attack Map and you will be forwarded to the Attack Map. Since the Attack Map utilizes web sockets you need to re-enter the <web_user> credentials.
![attackmap](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/95078e01-1fb7-46d6-9a2c-b625451caac7)

Cyberchef
On the T-Pot Landing Page just click on Cyberchef and you will be forwarded to Cyberchef.
![cyberchef](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/4c455cc5-3567-48ee-b0e4-c42bc0ab02ac)

Elasticvue
On the T-Pot Landing Page just click on Elastivue and you will be forwarded to Elastivue.
![elasticvue](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/fdd367f1-6f77-415e-bec0-6183acaed39c)

Spiderfoot
On the T-Pot Landing Page just click on Spiderfoot and you will be forwarded to Spiderfoot.
![spiderfoot](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/a8831617-b3fb-4391-a8de-beb6a0a85b4e)

Log Persistence
All log data stored in the T-Pot Data Folder will be persisted for 30 days by default. The persistence for the log files can be changed in /opt/tpot/etc/logrotate/logrotate.conf.
Elasticsearch indices are handled by the tpot Index Lifecycle Policy which can be adjusted directly in Kibana. 
![kibana_b](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/ec0c3b34-5243-414e-bcb0-573009b35eb7)

By default the tpot Index Lifecycle Policy keeps the indices for 30 days. This offers a good balance between storage and speed. However you may adjust the policy to your needs. 
![kibana_c](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/e62d165f-2015-4fd8-ba23-1f5b4d27a321)



