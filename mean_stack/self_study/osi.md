# OSI MODEL

__The open systems interconnection (OSI) model is a conceptual model created by the International Organization for Standardization which enables diverse communication systems to communicate using standard protocols. In plain English, the OSI provides a standard for different computer systems to be able to communicate with each other.__

## Why does the OSI model matter

__Although the modern Internet does not strictly follow the OSI Model (it more closely follows the simpler Internet protocol suite), the OSI Model is still very useful for troubleshooting network problems. Whether it’s one person who can’t get their laptop on the Internet, or a website being down for thousands of users, the OSI Model can help to break down the problem and isolate the source of the trouble. If the problem can be narrowed down to one specific layer of the model, a lot of unnecessary work can be avoided.__

## Layers of Osi model:

_Lower layer: Hardware focus_

__These layers handle the physical delivery of data across the network__

### 1. Physical Layer (Layer 1):

- __Function:__ Transmission of raw data bits across a physical medium (cables, connectors).

- __Protocols:__ Ethernet, RS-232, USB, DSL.

### 2. Data Link Layer (Layer 2):

- __Function:__ Reliable node-to-node data transfer, error correction for the physical layer.

- __Sub-layers:__

  - __Logical Link Control (LLC):__ Manages communication between data link and network layers.

  - __Media Access Control (MAC):__ Regulates device access to the network medium for data transmission.

- __Protocols:__ Ethernet, PPP, HDLC.

### 3. Network Layer (Layer 3):

- __Function:__ Routing - determining the optimal path for data to reach its destination.

- __Components:__ Routers, Layer 3 switches.

- __Protocols:__ IP (Internet Protocol), ICMP, IPsec.

### 4. Transport Layer (Layer 4):

- __Function:__ Guarantees reliable data transfer, manages end-to-end communication, and oversees error recovery.

- __Components:__ Gateways, firewalls.

- __Protocols:__ TCP (Transmission Control Protocol), UDP (User Datagram Protocol).

_Upper layer: Software Focus_

__These layers handle application-level issues, formatting, and data exchange__

### 5. Session Layer (Layer 5):

- __Function:__ Establishes, manages, and terminates sessions between applications.

- __Components:__ APIs, Sockets.

- __Protocols:__ NetBIOS, RPC (Remote Procedure Call).

### 6. Presentation Layer (Layer 6):

- __Function:__ Data translation between application and network formats, including encryption and compression.

- __Components:__ Gateways.

- __Protocols:__ SSL/TLS, JPEG, MPEG, GIF.

### 7. Application Layer (Layer 7):

- __Function:__ Provides network services directly to applications and interfaces with the end-user.

- __Components:__ End-user devices and applications.

- __Protocols:__ HTTP, FTP, SMTP, DNS, SNMP.

