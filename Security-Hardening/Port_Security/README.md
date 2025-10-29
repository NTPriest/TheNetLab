# **Port Security**

Have you ever wondered how to secure your switch ports?  
Well, if not, that's bad, ngl... BUT, if yes - excellent! Now we can talk about **Port Security**.

## What is Port Security?

Port Security, as the name suggests, is a feature that helps protect your switch ports from unauthorized devices. It **restricts** which devices can connect to a given port by **binding** specific MAC addresses to that port.

With **Port Security**, you can control and limit the devices that can access your network through a **switch port**, which significantly improves security by preventing unauthorized devices from gaining access.

### Key Features of Port Security:

**MAC Address Binding**: Only devices with specific MAC addresses can be connected to a port. - if not, switch will trigger violation rules
**Violation Actions**: When an unauthorized device tries to connect, you can set the action the switch should take: 
- **Shutdown**: The port will be shut down and placed into an error-disabled state. You must manually bring the port back up using ```shutdown``` and ```no shutdown``` commands.
- **Restrict**: The switch will block the traffic from the unauthorized device but keep the port active. The port will log the violation, and only valid traffic will pass through.
- **Protect**: The switch will drop packets from unauthorized devices, but the port will remain active. No logging will occur, and the port will not be disabled. - less secure

**Maximum Device Limit**: You can limit the number of devices (MAC addresses) that can connect to a port.

## How to Configure Port Security?

Here’s a simple example of how to enable Port Security on a Cisco switch:

```
Switch# configure terminal
Switch(config)# interface gigabitEthernet 0/1
Switch(config-if)# switchport port-security                    # Enable port security on the interface
Switch(config-if)# switchport port-security maximum 2          # Limit the port to 2 MAC addresses - or more (devices)
Switch(config-if)# switchport port-security violation <shutdown|restrict|protect>  # Define the action on violation
Switch(config-if)# switchport port-security mac-address sticky  # "Sticky" MAC addresses will be learned and saved ("stick port to mac")
Switch(config-if)# end
```
If an unauthorized device tries to connect, the switch will trigger the violation action configured for that port.

## Common mistakes:
If you plug an RJ45 cable into a port and it turns amber, it doesn’t necessarily mean something’s broken — it just indicates that Port Security is enabled on that interface.

