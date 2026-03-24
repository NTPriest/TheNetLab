# DHCP snopping

Let's start by separating the meaning of the two words "DHCP snooping":
- **DHCP** - we know what it is.
- **Snooping** - means *"quietly watch"* or *"monitor something"*. (depending on the context)

In our example "snooping", it literally means monitoring the flow of DHCP messages.

## Why ?

DHCP snooping **protects** the data plane and therefore the control plane against **rogue** or false **dhcp server broadcasting** based on trust on the [*trusted/untrusted*] port.

It also builds a **DHCP Snooping Binding Table**, which acts as a database to verify if a device is allowed to send specific queries.
If the traffic doesn't match the table/records, the IOS device simply drops it."

To understand the mechanism, we need to remember what messages the DHCP server and client send.
we will wignore typical words like [DHCP<something>]

## Not only DORA
**Server**:
- OFFER
- ACK
- NACK (if something goes wrong)


**Client**:
- DISCOVER
- REQUEST
- RELEASE
- DECLINE

## Validation Mechanism
DHCP Snooping validates messages based on the **Binding Table** and **Port Trust State**.
- **Trusted Ports**: No validation is performed. We assume the device here (server/uplink) is legitimate.
-  **Untrusted Ports**: Every DHCP message is scrutinized.

There are two primary validation methods depending on the message type:

### 1.Client to Server:

1.1 **DISCOVER** / **REQUEST**    

The switch checks if the **Source MAC address** (Layer 2) matches the **CHADDR** (Client Hardware Address) field inside the **DHCP payload** (Layer 7). If they don't match: It’s likely a spoofing attempt and will perform DROP action.

1.2 **RELEASE / DECLINE**

The switch checks if the packet **source IP address** and the receveing interface match the entry in DHCP snooping binding table.

In short: 
- Source IP Check: Does the IP in the packet match an active lease?
- Interface Match: Did this packet arrive on the exact same physical port (Interface) where the IP was originally assigned?

 If it match it will proceed forward, otherwise take DROP action.

### 2.Server to Client:

2.1 **OFFER** / **ACK** / **NACK**

The switch checks the port type. If any of these "Server-only" messages arrive on an Untrusted port:

 Action: The message is immediately blocked (DROP). This prevents Rogue DHCP Servers from sending rogue DHCP messages.

# Configuration:
[ In Configuration mode ] 


1.DHCP snooping is enabled by providing command:

```switch(config)#ip dhcp snooping```

2.Then you need to specify the vlan on which it will defend:

```
switch(config)#ip dhcp snooping vlan 10 
switch(config)#ip dhcp snooping vlan <number>
...
```
... and so on, this can be applied to multiple vlans as well.

***Note, that all ports are automatically set to untrusted**.


3.To set specific port to be **trusted** provide:
```
switch(config)# interface <port>
switch(config-if)# ip dhcp snooping trust
```

The device that **acts as** a DHCP server must have the port set to **trusted** - failure to do so will result in errdisable.


