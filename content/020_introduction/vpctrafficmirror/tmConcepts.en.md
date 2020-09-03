---
title: "Traffic Mirroring Concepts"
weight: 20
pre: "<b>1. </b>"
draft: false
---

#### Traffic Mirroring components:

![whatisamazonvpctm](/images/threeComponentsTm.png)


1. **Targets:** is the destination for a traffic mirror session.
  * The traffic mirror target can be an elastic network interface, or a Network Load Balancer. After you create a target, assign it to a traffic mirror session. A target can be used in more than one sessions.
  * You must configure a security group for the traffic mirror target that allows VXLAN traffic (UDP port 4789) from the source to the target.
  * You can share a traffic mirror target across accounts.

2. **Filters:** Define the traffic that is mirrored.
  * You use a traffic mirror filter and its rules to define the traffic that is mirrored. A traffic mirror filter contains one or more traffic mirror rules, and a set of network services.
  * You can define a set of parameters to apply to the traffic mirror source traffic to determine the traffic to mirror. The following traffic mirror filter rule parameters are available:
      * Traffic direction: Inbound or outbound
      * Action: The action to take, either to accept or reject the packet
      * Protocol: The L4 protocol
      * Source port range
      * Destination port range
      * Source CIDR block
      * Destination CIDR block

3. **Sessions:** Relationship between source and target
  * A traffic mirror session establishes a relationship between a traffic mirror source and a traffic mirror target. It contains the following resources:
      * A traffic mirror source
      * A traffic mirror target
      * A traffic mirror filter
  * Each Traffic Mirror source can support up to 3 sessions.
      * Session number determines the priority
      * Lowest ID given the highest priority
      * Packet mirrored only once
