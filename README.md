# Vulnerabilities-in-a-Besder-Camera

## Device information
- **Brand:** besder(white-label OEM)
- **Control APP:**: O-KAM Pro
- **Firmware version:** 10.194.120.44
- **Platform:** Eye4 Cloud
- **Purchase link:** https://www.aliexpress.com/item/1005009508892646.html

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
