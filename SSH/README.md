# Remote Access configuration on Cisco

You’ll often need to configure and maintain **remote access** to IOS devices.
One of the first things you’ll want to set up — after assigning a hostname — is SSH.

Now, here’s the catch:
Some Cisco switches and routers run newer IOS versions, while others are quite old.
So, where’s the problem?

When an IT guy (or you in your homelab) tries to connect to an **older** IOS device via SSH, you might see an error saying that the encryption algorithms are different — or that the client doesn’t support the older, less secure ciphers.
As long as you’re **not** exposing your lab network to the **Internet** (for example, by doing **port forwarding**), it’s totally fine to use older SSH ciphers or weaker encryption settings.
Just remember — this is only acceptable in a closed, isolated environment.
I’ll provide commands to **enable** (or at least accept) **older encryption algorithms** on the host side — I’ll explain this in detail at the end of this section.

## SSH

**1.** Enter privileged (enable) mode:
```
Switch>enable
Switch#
```

**2.** Enter global configuration mode:
```
Switch#conf t  # or full command 'configure terminal'
Switch(config)#
```

**3.** Set a hostname and domain name::
```
Switch(config)# hostname SW_Lab
SW_Lab(config)# ip domain name whatevername.com
```

**4.** Enable SSH
After key pair are generated you can enable SSH.
First you have to know what SSH version your IOS device support 
```
SW_Lab(config)# do show ip ssh
**SSH Enabled - version 1.99** [...]
```

Weird version, isn’t it?  
`Version 1.99` means that the IOS device supports both:
- **Version 1** — old and not secure  
- **Version 2** — newer and safe to use

Other possible outputs:
```
SSH Enabled - version 2.0
```
```
SSH Enabled - version 1.0
```

**5.** Change SSH version to the latest (recommended):
```
SW_Lab(config)# ip ssh version 2
```

* **Now check if the IOS successfully updated the version:**
```
SW_Lab(config)# do show ip ssh
SSH Enabled - **version 2.0**
Authentication methods: publickey, keyboard-interactive, password [...]
```

Yup, the version has been successfully updated.

Now the important part:

**6.** Configure user credentials
```
SW_Lab(config)# username <username> privilege 15 secret <password>
```
*The `secret` parameter ensures that your password is **hashed** instead of stored in plaintext in the running or startup config.*

**7.** Enable local login on VTY lines
```
SW_Lab(config)# line vty 0 15      	 	# all available VTY lines
SW_Lab(config-line)# login local    	 	# only accounts configured locally on the device will be used
SW_Lab(config-line)# transport input ssh 	#only allow SSH (no Telnet)
SW_Lab(config-line)# end
```

---
📝 **Note**: 
You must set a domain name — if you don’t, the next command will fail with:
`% Please define a domain-name first.`
It’s also a best practice to set a hostname, because the RSA key will be generated using this format:

`<hostname>.<domain-name>`

**4.** Generate the RSA key (this is required for SSH encryption)
```
SW_Lab(config)# crypto key generate rsa

Choose the size of the key modulus in the range of 512 to 4096 for your
 General Purpose Keys. Choosing a key modulus greater than 512 may take
 a few minutes.

How many bits in the modulus [1024]: 2048   # you can specify the key length
% Generating 2048 bit RSA keys, keys will be non-exportable...
[OK] (elapsed time was 3 seconds)
```

 ### *Optional, but considered *Best Practice*
After configuring SSH, also set ACL rules for that protocol.  

Applying an ACL to VTY lines is slightly different from assigning ACLs to interfaces.  
After creating an ACL with a specific name and rules, apply it to the VTY lines:

```
SW_Lab(config)# line vty 0 15
SW_Lab(config-line)# access-class <ACL_name> in
```

You can also configure session timeout:

```
SW_Lab(config)# line vty 0 15
SW_Lab(config-line)# exec-timeout <minutes> <seconds>  # e.g., exec-timeout 5 30
```

---
📝 **Notes**:  
- If you want the session to **never expire** (good for labs):  
```exec-timeout 0 0```
---
📝**Notes on RSA key length:**
You can choose the key size (modulus) when generating RSA keys:

**512-bit** - Outdated, not secure anymore. Use only for testing or old devices.

**1024-bit** - Still acceptable for homelabs or legacy IOS. Fast to generate.

**2048-bit** - Recommended balance between security and performance. Works great for most lab and production environments.

**4096-bit** - Strong encryption, but may slow down older devices during 
            key generation and SSH sessions.

For most lab setups, **2048-bit** is the best choice — secure enough, 
   fast enough, and won’t "burn" your switch/router’s CPU.
   



