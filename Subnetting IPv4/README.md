# IPv4 Subnetting

Well, everybody knows that a real Network [insert any technical title here] has to know how to do subnetting and IP allocations.  

**Why?**  
Because IPv4 is an addressing system for communication between networks and hosts — like a street name and number.  
We also have a subnet mask, which you can imagine as different neighborhoods that help packets find the right recipient.  

Without it, the city would be chaos.  
In networking terms, layer 4 simply wouldn’t exist — the router wouldn’t know where to deliver packets to a specific network or device.  
Even worse, the whole TCP/IP and ISO/OSI models (the latter being more of a theoretical model) would get stuck at that layer.  
The only communication possible would be within a LAN, without any routing.  
Sounds kind of "disabled-friendly," doesn’t it?  

By 2025, IPv4 is pretty much exhausted, and corporations have slowly started migrating to IPv6 — or at least using **Dual Stack** and **NAT64**.  
But for now, let’s focus on learning how to subnet IPv4.

---

## First of all — in what format do we describe IPv4?

We describe IPv4 — and every IP, really — in **bits**.  
Bits are `1`s and `0`s — in other terms, "yes" or "no/nothing."  
We use them because computers operate on bits.  
After that, we can convert them to decimal or hexadecimal, but at the lowest level, a PC always uses bits.

**Example:**

```192.168.1.0```

In binary it would be:
```11000000.10101000.00000001.00000000```
---

## Explanation

Each portion of 1s and 0s separated by a dot (`.`) is called an **octet**.  
Why "octet"? Because "octo" in Greek means *eight* — which makes sense, since each octet contains eight bits (whether 1s or 0s).

Every bit represents a **value**:

```128 64 32 16 8 4 2 1```

Each of these values is a power of 2:
```
2^0 = 1
2^1 = 2
2^2 = 4
2^3 = 8
2^4 = 16
2^5 = 32
2^6 = 64
2^7 = 128
```

How do we know which bit is which?  
Because **x** in `2^x` is the bit position, starting from 0 (**righmost**) to 8 (**leftmost**).

So:

```
bit 8 = 128
bit 7 = 64
bit 6 = 32
bit 5 = 16
bit 4 = 8
bit 3 = 4
bit 2 = 2
bit 1 = 1
```

```

|  11000000 | 10101000 | 00000001 | 00000000 |
|-----------|-----------|-----------|-----------|
|   192     |   168     |    1      |     0     |
-------------------------------------------------


```

## Putting it all together

We have **4 octets** (or **4 bytes**).  
`8 * 4 = 32` -> meaning all IPv4 addresses are **32 bits long**.  
That’s also why the **maximum prefix/mask value** is `/32` (we’ll cover that later).

So:
192.168.1.0
is:
```11000000.10101000.00000001.00000000```


Now, sum up each octet according to the bits that are `1`:

- For the first octet (`11000000`):
128 + 64 = 192
- For the second (`10101000`):
128 + 32 + 8 = 168
- For the third (`00000001`):
1 = 1
- And the last (`00000000`):
0 = 0

So the full address is:
192.168.1.0

Now you know how to translate bits to decimal and vice versa.
Next, we’ll learn how subnet masks work — how to calculate them, find available hosts, and allocate networks using CIDR and VLSM.

# Subnet Mask
# Subnet Mask

A **subnet mask** is one of the most important parts of the subnetting process.  
It defines the **range of IP addresses** that belong to a given network.  

You can imagine it as a **local guide** in each neighborhood —  
they know exactly which IPs (houses) are part of their area.  
But if that guide receives a request for an address that belongs to a different neighborhood,  
they’ll have no idea where to go — because that IP belongs to a different subnet.

In technical terms, the subnet mask separates the **network portion** from the **host portion** of an IP address.  
That’s how routers know whether a packet should stay inside the local network or be sent to another one.

How looks like subnet mask?
ex 255.255.255.0
Why is like that?
To understand this, remember: in a subnet mask, every bit set to `1` (shown as `255` in decimal)
marks the **network portion** of the address, while every bit set to `0` marks the **host portion**.

the converting works the same as previouse example: converting binary to decimal and vice versa
You can’t have something like this: `192.255.255.0` as a subnet mask —  
the values in a mask must always decrease **from left to right** in a consistent binary pattern.  

For example, a mask like `168.192.255.0` doesn’t make sense —  
because subnet masks must have all `1`s on the left and all `0`s on the right in their binary form.  

You can only use masks that follow valid binary boundaries, such as:
`255.255.255.0`, `255.255.255.128`, `255.255.255.192`, `255.255.255.224`, and so on.  

Invalid examples would be things like `255.255.255.64` or `255.255.192.64` —  
because `64` (in binary `01000000`) breaks the rule of having continuous `1`s followed by continuous `0`s.

## Calculating the Network and Hosts with a Network ID

As you can see, subnetting isn’t that terrible after all.  
Now we just need to understand how to allocate **hosts** and define the **network itself** using a **Network ID**.  
I’m going to show you **two ways of calculating** — a **slow** and a **fast** one (the second one is mine, and it’s especially useful for **LAN networks**).

---

### Key points to remember:

- The formula for calculating **usable hosts** is:  
2^n - 2

Where **`n`** is the number of bits remaining in the **host portion**.  
The **`-2`** accounts for the two reserved addresses:  
- one for the **network ID** (all host bits set to 0)  
- and one for the **broadcast address** (all host bits set to 1).  

So, “usable hosts” are simply the addresses that can be **assigned to devices** within that subnet.

---

- The formula for calculating how many **subnets** can exist in a given network is:  
2^n

Where **`n`** is the number of bits borrowed from the host portion to create subnets.

---

### The “slow” method:
The slow method requires operating directly on **binary values** to calculate:
- the **first host**,  
- the **last host**,  
- the **broadcast address**, and  
- the **network ID**.  

This approach is great for larger networks such as **WAN** or **MAN**, where precision matters and subnet design is more complex.

---

Now, before we go deeper into examples, we need to talk about another key concept —  
the **IP address classes**.  
They are extremely important, and after that, we’ll return to the previous topic.

---

# IP Address Classes

There are **five IP classes** in total, but only **three** of them are commonly used —  
unless you’re a **network researcher** experimenting with special ranges.

---

## Class A
**Class A** is typically used for **WANs** — *Wide Area Networks*.  
That means massive, public networks (think: ISPs, telecoms, or global enterprises).  

The **classfull** address range for Class A is:
`0.0.0.0 – 126.0.0.0/8`


---

## Class B
**Class B** addresses are often used for **MANs** — *Metropolitan Area Networks*.  
They’re common for **corporate**, **government**, or **infrastructure-level** networks — something between a LAN and a WAN in scale.  

The **clasfull** range is:
`128.0.0.0 – 191.255.255.255`

Class B networks are mid-sized — big enough for organizations or campuses,  
but not as huge as global WANs.

---

## Class C
**Class C** is what we use in our **daily networking life** — the **LAN** (*Local Area Network*).  
LANs are always **behind a firewall** and are **non-routable on the Internet**.  
In other words, LANs stay **inside** local networks only — you can’t use a LAN IP to talk directly to another device on the public Internet.

The engineers from **IANA** realized early on that if everyone used only public IPs, the IPv4 address pool would be exhausted *fast as hell*.  
So they decided to reserve **private ranges** for local use.

On every LAN, many people can have the *same* local IPs — because these addresses are reused across different networks behind NAT.

The private ranges for Class C (and other LAN scopes) are:
### classfull range:
`192.168.0.0 – 223.255.255.255`

### reserved ranges:
- `192.168.0.0 – 192.168.255.255
- 172.16.0.0 – 172.31.255.255
- 10.0.0.0 – 10.255.255.255`


You can read more about these ranges in **RFC 1918** —  
an easy way to remember it: *World War I ended in 1918.*

https://www.rfc-editor.org/rfc/rfc1918

---

## Class D
**Class D** addresses are used for **multicast** traffic.  
Their range is:
`224.0.0.0 – 239.255.255.255`

Multicast means sending one data stream to **multiple devices at once** —  
instead of flooding everyone (like broadcast), it targets a **group of subscribed hosts**.

For example:  
- Streaming services  
- Routing protocols like **OSPF**, **EIGRP**, **RIP v2**  
- Real-time communication (VoIP, video)

---

## Class E
**Class E** is reserved for **experimental and research purposes**.  
Range:
240.0.0.0 – 255.255.255.255
You’ll almost never see these addresses in normal network configs —  
they’re not routable on the public Internet and are generally reserved for testing.

## Loopback Address (127.0.0.1)

You might have noticed something — there’s no **127.0.0.1** in the ranges we’ve listed before.  
That’s because this range is **reserved** for **loopback** communication.

In simple terms — it’s an IP that loops back to **your own device**.  
When you send a packet to 127.0.0.1, it never leaves your machine.  
The data goes straight through your **TCP/IP stack** and comes right back —  
it’s like talking to yourself, but on a network level.

The entire **127.0.0.0/8** block (not just .1) is reserved for loopback,  
but we usually use `127.0.0.1` as the standard host address.

Common use cases:
- Hosting local web apps (`localhost`)
- Running development serv
- Testing sockets or TCP/IP stack behavior
- Diagnosing network issues — try running:
```ping 127.0.0.1```

* 📝 Small note:*  
If pinging the loopback address fails, your **TCP/IP stack** is broken — not your NIC or router.

Loopback = an internal network inside your own OS.  
Nothing outside your device will ever see it.

* 📝 Additional notes:*

The first things we can discuss again — plus a few new ones — are:

- **LAN IP addresses** are *not routable* on the public Internet.  
  They only work within your local network (behind NAT or a firewall).

- You **can’t assign a multicast address** directly to an end device.  
  Instead, devices or applications can **join multicast groups** dynamically using protocols like **IGMP** (in IPv4) or **MLD** (in IPv6).  
  This allows them to receive traffic sent to a specific multicast address,  
  but their actual interface IP remains a normal **unicast** address.

- **Public IPs** are used for **WAN (Wide Area Network)** connections —  
  these are the addresses visible to the Internet.

- **Public IPs are usually dynamic.**  
  They change over time, or when you reboot or power off your modem/router.  
  Most home users get a dynamic public IP assigned by their ISP.

- You *can* obtain a **static public IP**, but it usually costs extra.  
  It’s mainly useful for hosting web services, game servers, or other externally accessible systems.  
  For normal users, it’s generally unnecessary — unless you’re running something public-facing.

- You can only communicate **using your WAN-facing address** when reaching a different external network — for example, when you ping a host on the Internet.  
It’s impossible to establish communication **from LAN to WAN** directly, unless you’re using **port forwarding** or **NAT translation** — but that’s a more advanced topic, which I’ll cover later in the “NAT Translating” section.

# BACKING UP!

So, we have our **LAN** IP and subnet network:  
```
IP: 192.168.1.0  
Mask: 255.255.255.0
```

Can we shorten this?  
Of course we can.

Instead of typing that long, worm-like decimal mask, we can just use **CIDR notation** — for example:  
```
192.168.1.0/24
```

The `/24` is called a **prefix** in **CIDR (Classless Inter-Domain Routing)** notation.  
It’s simply a *shortened subnet mask* — easier to read and faster to type.

It means that the subnet mask contains **24 ones (`1`)** in its binary form, and the remaining bits are zeros (`0`):  
```
11111111.11111111.11111111.00000000
```

**Reminder:**  
- `1` → network bits  
- `0` → host bits  

---

### How many hosts and subnets can we assign to this network?

If you remember, the formulas are:
```
Usable hosts:  2^n - 2
Total subnets: 2^n
```

Here we can assign **254 usable hosts** (full range is 256 because `2^8 = 256`,  
but we always reserve two IPs — one for **network ID** and one for **broadcast**).  

We have **8 free bits** (zeros in the mask), which represent host addresses.

Now, how many subnets do we have here?  
Well, it depends.  
You’ve probably heard of **VLSM** and **CIDR**.

- **CIDR** → that `/number` prefix; just a shorter representation of the subnet mask.  
- **VLSM** (Variable Length Subnet Mask) → allows you to create *different-sized subnets* as needed — very useful for optimizing address usage.

---

### 📝 Note

Before **IANA** introduced CIDR and VLSM, the Internet used **classful addressing** — meaning you couldn’t split networks freely.  
You had only the ranges defined by **Class A/B/C**, which was a massive waste of address space.

Later, **VLSM** was introduced to fix this problem — it allows subdividing a single network into as many subnets as you want.  
Of course, each subnet is its **own isolated network**, with its **own broadcast** and **gateway IP**.

---

 *Pro tip:*  
Always think of subnetting like cutting a pizza. You decide how many slices you want — just remember, each slice is a **separate network**, not just a smaller part of the same one.

## Playing with Bits — Calculating Address Ranges, Slow method

There’s also a formula (or rather, a trick) that lets you **see directly from the bits** how many addresses are in a subnet.

Let’s take our network:
```
192.168.1.0  →  Network ID / Scope
Mask: 255.255.255.0
```

In binary, the mask looks like this:
```
11111111.11111111.11111111.00000000
```

Now, let’s perform a **reverse (bitwise NOT)** operation — meaning we flip every bit:  
replace `1`s with `0`s and `0`s with `1`s.

```
00000000.00000000.00000000.11111111
```

This binary pattern represents the **host portion**.  
If we add up the values of all the `1`s (128 + 64 + 32 + 16 + 8 + 4 + 2 + 1),  
we get **256** — meaning this subnet can hold **256 total addresses**.

Of course, as you already know, we subtract 2 (one for **network ID**, one for **broadcast**) →  
that gives **254 usable hosts**.

---

## Calculating the First and Last Hosts

The **first usable host** is always **+1** from the network ID.  
The **last usable host** is always **-1** from the broadcast address.

So, for our `/24` network:
```
Network ID:   192.168.1.0
First Host:   192.168.1.1
Last Host:    192.168.1.254
Broadcast:    192.168.1.255
```

That’s your complete **address scope** for a `/24` subnet.

---

*Tip:*  
If you want to do this mentally, remember:
- The number of host bits = number of zeros in mask  
- Hosts = 2^`n`  
- Usable = (2^`n`) - 2  
- Each increment in the last subnet octet = size of the subnet block

## Fast method
If you fell confidence enough Im going to give you fast way - my way BUT you have to remember a little from that chart:
Hosts/Subnets in network CIDR:
/24 H:**254** S:[**1**]
/25 H:**128** S:[**2**]
/26 H:**64**  S:[**4**]
/27 H:**32**  S:[**8**]
/28 H:**16**  S:[**16**]
/29 H:**8**   S:[**32**]
/30 H:**4**   S:[**64**] Router-to-router links
/31 H:**2**   S:[**128**] P2P links (RFC 3021)*
/32 H:**1**   S:[**254**] not usefull, loopback or static route
`/31` and `/32` are *special*: `/31` is used for point-to-point router links, `/32` for a single IP (loopback or static route).

As you can see, the numbers rise and fall in a proportional pattern as the subnet size changes.
When the number of hosts goes down, the number of subnets goes up — pretty neat, isn’t it?
I sometimes even count it on my fingers — like, the fourth finger is /24, the fifth is /25, and so on.

## Real Calculation Example

we have network `192.168.2.29` and our boss wants **4 subnets** from this.  
why those non-zero hosts at the end?  
because many tutorials and tech people like to overcomplicate things —  
but calm down, it's **easy as hell**.

let's remind our **CIDR chart** again.  
we know:
- we want **4 subnets**
- that means `/26` prefix
- `/26` = **64 hosts** (62 usable)

our given address `192.168.2.29` already sits *inside* one of those subnets,  
so we **don’t** start counting from `.29` — it’s not a network boundary.

instead, find the **valid block start** — that means we check the subnet step size.  
for `/26`, the block size is **64** in the last octet.

so subnet ranges go like this:

192.168.2.0 - 192.168.2.63
192.168.2.64 - 192.168.2.127
192.168.2.128 - 192.168.2.191
192.168.2.192 - 192.168.2.255


so `192.168.2.29` falls inside the first subnet (0–63 range).  
that means:
- **Network ID:** `192.168.2.0`  
- **First usable:** `192.168.2.1` — just add +1, that’s your first host  
- **Last usable:** `192.168.2.62` — (total hosts - 2 = 62 usable)  
- **Broadcast:** `192.168.2.63` — (last address in the range)

### why 62 and not 63 for the last host?

because we always reserve **2 addresses** in every subnet:  
one for the **network ID** (the very first, `.0`)  
and one for the **broadcast** (the very last, `.63` here).  

so when you see the formula `2^n - 2`,  
that “-2” literally means: *subtract network and broadcast addresses.*  

-> that’s why your last usable is **62**, not 63.  
easy, right?

### Next Example

we have `192.168.1.36` and we want to divide that network into `/27`.

Here we go again...  
as you remember — if the **host number** (from our CIDR chart) doesn’t fit nicely into the current host bits,  
you just **round it down to the nearest valid subnet boundary** — basically, zero out the host bits.

now, if the number *does* fit, you just calculate the **nearest subnet range** it belongs to.

**Simple rule:**  
Find where your IP “lands” — in which block of subnets (0–31, 32–63, 64–95, etc.),  
then assign the correct **Network ID**, **First usable**, **Last usable**, and **Broadcast** addresses.

**Example:**
- **Network ID:** 192.168.2.32  
- **First usable:** 192.168.2.33 — add +1, that’s your first host  
- **Last usable:** 192.168.2.62 — total hosts - 2  
- **Broadcast:** 192.168.2.63 — last address in the range

**Breakdown:**  
- **Network ID:** 192.168.1.32  
- **Subnet size:** 32 addresses  
- **Next subnet** starts at: 32 + 32 = 64 → 192.168.1.64  
- **Last usable host:** 32 + 32 - 2 = 62 → 192.168.1.62  
- **Broadcast:** 192.168.1.63  

**Key point:**  
You’re adding the subnet size to the **Network ID**, not the first host.  
That’s why the last host is always `(Network ID + Subnet size - 2)`.

## Summary (LAN calculation)

Very well, now you know how to subnetting internetwork by yourself, if you have trouble with that don't worry just review material again - and practice

**Links for practice***
https://subnetting.org/
https://subnetipv4.com/

## Calculating address with Class B

As we know subnetting LAN is easy, but we going to do something more complex, subnetting within Class B
We have to take different approach to calculate those networks - or use ip calculate.

Example:
- 16.103.217.41/19

**If you don’t know the CIDR chart and the 7 common masks:**

`if you dont know CIDR chart and 7 masks`:
**1.** Identify which block the IP belongs to:
- The IP is `16.103.217.41`.  
- Focus on the **third octet** (`217`) because /19 means the first **19 bits are network**, so the split happens in the third octet.

**2.** Subnet mask for /19:
- Binary: `255.255.224.0`  
- Host portion (**bitwise NOT**): `0.0.31*.255`  
  -> tells us the **block size is 32 addresses in the third octet**,  
     and **always round up to match the CIDR block size** (hosts).  

**3.** Calculate which /19 block contains 217:
- Blocks in the third octet (step = 32):  
  0, 32, 64, 96, 128, 160, 192, 224  
- `217` falls into the block starting at **192**.  
- So the **Network ID** = `16.103.192.0`.

**4.** Calculate hosts and broadcast:
- **First Host:** `16.103.192.1`  
- **Last Host:** `16.103.223.254` (Network ID + block size - 2)  
- **Broadcast:** `16.103.223.255`  

**5**Next Subnet:
- **Next Network ID:** `16.103.224.0` (add block size = 32 to third octet start)  

**Summary:**
- **Network ID:** 16.103.192.0  
- **First Host:** 16.103.192.1  
- **Last Host:** 16.103.223.254  
- **Broadcast:** 16.103.223.255  
- **Next Subnet:** 16.103.224.0

**If you already know the CIDR mask (or the “7 common masks”), you can skip the block calculations and just match the IP to the correct subnet directly.**

**Common masks (usable hosts, -2):**
- /25 -> 128 -> H: [126]
- /26 -> 192 -> H: [62]
- /27 -> 224 -> H: [30]
- /28 -> 240 -> H: [14]
- /29 -> 248 -> H: [6]
- /30 -> 252 -> H: [2]
- /31 -> 254 -> H: [1]
I typed useable hosts (-2)

Congrats you know how subnetting LAN, and more than that. 



