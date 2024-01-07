# Multi-Honeypot
Netsec - Honey Pot
![tpotsocial](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/08203f00-6ff7-4b90-ae72-cd212f64c4cf)
T-Pot is the all in one, optionally distributed, multiarch (amd64, arm64) honeypot plattform, supporting 20+ honeypots and countless visualization options using the Elastic Stack, animated live attack maps and lots of security tools to further improve the deception experience.


Running in a VM
T-Pot is reported to run with the following hypervisors, however not each and every combination is tested.

UTM (Intel & Apple Silicon)
VirtualBox
VMWare vSphere / ESXi
VMWare Fusion and VMWare Workstation
KVM is reported to work as well.


Running in a Cloud
T-Pot is tested on and known to run on ...

Telekom OTC using the post install method
Amazon AWS using the post install method (somehow limited)

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


First Start
Once the T-Pot Installer successfully finishes, the system will automatically reboot and you will be presented with the T-Pot login screen. Logins are according to the User Types:

user: [tsec or <os_username>]
pass: [password]
You can login from your browser and access Cockpit: https://<your.ip>:64294 or via SSH to access the command line: ssh -l [tsec,<os_username>] -p 64295 <your.ip>:

user: [tsec or <os_username>]
pass: [password]
You can also login from your browser and access the Nginx (T-Pot Web UI and tools): https://<your.ip>:64297

user: [<web_user>]
pass: [password]

Standalone First Start
There is not much to do except to login and check via dps.sh if all services and honeypots are starting up correctly and login to Kibana and / or Geoip Attack Map to monitor the attacks.


Distributed Deployment
With the distributed deployment firstly login to HIVE and the HIVE_SENSOR and check via dps.sh if all services and honeypots are starting up correctly. Once you have confirmed everything is working fine you need to deploy the HIVE_SENSOR to the HIVE in order to transmit honeypot logs to the Elastic Stack.


For deployment simply keep the HIVE login data ready and follow these steps while the deploy.sh script will setup the HIVE and HIVE_SENSOR for securely shipping and receiving logs:

sudo su -
deploy.sh
The script will ask for the HIVE login data, the HIVE IP address, will create SSH keys accordingly and deploy them securely over a SSH connection to the HIVE. On the HIVE machine a user with the HIVE_SENSOR hostname is created, belonging to a user group tpotlogs which may only open a SSH tunnel via port 64295 and transmit Logstash logs to port 127.0.0.1:64305, with no permission to login on a shell. You may review the config in /etc/ssh/sshd_config and the corresponding autossh settings in docker/elk/logstash/dist/entrypoint.sh. Settings and keys are stored in /data/elk/logstash and loaded as part of /opt/tpot/etc/tpot.yml.


Community Data Submission
T-Pot is provided in order to make it accessible to all interested in honeypots. By default, the captured data is submitted to a community backend. This community backend uses the data to feed Sicherheitstacho. You may opt out of the submission by removing the # Ewsposter service from /opt/tpot/etc/tpot.yml by following these steps:

Stop T-Pot services: systemctl stop tpot
Open tpot.yml: vi /opt/tpot/etc/tpot.yml
Remove the following lines, save and exit vi (:x!):
# Ewsposter service
  ewsposter:
    container_name: ewsposter
    restart: always
    networks:
     - ewsposter_local
    environment:
     - EWS_HPFEEDS_ENABLE=false
     - EWS_HPFEEDS_HOST=host
     - EWS_HPFEEDS_PORT=port
     - EWS_HPFEEDS_CHANNELS=channels
     - EWS_HPFEEDS_IDENT=user
     - EWS_HPFEEDS_SECRET=secret
     - EWS_HPFEEDS_TLSCERT=false
     - EWS_HPFEEDS_FORMAT=json
    env_file:
     - /opt/tpot/etc/compose/elk_environment
    image: "dtagdevsec/ewsposter:2203"
    volumes:
     - /data:/data
     - /data/ews/conf/ews.ip:/opt/ewsposter/ews.ip
Start T-Pot services: systemctl start tpot
It is encouraged not to disable the data submission as it is the main purpose of the community approach - as you all know sharing is caring 😍


Opt-In HPFEEDS Data Submission
As an Opt-In it is now possible to also share T-Pot data with 3rd party HPFEEDS brokers.
If you want to share your T-Pot data you simply have to register an account with a 3rd party broker with its own benefits towards the community. You simply run hpfeeds_optin.sh which will ask for your credentials. It will automatically update /opt/tpot/etc/tpot.yml to deliver events to your desired broker.

The script can accept a config file as an argument, e.g. ./hpfeeds_optin.sh --conf=hpfeeds.cfg

Your current config will also be stored in /data/ews/conf/hpfeeds.cfg where you can review or change it.
Be sure to apply any changes by running ./hpfeeds_optin.sh --conf=/data/ews/conf/hpfeeds.cfg.
No worries: Your old config gets backed up in /data/ews/conf/hpfeeds.cfg.old

Of course you can also rerun the hpfeeds_optin.sh script to change and apply your settings interactively.


Remote Access and Tools
T-Pot comes with some pre-installed services and tools which will make some of your research tasks or accessing T-Pot remote a lot easier.


SSH and Cockpit
According to the User Types you can login from your browser and access Cockpit: https://<your.ip>:64294 or via SSH to access the command line: ssh -l [tsec,<os_username>] -p 64295 <your.ip>:

user: [tsec or <os_username>]
pass: [password]
Especially if you do not have a SSH client at hand and still want to access the machine with a command line option you can do so by accessing Cockpit. You can also add two factor authentication to Cockpit just by running 2fa.sh on the command line.
![cockpit_a](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/47065e13-1f3e-4f30-aec0-7dc75722c70f)
![cockpit_b](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/25acea69-ace5-4ea6-b055-536c95d38ef2)
T-Pot Landing Page
According to the User Types you can open the T-Pot Landing Page from your browser via https://<your.ip>:64297:

user: [<web_user>]
pass: [password]
![tpotwebui](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/f43a6c01-7a16-459e-8ae9-6484a0bba71d)

Kibana Dashboard
On the T-Pot Landing Page just click on Kibana and you will be forwarded to Kibana. You can select from a large variety of dashboards and visualizations all tailored to the T-Pot supported honeypots.
![kibana_a](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/68884226-38e9-46bc-8a1b-3dbcdeeb59b3)

Attack Map
On the T-Pot Landing Page just click on Attack Map and you will be forwarded to the Attack Map. Since the Attack Map utilizes web sockets you need to re-enter the <web_user> credentials.
![attackmap](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/70f74fe0-43f8-4c85-9e3e-2fe97ae8a4a8)

Cyberchef
On the T-Pot Landing Page just click on Cyberchef and you will be forwarded to Cyberchef.
![cyberchef](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/88b66d2f-abb1-4fb6-951f-8b0511515be3)

Elasticvue
On the T-Pot Landing Page just click on Elastivue and you will be forwarded to Elastivue.
![elasticvue (1)](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/6ed1d350-7a48-47d8-b129-8d8ba5eeeba7)

Spiderfoot
On the T-Pot Landing Page just click on Spiderfoot and you will be forwarded to Spiderfoot.
![spiderfoot](https://github.com/Paul5070/Multi-Honeypot/assets/108010905/e238539c-c895-435b-8d22-641e25f544b2)



