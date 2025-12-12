# Vulnerabilities-in-a-Besder-Camera

## Device information
- **Brand:** besder(white-label OEM)
- **Control APP:**: O-KAM Pro
- **Firmware version:** 10.194.120.44
- **Platform:** Eye4 Cloud
- **Purchase link:** https://www.aliexpress.com/item/1005009508892646.html
![427f7287a108d980af7c725b310b8c2](https://github.com/user-attachments/assets/c06c2942-859c-4f82-bd51-4382b5bfb1b8)

## Summary of Vulnerabilities
- **Unauthenticated ONVIF Interface:**
The camera exposes its ONVIF interface without requiring any authentication.
This allows an attacker to enumerate device information, discover available services, and potentially control PTZ, stream settings, or other administrative functions.
Any user on the same network can interact with ONVIF endpoints without valid credentials, resulting in a complete loss of access control.
- **RTSP Unauthenticated Access:**
The device’s RTSP video stream is accessible without providing a username or password.
An attacker can directly retrieve live video footage simply by connecting to the RTSP URL, bypassing all authentication mechanisms.
This vulnerability leads to severe privacy leakage, as attackers can view real-time camera feeds without authorization.
- **TLS Certificate Verification Bypass**
The device accepts any certificate—including those signed by mitmproxy’s default CA—allowing attackers to intercept and proxy its encrypted traffic.
This improper certificate validation enables full man-in-the-middle attacks, allowing an attacker to decrypt, inspect, and modify all data transmitted between the device and the cloud service.
## Attack Reproduction
### 1.Port Scanning and Service Discovery
Discovering Open Ports (10080-ONVIF, 10554-RTSP) and Services Using `Nmap`; I have taken screenshots of the other scanned open ports below
<img width="839" height="409" alt="6422a63154781a8c88b00d46d86569e" src="https://github.com/user-attachments/assets/7f5ed256-6141-41ba-a29e-de6c1c499686" />

### 2.Unauthenticated ONVIF Interface and RTSP Stream Accessible Without Credentials
Using any ONVIF client tools (I am using Agent DVR), the device was scanned for ONVIF capabilities.
The ONVIF service accepted any username and password. Even completely random credentials worked. The client was able to retrieve device configurations and capabilities without authentication.
**This confirms the ONVIF interface does not enforce access control.**
Also, the ONVIF response disclosed the device’s RTSP streaming URL.

Unlike standard formats such as:

`rtsp://admin:admin@<ip>/live/ch0`

The camera returned a URL without credentials:

`rtsp://<ip>/xxx/xxx`

<img width="1595" height="1643" alt="1469269dd588f09e43b53800a7ff83b" src="https://github.com/user-attachments/assets/6230ded9-b3d5-43aa-87e4-d995dd301411" />

**Connecting to this RTSP endpoint provided full live video access without authentication.**
<img width="2877" height="1645" alt="298347851a3e95cf10a05785aeed142" src="https://github.com/user-attachments/assets/061d6da8-c860-40a7-bb92-7d7ce1ad0067" />

### 3.TLS MITM: Certificate Validation Bypass
**1.ARP Spoofing**
The testing machine positions itself between the target device and the network gateway by performing ARP spoofing.
By manipulating ARP cache entries on both sides, the attacker-controlled host becomes the intermediary for all network traffic exchanged between the two endpoints.

**2.Packet Forwarding Configuration**
Once the MITM position is established, the machine is configured to transparently forward packets.
This prevents service disruption and maintains normal communication between the target device and the gateway.

**3.Traffic Redirection for Local Inspection**
Selected traffic is internally redirected to a local service for analysis.

**4.Transparent Proxy Setup**
A transparent interception proxy is deployed using mitmproxy.
When HTTPS traffic is routed through the proxy, mitmproxy automatically creates a local, temporary Certificate Authority (CA) within the testing environment. For each intercepted HTTPS domain, it dynamically generates a domain-specific certificate signed by this fake CA, allowing the proxy to decrypt and inspect encrypted traffic only if the client trusts the generated CA.

**The result shows that mitmproxy has been successfully decrypted, which means the device has accepted this fake CA.**
<img width="1397" height="1131" alt="f886dda64cdd3ead81de53ee8ae6949" src="https://github.com/user-attachments/assets/7b4d2e35-aea8-46cb-86f0-1e3a136b266c" />


