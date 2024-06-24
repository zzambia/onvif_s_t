# ONVIF Profile S and Profile T: a quick survey

## 1. Introduction

### 1.1 What is ONVIF?

ONVIF (Open Network Video Interface Forum) is a global open industry forum that develops and promotes standardized interfaces for effective interoperability of IP-based physical security products. It aims to standardize communication between IP-based physical security devices, ensuring that products from different manufacturers can work together seamlessly.

### 1.2 Purpose of ONVIF profiles

ONVIF profiles are designed to help manufacturers, systems integrators, and end users by providing specific feature sets for common applications. These profiles simplify the selection, integration, and operation of devices by ensuring a consistent set of features and capabilities across different manufacturers' products.

### 1.3 Overview of Profile S and Profile T

Profile S and Profile T are two of the most widely used ONVIF profiles for IP cameras and video management systems.

- Profile S: Addresses core streaming features for IP cameras, encoders, and NVRs.
- Profile T: Extends Profile S with advanced streaming capabilities, improved imaging settings, and support for analytics.

## 2. ONVIF Profile S

### 2.1 Key features

- Basic device discovery and management
- Video streaming (H.264 and MJPEG)
- PTZ control
- Audio streaming
- Metadata streaming
- Event handling
- Security features

### 2.2 Minimum implementation requirements

- Device discovery using WS-Discovery
- Device management (system date and time, network configuration)
- Media streaming using RTSP/RTP
- Support for at least one H.264 profile
- Basic event subscription and notification
- WS-UsernameToken authentication

### 2.3 Supported protocols and services

- RTSP (Real-Time Streaming Protocol)
- RTP/RTCP (Real-time Transport Protocol)
- HTTP/HTTPS
- SOAP (Simple Object Access Protocol)
- WS-Discovery

## 3. ONVIF Profile T

### 3.1 Enhancements over Profile S

- Advanced video streaming capabilities
- Improved imaging controls
- Enhanced PTZ functionality
- Standardized metadata streaming
- More sophisticated event handling

### 3.2 New features introduced

- H.265/HEVC codec support
- 4K resolution support
- Higher frame rates (up to 60 fps)
- Advanced imaging settings (e.g., wide dynamic range, backlight compensation)
- Remote focus control
- Metadata streaming alongside video
- Audio analytics support

### 3.3 Advanced capabilities

- 4K video streaming: Support for ultra-high-definition video
- H.265 codec: More efficient compression for reduced bandwidth usage
- Metadata streaming: Ability to stream analytics data alongside video
- Advanced PTZ: More precise control and smoother movements
- Enhanced security: TLS 1.2 or higher required, improved password policies

## 4. Comparison of Profile S and Profile T

### 4.1 Detailed comparison table

| Feature | Profile S | Profile T |
|---------|-----------|-----------|
| Video Codecs | H.264, MJPEG | H.264, MJPEG, H.265/HEVC |
| Max Resolution | 1080p common | 4K support required |
| Max Frame Rate | 30 fps typical | Up to 60 fps |
| Imaging Controls | Basic | Advanced (HDR, remote focus) |
| PTZ Control | Basic | Enhanced precision, advanced tours |
| Metadata Streaming | Not standardized | Supported |
| Event Handling | Basic | Advanced filtering and subscription |
| Audio | Basic streaming | Bidirectional, analytics support |
| Security | Basic (HTTPS, WS-UsernameToken) | Enhanced (TLS 1.2+, stricter passwords) |
| Edge Storage | Not standardized | Standardized management |
| Multicast | Required | Required |
| Backwards Compatibility | N/A | Must be compatible with Profile S |

### 4.2 Key differences in implementation

- Codec support: Profile T requires implementation of H.265/HEVC
- Resolution and frame rate: Higher requirements for Profile T
- Metadata handling: Profile T needs a standardized way to stream metadata
- Analytics: Profile T includes support for video analytics
- Security: Stricter requirements in Profile T

## 5. Mandatory ONVIF Services and Calls

### 5.1 Device Management Service

Mandatory calls for both Profile S and T:
- GetDeviceInformation
- GetCapabilities
- GetServices
- GetServiceCapabilities
- SystemReboot
- GetSystemDateAndTime
- SetSystemDateAndTime
- GetScopes
- GetNetworkInterfaces

### 5.2 Media Service

Profile S mandatory calls:
- GetProfiles
- GetStreamUri
- GetSnapshotUri
- GetVideoEncoderConfigurations
- GetVideoEncoderConfiguration
- GetCompatibleVideoEncoderConfigurations
- GetVideoSourceConfigurations
- GetVideoSourceConfiguration
- GetAudioEncoderConfigurations
- GetAudioEncoderConfiguration

Profile T additional calls (Media2 service):
- GetVideoEncoderInstances
- GetStreamUri
- GetSnapshotUri
- GetConfiguration
- GetVideoSourceConfigurations

### 5.3 PTZ Service (if supported)

Mandatory calls for both Profile S and T:
- GetConfigurations
- GetConfiguration
- GetConfigurationOptions
- ContinuousMove
- Stop
- GetStatus

### 5.4 Imaging Service

Mandatory calls for both Profile S and T:
- GetImagingSettings
- GetOptions
- SetImagingSettings

### 5.5 Event Service

Mandatory calls for both Profile S and T:
- GetServiceCapabilities
- CreatePullPointSubscription
- PullMessages
- Unsubscribe

### 5.6 Analytics Service (Profile T only)

Mandatory calls:
- GetServiceCapabilities
- GetSupportedAnalyticsModules
- CreateAnalyticsModules
- DeleteAnalyticsModules
- GetAnalyticsModules

## 6. Implementation Guide

### 6.1 C# implementation overview

Implementing ONVIF in C# involves creating a web service that handles SOAP requests and responses, along with implementing the required ONVIF services. Key components include:
- HTTP/HTTPS server for handling SOAP requests
- XML parsing for SOAP messages
- RTSP server for media streaming
- Implementation of ONVIF services and methods

### 6.2 Considerations for embedded Linux devices

- Limited resources (CPU, memory)
- Cross-platform compatibility
- Optimized libraries for embedded systems
- Careful resource management

### 6.3 Key libraries for TLS and RTSP

For TLS:

```csharp
using System.Net.Security;
using System.Security.Cryptography.X509Certificates;

public void SecureConnection(TcpClient client, X509Certificate serverCertificate)
{
    using (SslStream sslStream = new SslStream(client.GetStream(), false))
    {
        sslStream.AuthenticateAsServer(serverCertificate);
        // Use sslStream for secure communication
    }
}
```

For RTSP (using RtspClientSharp):

```csharp
using RtspClientSharp;

var connectionParameters = new ConnectionParameters(new Uri("rtsp://your_camera_ip/stream"));
using (var rtspClient = new RtspClient(connectionParameters))
{
    await rtspClient.ConnectAsync();
    // Handle streaming
}
```

### 6.4 Basic code structure and examples

Basic ONVIF server structure:

```csharp
public class OnvifServer
{
    private HttpListener _listener;

    public void Start()
    {
        _listener = new HttpListener();
        _listener.Prefixes.Add("http://+:8080/onvif/");
        _listener.Start();
        
        while (true)
        {
            HttpListenerContext context = _listener.GetContext();
            ProcessRequest(context);
        }
    }

    private void ProcessRequest(HttpListenerContext context)
    {
        // Parse SOAP request and handle ONVIF methods
    }
}
```

## 7. Challenges and Considerations

### 7.1 Resource management on embedded devices

- Efficient memory usage
- CPU optimization
- Power consumption considerations

### 7.2 Security implementation

- Proper TLS implementation
- Secure storage of credentials
- Regular security updates

### 7.3 Testing and compliance verification

- ONVIF Device Test Tool
- Interoperability testing with various clients
- Performance testing under different network conditions

## 8. Conclusion

### 8.1 Recap of key points

- ONVIF standardizes communication between IP-based security devices
- Profile S covers basic streaming features, while Profile T adds advanced capabilities
- Implementing ONVIF requires careful attention to the specification and resource management

### 8.2 Next steps for implementation

- Thorough review of ONVIF specifications
- Development of a prototype implementation
- Testing and verification using ONVIF tools
- Optimization for target embedded platform

### 8.3 Resources for further learning

- ONVIF official website (www.onvif.org)
- ONVIF specification documents
- ONVIF Developer Plugfests

## 9. Appendices

### A. Full list of mandatory calls for Profile S and T

(Include the complete table from earlier in the document)

### B. Sample code snippets

(Include more detailed code examples for key ONVIF operations)

### C. Glossary of ONVIF terms

(Include definitions for key ONVIF-specific terms and acronyms)
