---
title: "Monitor Traffic using Suricata"
weight: 20
pre: "<b>1. </b>"
draft: false
---

[Create Infrastructure](/010_prerequisites/createinfra) creates 3 Amazon EC2 instances, they serve following purpose:

1. Client instance:
  * Using **curl**, we will send port 80 traffic from client to server
2. Server instance:
  * It is running web server and returns a basic hello html page. It will respond to client instances's curl request
  * This is also acting as a source. We are going to mirror port 80 traffic ingressing on the server.
3. Destination instance:
  * Suricata is installed and configured to listen on the interface
  * We will configure Suricata rule for http and analyze the log

**Let's begin by opening two terminals side by side:**
![sideBySideTerminal](/images/sideBySideTerminal.png)

{{% notice warning %}}
We will use ssh to access EC2 instances. Along with public ip, you will need ssh key-pair to access EC2 instances. If you are running at an **AWS hosted event**, download the keys by following instructions [here](/010_prerequisites/aws_event/portal). If you are running it through your **own Account**, you use own ssh key-pair
{{% /notice %}}


1. In one terminal, access destination instance.
  * Depending on the region where you have [created infrastructure](/010_prerequisites/createinfra), find out public ip of the destination instance:
    * {{% button href="https://us-east-2.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:search=vpctm-ids-ec2;instanceState=running,stopped;sort=instanceId"icon="fas fa-external-link-alt" %}}Ohio{{% /button %}} {{% button href="https://us-west-2.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:search=vpctm-ids-ec2;instanceState=running,stopped;sort=instanceId"icon="fas fa-external-link-alt" %}}Oregon{{% /button %}} {{% button href="https://eu-west-1.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:search=vpctm-ids-ec2;instanceState=running,stopped;sort=instanceId"icon="fas fa-external-link-alt" %}}Ireland{{% /button %}}
    ![destinationInstancePublicIp](/images/destinationInstancePublicip.png)

  * SSH to destination instance, make sure to user appropriate ssh key-pair and public ip. Also make note of user **ubuntu** vs ec2-user:
  ```
  ssh -i ~/.ssh/ec2keypair.pem ubuntu@52.48.17.228
  ```
  ```
  Expected output:

  $: ssh -i ~/.ssh/ec2keypair.pem ubuntu@52.48.17.228
  The authenticity of host '52.48.17.228 (52.48.17.228)' can't be established.
  ECDSA key fingerprint is SHA256:UOySzeGo72MNKozS5RbjmT+HEE98cqERCyWAMJyUwrU.
  Are you sure you want to continue connecting (yes/no)? yes
  Warning: Permanently added '52.48.17.228' (ECDSA) to the list of known hosts.
  Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-1051-aws x86_64)

   * Documentation:  https://help.ubuntu.com
   * Management:     https://landscape.canonical.com
   * Support:        https://ubuntu.com/advantage

    System information as of Fri Nov 29 02:58:33 UTC 2019

    System load:  0.0               Processes:           89
    Usage of /:   16.6% of 7.69GB   Users logged in:     0
    Memory usage: 4%                IP address for ens5: 10.0.1.20
    Swap usage:   0%

  57 packages can be updated.
  37 updates are security updates.



  The programs included with the Ubuntu system are free software;
  the exact distribution terms for each program are described in the
  individual files in /usr/share/doc/*/copyright.

  Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
  applicable law.

  To run a command as administrator (user "root"), use "sudo <command>".
  See "man sudo_root" for details.

  ubuntu@ip-10-0-1-20:~$

  ```

{{% notice warning %}}
Step 2 through 11 should be performed on destination instance:
{{% /notice %}}

2. Check Suricata is installed:
```
sudo suricata build-info |head -1
```
```
Expected output:

ubuntu@ip-10-0-1-20:~$ sudo suricata build-info |head -1
Suricata 5.0.0
ubuntu@ip-10-0-1-20:~$
```

3. Check state of Suricata service, it won't be running:
```
sudo systemctl status suricata
```
```
Expected output:

ubuntu@ip-10-0-1-20:~$ sudo systemctl status suricata
● suricata.service - LSB: Next Generation IDS/IPS
   Loaded: loaded (/etc/init.d/suricata; generated)
   Active: inactive (dead) since Fri 2019-11-29 09:10:46 UTC; 5s ago
     Docs: man:systemd-sysv-generator(8)
  Process: 11517 ExecStop=/etc/init.d/suricata stop (code=exited, status=0/SUCCESS)
  Process: 11487 ExecStart=/etc/init.d/suricata start (code=exited, status=0/SUCCESS)

Nov 29 09:10:29 ip-10-0-1-20 systemd[1]: Starting LSB: Next Generation IDS/IPS...
Nov 29 09:10:29 ip-10-0-1-20 suricata[11487]: Starting suricata in IDS (af-packet) mode... d
Nov 29 09:10:29 ip-10-0-1-20 systemd[1]: Started LSB: Next Generation IDS/IPS.
Nov 29 09:10:38 ip-10-0-1-20 systemd[1]: Stopping LSB: Next Generation IDS/IPS...
Nov 29 09:10:46 ip-10-0-1-20 suricata[11517]: Stopping suricata: Waiting . . . .  done.
Nov 29 09:10:46 ip-10-0-1-20 systemd[1]: Stopped LSB: Next Generation IDS/IPS.
ubuntu@ip-10-0-1-20:~$
```

4. Check which interface Suricata should be running on:
```
ifconfig
```
```
Expected output:

ubuntu@ip-10-0-1-20:~$ ifconfig
ens5: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 10.0.1.20  netmask 255.255.255.0  broadcast 10.0.1.255
        inet6 fe80::41f:f6ff:fee8:b78e  prefixlen 64  scopeid 0x20<link>
        ether 06:1f:f6:e8:b7:8e  txqueuelen 1000  (Ethernet)
        RX packets 82092  bytes 88455441 (88.4 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 32847  bytes 7063566 (7.0 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 5610  bytes 536438 (536.4 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5610  bytes 536438 (536.4 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ubuntu@ip-10-0-1-20:~$
```

5. Check Suricata is configured to run on interface - ens5 (based on the output above). Information is stored in suricata config (suricata.yml) located /etc/suricata:
```
sudo cat /etc/suricata/suricata.yaml |head -581 |tail -4
```
```
Expected output:

ubuntu@ip-10-0-1-20:~$ sudo cat /etc/suricata/suricata.yaml |head -581 |tail -4
# Linux high speed capture support
af-packet:
  - interface: ens5
    # Number of receive threads. "auto" uses the number of cores
ubuntu@ip-10-0-1-20:~$
```

6. By default HOME_NET variable is configured for RFC1918 address space: [192.168.0.0/16,10.0.0.0/8,172.16.0.0/12], we can keep using that or further restrict by setting HOME_NET to address space of which our subnet is part of. For this activity, we will set it to 10.0.0.0/8. Open suricata.yml file. Under vars, search for line: HOME_NET: "[192.168.0.0/16,10.0.0.0/8,172.16.0.0/12]" and comment it out. Search for line #HOME_NET: "[10.0.0.0/8]" and uncomment it.
```
sudo vim /etc/suricata/suricata.yaml
```
```
Expected output:

%YAML 1.1
---

# Suricata configuration file. In addition to the comments describing all
# options in this file, full documentation can be found at:
# https://suricata.readthedocs.io/en/latest/configuration/suricata-yaml.html

##
## Step 1: inform Suricata about your network
##

vars:
  # more specific is better for alert accuracy and performance
  address-groups:
    HOME_NET: "[192.168.0.0/16,10.0.0.0/8,172.16.0.0/12]"
    #HOME_NET: "[192.168.0.0/16]"
    #HOME_NET: "[10.0.0.0/8]"
    #HOME_NET: "[172.16.0.0/12]"
    #HOME_NET: "any"

    EXTERNAL_NET: "!$HOME_NET"
    #EXTERNAL_NET: "any"

    HTTP_SERVERS: "$HOME_NET"
```

7. Update Suricata signature, this will place rules in /var/lib/suricata/rules:
```
sudo suricata-update
```
```
Expected output:

ubuntu@ip-10-0-1-20:~$ sudo suricata-update
29/11/2019 -- 09:41:26 - <Info> -- Using data-directory /var/lib/suricata.
29/11/2019 -- 09:41:26 - <Info> -- Using Suricata configuration /etc/suricata/suricata.yaml
29/11/2019 -- 09:41:26 - <Info> -- Using /etc/suricata/rules for Suricata provided rules.
29/11/2019 -- 09:41:26 - <Info> -- Found Suricata version 5.0.0 at /usr/bin/suricata.
29/11/2019 -- 09:41:26 - <Info> -- Loading /etc/suricata/suricata.yaml
29/11/2019 -- 09:41:26 - <Info> -- Disabling rules with proto modbus
29/11/2019 -- 09:41:26 - <Info> -- Disabling rules with proto enip
29/11/2019 -- 09:41:26 - <Info> -- Disabling rules with proto dnp3
29/11/2019 -- 09:41:26 - <Info> -- No sources configured, will use Emerging Threats Open
29/11/2019 -- 09:41:26 - <Info> -- Checking https://rules.emergingthreats.net/open/suricata-5.0.0/emerging.rules.tar.gz.md5.
29/11/2019 -- 09:41:26 - <Info> -- Remote checksum has not changed. Not fetching.
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/app-layer-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/decoder-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/dhcp-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/dnp3-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/dns-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/files.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/http-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/ipsec-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/kerberos-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/modbus-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/nfs-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/ntp-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/smb-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/smtp-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/stream-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Loading distribution rule file /etc/suricata/rules/tls-events.rules
29/11/2019 -- 09:41:26 - <Info> -- Ignoring file rules/emerging-deleted.rules
29/11/2019 -- 09:41:29 - <Info> -- Loaded 25984 rules.
29/11/2019 -- 09:41:30 - <Info> -- Disabled 14 rules.
29/11/2019 -- 09:41:30 - <Info> -- Enabled 0 rules.
29/11/2019 -- 09:41:30 - <Info> -- Modified 0 rules.
29/11/2019 -- 09:41:30 - <Info> -- Dropped 0 rules.
29/11/2019 -- 09:41:30 - <Info> -- Enabled 59 rules for flowbit dependencies.
29/11/2019 -- 09:41:30 - <Info> -- Backing up current rules.
29/11/2019 -- 09:41:33 - <Info> -- Writing rules to /var/lib/suricata/rules/suricata.rules: total: 25984; enabled: 20840; added: 0; removed 0; modified: 0
29/11/2019 -- 09:41:33 - <Info> -- No changes detected, exiting.
ubuntu@ip-10-0-1-20:~$
```

8. We are going to create our own HTTP rule and use with Suricata:
  * Create HTTP rule at /var/lib/suricata/rules/
  ```
  sudo -s

  echo 'alert tcp $HOME_NET any -> $HOME_NET 80 (msg:"Welcome to MKT210 Builder Session, HTTP traffic detect - ingressing Server"; sid:200001; rev:1;)' > /var/lib/suricata/rules/mkt210-custom-http.rules

  ls -lth /var/lib/suricata/rules/mkt210-custom-http.rules
  ```
  ```
  Expected output:

  ubuntu@ip-10-0-1-20:~$ sudo -s
  root@ip-10-0-1-20:~# echo "alert tcp $HOME_NET any -> $HOME_NET 80 (msg:"Welcome to MKT210 Builder Session, HTTP traffic detect - ingressing Server"; sid:200001; rev:1;)" > /var/lib/suricata/rules/mkt210-custom-http.rules
  root@ip-10-0-1-20:~# ls -lth /var/lib/suricata/rules/mkt210-custom-http.rules
  -rw-r--r-- 1 root root 123 Nov 29 10:23 /var/lib/suricata/rules/mkt210-custom-http.rules
  root@ip-10-0-1-20:~#
  ```

9. Verify rule was installed and start suricata:
```
cat /var/lib/suricata/rules/mkt210-custom-http.rules

systemctl start suricata

status suricata
```
```
Expected output:

root@ip-10-0-1-20:~# cat /var/lib/suricata/rules/mkt210-custom-http.rules
alert tcp $HOME_NET any -> $HOME_NET 80 (msg:"Welcome to MKT210 Builder Session, HTTP traffic detect - ingressing Server"; sid:200001; rev:1;)
root@ip-10-0-1-20:~# systemctl start suricata
root@ip-10-0-1-20:~# systemctl status suricata
● suricata.service - LSB: Next Generation IDS/IPS
   Loaded: loaded (/etc/init.d/suricata; generated)
   Active: active (running) since Fri 2019-11-29 10:27:55 UTC; 11s ago
     Docs: man:systemd-sysv-generator(8)
  Process: 12912 ExecStop=/etc/init.d/suricata stop (code=exited, status=0/SUCCESS)
  Process: 12945 ExecStart=/etc/init.d/suricata start (code=exited, status=0/SUCCESS)
    Tasks: 1 (limit: 4633)
   CGroup: /system.slice/suricata.service
           └─12962 /usr/bin/suricata -c /etc/suricata/suricata.yaml --pidfile /var/run/suric

Nov 29 10:27:55 ip-10-0-1-20 systemd[1]: Starting LSB: Next Generation IDS/IPS...
Nov 29 10:27:55 ip-10-0-1-20 suricata[12945]: Starting suricata in IDS (af-packet) mode... d
Nov 29 10:27:55 ip-10-0-1-20 systemd[1]: Started LSB: Next Generation IDS/IPS.
root@ip-10-0-1-20:~#
```

10. Rule should be loaded successfully you should see message similar to one below in /var/log/suricata/suricata.log:
```
cat /var/log/suricata/suricata.log |grep mkt210
```
```
Expected output - you should message like one below:

29/11/2019 -- 10:28:00 - <Config> - Loading rule file: /var/lib/suricata/rules/mkt210-custom-http.rules
```

11. Tail /var/log/suricata/fast.log to check for message that is being triggered through custom rule:

```
tail -f /var/log/suricata/fast.log
```

12. In another terminal, access client instance:
  * Depending on the region where you have [created infrastructure](/010_prerequisites/createinfra), find out public ip of the client instance:
    * {{% button href="https://us-east-2.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:search=vpctm-client-ec2;instanceState=running,stopped;sort=instanceId"icon="fas fa-external-link-alt" %}}Ohio{{% /button %}} {{% button href="https://us-west-2.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:search=vpctm-client-ec2;instanceState=running,stopped;sort=instanceId"icon="fas fa-external-link-alt" %}}Oregon{{% /button %}} {{% button href="https://eu-west-1.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:search=vpctm-client-ec2;instanceState=running,stopped;sort=instanceId"icon="fas fa-external-link-alt" %}}Ireland{{% /button %}}
    ![clientInstancePublicIp](/images/clientInstancePublicip.png)

  * SSH to client instance, make sure to user appropriate ssh key-pair and public ip:
  ```
  ssh -i ~/.ssh/ec2keypair.pem ec2-user@52.48.227.87
  ```
  ```
  Expected output:

  $: ssh -i ~/.ssh/ec2keypair.pem ec2-user@52.48.227.87
  The authenticity of host '52.48.227.87 (52.48.227.87)' can't be established.
  ECDSA key fingerprint is SHA256:APWvvya3TKwqD2RHcqrr4UVXBUQaPQfHOSQ3+1fy+sU.
  Are you sure you want to continue connecting (yes/no)? yes
  Warning: Permanently added '52.48.227.87' (ECDSA) to the list of known hosts.

         __|  __|_  )
         _|  (     /   Amazon Linux 2 AMI
        ___|\___|___|

  https://aws.amazon.com/amazon-linux-2/
  19 package(s) needed for security, out of 38 available
  Run "sudo yum update" to apply all updates.
  [ec2-user@ip-10-0-1-22 ~]$

  ```

13. Send port 80 traffic from client to server. You need to be on **client instance terminal** for this. This will trigger the rule on destination instance:
```
curl 10.0.1.21
```
```
Expected output:

[ec2-user@ip-10-0-1-22 ~]$ curl 10.0.1.21
<html>
    <head>
        <title>MKT210: Amazon VPC Traffic Mirroring Demo</title>
        <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    </head>
    <body>
        <h1>Welcome to MKT210: Amazon VPC Traffic Mirroring Demo</h1>
        <h2>This is a simple webserver running in eu-west-1</h2>
    </body>
</html>
[ec2-user@ip-10-0-1-22 ~]$

```

14. You will see message being triggered. You need to be on **destination instance terminal** for this:

```
Expected output for 'tail -f /var/log/suricata/fast.log'

11/29/2019-10:37:34.596041  [**] [1:200001:1] Welcome to MKT210 Builder Session, HTTP traffic detect - ingressing Server [**] [Classification: (null)] [Priority: 3] {TCP} 10.0.1.22:48318 -> 10.0.1.21:80

```
