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
图
### 2.Unauthenticated ONVIF Interface and RTSP Stream Accessible Without Credentials
Using any ONVIF client tools (I am using Agent DVR), the device was scanned for ONVIF capabilities.
The ONVIF service accepted any username and password. Even completely random credentials worked. The client was able to retrieve device configurations and capabilities without authentication.
图
**This confirms the ONVIF interface does not enforce access control.**
Also, the ONVIF response disclosed the device’s RTSP streaming URL.
Unlike standard formats such as:
`rtsp://admin:admin@<ip>/live/ch0`
The camera returned a URL without credentials:
`rtsp://<ip>/xxx/xxx`
图
**Connecting to this RTSP endpoint provided full live video access without authentication.**
### 3.TLS MITM: Certificate Validation Bypass


