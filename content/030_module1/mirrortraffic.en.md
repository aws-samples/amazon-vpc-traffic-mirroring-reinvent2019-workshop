---
title: "Mirror Traffic"
weight: 24
pre: "<b>4. </b>"
draft: false
---

[Create Infrastructure](/010_prerequisites/createinfra) creates 3 Amazon EC2 instances, they serve following purpose:

1. Client instance:
  * Using **curl**, we will send port 80 traffic from client to server
2. Server instance:
  * It is running web server and returns a basic hello html page. It will respond to client instances's curl request
  * This is also acting as a source. We are going to mirror port 80 traffic ingressing on the server.
3. Destination instance:
  * Mirrored traffic is send to this instance

**Let's begin by opening two terminals side by side:**
![sideBySideTerminal](/images/sideBySideTerminal.png)

{{% notice warning %}}
We will use ssh to access EC2 instances. Along with public ip, you will need ssh key-pair to access EC2 instances. If you are running at an **AWS hosted event**, download the keys by following instructions [here](/010_prerequisites/aws_event/portal). If you are running it through your **own Account**, you use own ssh key-pair
{{% /notice %}}

1. Access client instance:
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

2. Access destination instance.
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

3. Start capturing traffic on destination instance. You need to be on **destination instance terminal** for this:

```
sudo tcpdump -nnni ens5 udp port 4789
```
```
Expected output:

ubuntu@ip-10-0-1-20:~$ sudo tcpdump -nnni ens5 udp port 4789
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on ens5, link-type EN10MB (Ethernet), capture size 262144 bytes


```

4. Send port 80 traffic from client to server. You need to be on **client instance terminal** for this:
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

5. You will see VXLAN packets being capture/mirrored on destination instance. You need to be on **destination instance terminal** for this:

```
Expected output:

ubuntu@ip-10-0-1-20:~$ sudo tcpdump -nnni ens5 udp port 4789
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on ens5, link-type EN10MB (Ethernet), capture size 262144 bytes
08:28:10.651515 IP 10.0.1.21.65506 > 10.0.1.20.4789: VXLAN, flags [I] (0x08), vni 2317549
IP 10.0.1.22.47404 > 10.0.1.21.80: Flags [S], seq 2845860947, win 26883, options [mss 8961,sackOK,TS val 664535282 ecr 0,nop,wscale 7], length 0
08:28:10.651769 IP 10.0.1.21.65506 > 10.0.1.20.4789: VXLAN, flags [I] (0x08), vni 2317549
IP 10.0.1.22.47404 > 10.0.1.21.80: Flags [.], ack 3091086770, win 211, options [nop,nop,TS val 664535282 ecr 639407793], length 0
08:28:10.651807 IP 10.0.1.21.65506 > 10.0.1.20.4789: VXLAN, flags [I] (0x08), vni 2317549
IP 10.0.1.22.47404 > 10.0.1.21.80: Flags [P.], seq 0:73, ack 1, win 211, options [nop,nop,TS val 664535282 ecr 639407793], length 73: HTTP: GET / HTTP/1.1
08:28:10.652260 IP 10.0.1.21.65506 > 10.0.1.20.4789: VXLAN, flags [I] (0x08), vni 2317549
IP 10.0.1.22.47404 > 10.0.1.21.80: Flags [.], ack 595, win 220, options [nop,nop,TS val 664535283 ecr 639407793], length 0
08:28:10.652370 IP 10.0.1.21.65506 > 10.0.1.20.4789: VXLAN, flags [I] (0x08), vni 2317549
IP 10.0.1.22.47404 > 10.0.1.21.80: Flags [F.], seq 73, ack 595, win 220, options [nop,nop,TS val 664535283 ecr 639407793], length 0
08:28:10.652479 IP 10.0.1.21.65506 > 10.0.1.20.4789: VXLAN, flags [I] (0x08), vni 2317549
IP 10.0.1.22.47404 > 10.0.1.21.80: Flags [.], ack 596, win 220, options [nop,nop,TS val 664535283 ecr 639407794], length 0

```

6. If we want to get rid of the VXLAN headers, we can create a VXLAN interface on to listen. You need to be on **destination instance terminal** for this. You can open a new one one, or use the existing one. Also note vxlan id in the command below should match vni id in the output above:
```
sudo ip link add vxlan100 type vxlan id 2317549 dstport 4789 local 10.0.1.20 dev ens5
sudo ip link set vxlan100 up
sudo tcpdump -nni vxlan100 port 80
```
```
Expected output:

ubuntu@ip-10-0-1-20:~$ sudo ip link add vxlan100 type vxlan id 2317549 dstport 4789 local 10.0.1.20 dev ens5
ubuntu@ip-10-0-1-20:~$ sudo ip link set vxlan100 up
ubuntu@ip-10-0-1-20:~$ sudo tcpdump -nni vxlan100 port 80
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on vxlan100, link-type EN10MB (Ethernet), capture size 262144 bytes


```

7. Send port 80 traffic from client to server. You need to be on **client instance terminal** for this:
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

8. You will now see port 80 traffic/packets being capture/mirrored on destination instance. You need to be on **destination instance terminal** for this:

```
Expected output:

ubuntu@ip-10-0-1-20:~$ sudo tcpdump -nni vxlan100 port 80
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on vxlan100, link-type EN10MB (Ethernet), capture size 262144 bytes
08:41:19.867485 IP 10.0.1.22.47498 > 10.0.1.21.80: Flags [S], seq 1695703862, win 26883, options [mss 8961,sackOK,TS val 665324494 ecr 0,nop,wscale 7], length 0
08:41:19.867500 IP 10.0.1.22.47498 > 10.0.1.21.80: Flags [.], ack 3902389973, win 211, options [nop,nop,TS val 665324494 ecr 640197004], length 0
08:41:19.867501 IP 10.0.1.22.47498 > 10.0.1.21.80: Flags [P.], seq 0:73, ack 1, win 211, options [nop,nop,TS val 665324494 ecr 640197004], length 73: HTTP: GET / HTTP/1.1
08:41:19.868034 IP 10.0.1.22.47498 > 10.0.1.21.80: Flags [.], ack 595, win 220, options [nop,nop,TS val 665324495 ecr 640197005], length 0
08:41:19.868148 IP 10.0.1.22.47498 > 10.0.1.21.80: Flags [F.], seq 73, ack 595, win 220, options [nop,nop,TS val 665324495 ecr 640197005], length 0
08:41:19.868209 IP 10.0.1.22.47498 > 10.0.1.21.80: Flags [.], ack 596, win 220, options [nop,nop,TS val 665324495 ecr 640197005], length 0

```
