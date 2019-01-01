 NetScaler tcpprofile v10.5 - 11.1      td p { color: #000000 } h1 { color: #000000 } p { color: #000000 } h2 { color: #000000 } h2.cjk { font-family: "Songti SC" } h2.ctl { font-family: "Arial Unicode MS" } pre { color: #000000 } h3 { color: #000000 } h3.cjk { font-family: "Songti SC" } h3.ctl { font-family: "Arial Unicode MS" } a:link { color: #0000ff } a:visited { color: #800080 } 

NetScaler tcpprofile v10.5 - v11.1
==================================

Summary
=======

This document pulls together fractured information from the Citrix site, emails, testing and operational experience.

TCPProfiles are a powerful tool that allow the tuning of TCP flow characteristics to support the wide range of application and link requirements. The default profiles outlined below were developed to handle the middle 80% of traffic cases. The xfer profiles support the special case of large file transfers inbound to the vip. The mobile profile handles the unique requirements of mobile devices.

For simplicity, there is one profile for the Service and/or Service group.

**Like all tuning functions, these settings have the potential to make both a positive or negative impact on traffic and/or device. Make sure you understand what the argument does before making a change in production.**

TCPProfile Scope
================

*   TCPProfile settings affect the individual flows on a vserver, service or service group.
    
*   A flow is defined as the unique 5 tuple of Src IP, Dest IP, IP Protocol, Src Port and Dest Port
    
*   TCPProfiles can be set on services, service groups, CS vservers or LB vservers.
    
*   TCPProfiles have no effect on a noip vserver.
    
*   TCPProfiles override the global TCPParam settings
    
*   When a TCPProfile is set or changed, the changes only affect new connections. Existing sessions are not affected.
    
*   The set command is used to add the profile to the service or vserver.
    
*   Because they are UDP, TCPProfiles cannot be applied to GSLB vips.
    
*   Citrix documents these values in [ns tcpprofile](https://docs.citrix.com/en-us/netscaler/11/reference/netscaler-command-reference/ns/ns-tcpprofile.html) and in [TCP Optimization](https://docs.citrix.com/en-us/netscaler/11-1/system/TCP_Congestion_Control_and_Optimization_General.html)
    

Definitions
===========

Make sure we are on the same page

*   VIP - Load Balancing (LB) and Content Switching (CS) VServers
    
*   Service | ServiceGroup - Backend connectivity to Real Hosts
    
*   Outbound - Traffic outbound from the NetScaler VIP or Service
    
*   Inbound - Traffic inbound towards the NetScaler VIP or Service
    
*   flow - is a unique 5 tuple of Src IP, Dest IP, IP Protocol, Src Port and Dest Port
    

TCPProfile Arguments.
=====================

The Use column indicates out understanding of the option.

N = Not fully understood, research in progress. Use with care.  
Y = We understand and can use

**Arguments**

**Ver**

**Default Value**

**Min**

**Max**

**sh cmd value**

**Description**

ackAggregation

10.5

Disabled

ACK Aggregation

Enable or disable ACK Aggregation.

NetScaler TCP Stack does delayedAck by default i.e. waits for the second packet or configured delayedAck time in profile before ACK'ing the TCP Segment. When ackAggregation is enabled, NetScaler can ack up to 16 TCP Segments together.

ackOnPush

9

Enabled

–

–

Immediate ACK on PUSH packet

Send immediate acknowledgement (ACK) on receipt of TCP packets with the PUSH bit set.

bufferSize

9.3

8190

–

20971520

TCP Buffer Size

TCP Recieve Buffer Size. The value that you set is the minimum receive window that is advertised by the NetScaler appliance, and this buffer size is reserved when a client initiates a connection that is associated with an endpoint-application function, such as compression or SSL.  
The managed application can request a larger buffer, but if it requests a smaller buffer, the request is not honored, and the specified buffer size is used. If the TCP buffer size is set both at the global level and at the entity level (virtual server or service level), the buffer specified at the entity level takes precedence. If the buffer size that you specify for a service is not the same as the buffer size that you specify for the virtual server to which the service is bound, the NetScaler appliance uses the buffer size specified for the virtual server for the client-side connection and the buffer size specified for the service for the server-side connection. However, for optimum results, make sure that the values specified for a virtual server and the services bound to it have the same value. The buffer size that you specify is used only when the connection is associated with endpoint-application functions, such as SSL and compression.  
This is not related to  TCP buffering.

Note: A high TCP buffer value could limit the number of connections that can be made to the NetScaler appliance.

burstRateControl

11

Disabled

Burst Rate Control

In a NetScaler appliance, this technique evenly spreads the transmission of a packets across the entire duration of the round-trip-time (RTT). This is achieved by using a TCP stack and network packet scheduler that identifies the various network conditions to output packets for ongoing TCP sessions in order to reduce the bursts.  See  [TCP Optimization](https://docs.citrix.com/en-us/netscaler/11-1/system/TCP_Congestion_Control_and_Optimization_General.html) for details.  
Possible values: Disabled / Fixed / Dynamic  
Uses tcprate and rateqmax for tuning

delayedAck

9

100

10

300

TCP Delayed-ACK Timer

The time-out for TCP delayed ACK, in milliseconds.

dsack

10.5

Enabled

Duplicate Selective Acknowledgement(DSACK)

Enable or disable TCP Duplicate Selective Acknowledgement (DSACKs). Detection of spurious retransmissions can be done using TCP duplicate selective acknowledgement (D-SACK) and forward RTO-Recovery (F-RTO). In case of spurious retransmissions, the congestion control configurations are reverted to their original state.  The NetScaler implementation of D-SACK is RFC [2883](https://tools.ietf.org/html/rfc2883)  
**Question: Not clear about OS support on clients and servers.**

dupackthresh

3

1

15

TCP dupack threshold

This is the number of duplicate ACKs after which NS will start fast-retransmit. [https://tools.ietf.org/html/rfc5681#section-3.2](https://tools.ietf.org/html/rfc5681#section-3.2)  
  
Entering fast-retransmit/fast-recovery will reduce the current congestion window and hence will affect throughput. This is generally not tuned and left as RFC default. But if you know for sure that the network doesn’t cause out-of-order but any out-of-order packets are definitely due to lost packets, or if the number of packets outstanding is generally low for some traffic patterns, then one can lower the threshold. Or if the network is more prone to out-of-order, one can increase the threshold by a little. SACK and early retransmit/limited retransmit algorithms usually take care of such network behavior and is better to leave this value at 3.

dynamicReceiveBuffering

10.5

Enabled

–

–

TCP Dynamic Receive Buffering

Enable or disable dynamic receive buffering. When enabled, allows the receive buffer to be adjusted dynamically based on memory and network conditions.  
Note: The buffer size argument must be set for dynamic adjustments to take place. 

bufferSize and recvBufferSize take effect when the transaction is [ENDPOINT](#NetScalertcpprofilev10.5-11.1-NetScaler). By default, for ENDPOINT transactions, NetScaler will advertise the whole recvBufferSize as the window available to the Peer. When the NetScaler(NS) is under load, this may result in over-promising on available buffer/memory resulting to drops and further stress on the system. When dynamicReceiveBuffering is enabled, NS uses a internal algorithm to determine the amount of window to advertise, which takes into account, the minimum requested by Apps (like AppFW, CMP, SSL), the amount of memory available, the configured recvBufferSize and the window advertised by client (for server connections and vice versa for client connections)

ecn

10.5

Disabled

TCP Explicit Congestion Notification(ECN)

Enable or disable TCP Explicit Congestion Notification. The NetScaler implementation of ECN is RFC [3168](http://tools.ietf.org/html/rfc3168) compliant.

EstablishClientConn

10.5

AUTOMATIC

Establishing Client Connection

Establishing Client Client connection on First data/ Final-ACK / Automatic.  
Possible values: AUTOMATIC, DISABLED

With SYNCookie enabled, NetScaler doesn't create the TCP session even after TCP 3-way handshake for HTTP/SSL type vservers as NS knows that the Client should always send the first data packet. This behavior can be changed through this knob to create the session after 3-way handshake.

fack

10.5

Disabled

TCP Forward Acknowledgment(FACK)

Enable or disable FACK (Forward ACK). (FACK) is a TCP option which works in conjunction with SACK and helps avoid TCP congestion by measuring the total number of data bytes that are outstanding in the network. Using the information from SACK, it can more precisely calculate how much data it can retransmit.

enable/disable use of Forward ACKnowledgment (fack) algorithm during congestion control. FACK uses the information provided by the Selective ACK (sack) received during packet loss events to improve on existing congestion control algorithms.

flavor

10.5

Default / New Reno

–

–

TCP flavor

Set TCP congestion control algorithm. Possible values: Default (New Reno), Westwood, BIC, CUBIC, Nile.

Nile was added in v11

frto

10.5

Disabled

Forward RTO recovery(FRTO)

Enable or disable FRTO (Forward RTO-Recovery). Detection of spurious retransmissions can be done using TCP duplicate selective acknowledgement (D-SACK) and forward RTO-Recovery (F-RTO). In case of spurious retransmissions, the congestion control configurations are reverted to their original state. The NetScaler implementation is RFC [5682](http://tools.ietf.org/html/rfc5682) compliant.

Forward RTO recovery \[[https://tools.ietf.org/html/rfc5682](https://tools.ietf.org/html/rfc5682)\] algorithm to detect spurious retransmissions i.e. retransmitting packets unnecessarily as the receiver has delayed acknowledgement/RTT changes etc.

hystart 

11

Disabled

TCP Hybrid Start(HYSTART)

A new TCP profile parameter, hystart, enables the Hystart algorithm, which is a slow-start algorithm that dynamically determines a safe point at which to terminate (ssthresh). It enables a transition to congestion avoidance without heavy packet losses. This new parameter is disabled by default.

If congestion is detected, Hystart enters a congestion avoidance phase. Enabling it gives you better throughput in high-speed networks with high packet loss. This algorithm helps maintain close to maximum bandwidth while processing transactions. It can therefore improve throughput. See  [TCP Optimization](https://docs.citrix.com/en-us/netscaler/11-1/system/TCP_Congestion_Control_and_Optimization_General.html) for details.

initialCwnd

9

4

2

44

Initial congestion window(cwnd) setting

The initial maximum upper limit on the number of TCP packets that can be outstanding on the TCP link to the server.

KA

10.5

Disabled

–

–

Keep-alive probes

Send periodic TCP keep-alive (KA) probes to check if peer is still up.

KAconnIdleTime

10.5

900

1

4095

Connection idle time before starting keep-alive probes

Duration, in seconds, for the connection to be idle, before sending a keep-alive (KA) probe.

KAmaxProbes

10.5

3

1

255

Maximum keep-alive probes to be missed before dropping connection

"Number of keep-alive (KA) probes to be sent when not acknowledged, before assuming the peer to be down."

KAprobeInterval

10.5

75

1

4095

Keep-alive probe interval

Time interval, in seconds, before the next keep-alive (KA) probe, if the peer does not respond.

KAprobeUpdateLastactivity

10.5

Enabled

–

–

Update Last activity on KA Probes

This knob determines if any KeepAlive probe received on a connection will be treated as activity on that connection (thus updating the last activity time for that connection) for calculating the idletime of the connection. if disabled, a connection with only KA probe will get flushed as inactive after the idleTimeout.

maxBurst

9

6

1

255

Maximum TCP segments allowed in a burst

The maximum number of TCP segments allowed in a burst. The higher this value, the more frames are able to be sent at one time.

maxcwnd

11

524288

8190

20971520

TCP Max congestion window(CWND)

TCP Maximum Congestion Window

maxPktPerMss

9

0

0

512

Maximum packets per MSS

The maximum number of TCP packets allowed per maximum segment size (MSS). A value of 0 means that no maximum is set.

**This Profile parameter doesn't change much of the functionality in present releases. Please Ignore.**

minRTO

9

100

10

64000

TCP minimum Restransmission Timeout(RTO) in millisec

" The minimum Receive Time Out (RTO) value, in milliseconds. The NetScale supports [http://en.wikipedia.org/wiki/TCP\_congestion\_avoidance\_algorithm New Reno](http://en.wikipedia.org/wiki/TCP_congestion_avoidance_algorithm%20New%20Reno) and conforms to RFC 2001 and RFC 5827

mptcp

10.5

Disabled

Multipath TCP

Enable or disable Multipath TCP.

mptcpDropDataOnPreEstSF

10.5

Disabled

Multipath TCP drop data on pre-established subflow

Enable or disable silently dropping the data on Pre-Established subflow. When enabled, DSS data packets are dropped silently instead of dropping the connection when data is received on pre established subflow.

mptcpFastOpen

10.5

Disabled

Multipath TCP fastopen

Enable or disable Multipath TCP fastopen. When enabled, DSS data packets are accepted before receiving the third ack of SYN handshake.

mptcpSessionTimeout

10.5

0

0

86400

Multipath TCP session timeout

MPTCP session timeout in seconds. If this value is not set, idle MPTCP sessions are flushed after vserver's client idle timeout.

mss

9

0

0

9176

Maximum Segment Size(MSS)

Maximum number of octets to allow in a TCP data segment, used for Jumbo frames and VPNs

From NetScaler 10.5 onwards, if the MSS value of the bound TCPprofile is 0, the MSS value is derived from the interface (and if applicable, VLAN) MTUs.  [\[From Build 50.10\] \[#422126, 425696\]](http://docs.citrix.com/en-us/netscaler/10-5/ns-rn-main-wrapper-10-5-con/main-releases/whats-new-in-previous-10-5-builds.html)

nagle

9

Disabled

–

–

Nagle's Algorithm

Enable or disable the Nagle algorithm on TCP connections. When enabled, reduces the number of small segments by combining them into one packet. Primary use is on slow or congested links such as mobile or dial.

oooQSize

9

64

0

65535

Maximum out-of-order packets to queue

The maximum size of out-of-order packets queue. A value of 0 means infinite.  
The name is a misnomer, this buffer contains sent frames that are awaiting acks or received frames that are not sequential, meaning some packets are missing due to SACK

pktPerRetx

9

1

1

512

Maximum packets per retransmission

The maximum limit on the number of packets that should be retransmitted on receiving a "partial ACK". Partial ACKare ACKs indicating not all outstanding frames were acked.

rateqmax

11

0

TCP Rate Maximum Queue

Used with tcprate to tune burstRateControl

rstMaxAck

10.5

Disabled

Accept RST with last acknowledged sequence number

Enable or disable acceptance of RST that is out of window yet echoes highest ACK sequence number.  
Useful only in proxy mode. Could be disabled to prevent an attack. The NetScaler implementation of window attenuation is RFC [4953](http://tools.ietf.org/html/rfc4953) compliant

rstWindowAttenuate

10.5

Disabled

RST window attenuation (spoof protection)

Enable or disable attack protection for TCP Reset (RST) spoofing. When enabled, this argument restricts TCP to accept only RST in the Sequence number range.

If the NetScaler receives a RST with an invalid sequence number it will reply with a [corrective ACK2](#NetScalertcpprofilev10.5-11.1-NS_Correc). [https://tools.ietf.org/html/rfc4953#section-3.1.2](https://tools.ietf.org/html/rfc4953#section-3.1.2) .

SACK

9

Disabled

–

–

Selective Acknowledgement(SACK) status

Enable or disable selective acknowledgement (SACK). Unless there is a bug in the code, there is NO reason this should be off.

sendBuffsize

10.5

8190

8190

20971520

TCP Send Buffer Size

TCP Send Buffer Size

slowStartIncr

9

2

1

100

TCP Slow start increment

The multiplier that determines the rate at which slow start increases the size of the TCP transmission window after each acknowledgement of successful transmission.

spoofSynDrop

10.5

Enabled

SYN spoof protection

Enable or disable drop of invalid SYN packets to protect against spoofing. When disabled, established connections will be reset when a SYN packet is received.

A TCP stack will send a [corrective ACK 2](#NetScalertcpprofilev10.5-11.1-NS_Correc) when a TCP SYN is received for an already established session.

When this option is disabled, a corrective ACK is sent only if the TCP SYN is within the window advertised by the existing session. otherwise the session gets RESET

synCookie

10.5

Enabled

–

–

TCP Syncookie

Enable or disable the SYNCOOKIE mechanism for TCP handshake with clients. Disabling SYNCOOKIE prevents SYN attack protection on the NetScaler appliance.

When enabled, SYNCookie is ALWAYS used for connection established on Client side. There is no minimum threshold after which SYNCookie gets triggered. when disabled, NetScaler will always create the session after receiving the TCP SYN from Client (Useful for cases where server sends data first, like SMTP).  
Note: This is different from normal implementations where the session is created only after the ACK is received, precluding the use of TCP Options

[tcpFastOpen](#NetScalertcpprofilev10.5-11.1-NetScaler)

11

Disabled

TCP Fastopen

TCP Fast Open (TFO) is a TCP mechanism that enables speedy and safe data exchange between a client and a server during TCP’s initial handshake. This feature is available as a TCP option in the TCP profile bound to a virtual server of a NetScaler appliance. TFO uses a TCP Fast Open Cookie (a security cookie) that the NetScaler appliance generates to validate and authenticate the client initiating a TFO connection to the virtual server. By using the TFO mechanism, you can reduce an application's network latency by the time required for one full round trip, which significantly reduces the delay experienced in short TCP transfers. See  [TCP Optimization](https://docs.citrix.com/en-us/netscaler/11-1/system/TCP_Congestion_Control_and_Optimization_General.html) for details.

tcpmode

10.5

TRANSPARENT

TCP Optimization mode

TCP Optimization modes TRANSPARENT / ENDPOINT. Possible values: TRANSPARENT, ENDPOINT enable or disable ENDPOINT mode for the flow. NetScaler by default works in non-ENDPOINT mode, where the client and server handle the window/duplicate-ack/retransmission etc. NS will simply remap the packets and buffer only necessary packets for protocol parsing. The connection moves to ENDPOINT when NS needs to remake the packets. SSL/CMP/[TCPB](#NetScalertcpprofilev10.5-11.1-NetScaler)/ of tcpMode=ENDPOINT in tcpProfile.

tcprate

11

0

TCP Rate

Used with rateqmax to tune burstRateControl

tcpSegOffload 

10.5

AUTOMATIC

TCP Segmentation Offload

Offload TCP segmentation to the NIC. If set to AUTOMATIC, TCP segmentation will be offloaded to the NIC, if the NIC supports it.

TimeStamp

10.5

Disabled

TCP Timestamp Option

Enable or Disable TCP Timestamp option. The NetScaler implementation of TimeStamp option is RFC [1323](http://tools.ietf.org/html/rfc1323) compliant.

**Do not use this option in ANY version**  
\[From Build 61.11\] \[#593209\]  The NetScaler appliance does not reduce the received Maximum Segment Size (MSS) to accommodate TCP options (such as timestamps). Therefore, the NIC drops such packets.

WS

9

Disabled

–

–

Window Scaling status

Enable or disable window scaling. If Disabled, Window Scaling is disabled for both sides of the conversation. There is NO reason this should be disabled

WSVal

9

4

0

14

Window Scaling factor

The left shift factor used to calculate the receive window size. For a value of 3, multiply the Window size by 8. Has no impact if WS is disabled. See [How to Configure, Verify, and Troubleshoot TCP Window Scaling on a NetScaler Appliance](https://support.citrix.com/article/CTX113656) for details  
Note: Wireshark shows the multiplication value, not the shift value.

 1 NetScaler by default works in non-ENDPOINT mode, where the client and server handle the window/duplicate-ack/retransmission etc. NS will simply remap the packets and buffer only necessary packets for protocol parsing. The connection moves to ENDPOINT when NS needs to remake the packets. SSL/CMP/[TCPB](#NetScalertcpprofilev10.5-11.1-NetScaler)/ of tcpMode=ENDPOINT in tcpProfile.

TCPB TCP Buffering Mode

2 **Corrective Ack -** A zero length TCP Ack packet used in response to SYN or RST packets that are out of Sequence number rage.  In some circumstances, such as a SYN packet is received when the connection is already established, a RST not in the sequence number range, or the timestamp option determines packet is older incarnation, the NetScaler TCP stack will send an ACK only packet ACK’ing/informing the peer of the next expected sequence number. This helps prevent spoofed attacks from dropping sessions.  Complies with [RFC 4953 section-3.1.2 / TCP RST Window Attenuation](https://tools.ietf.org/html/rfc4953#section-3.1.2)

 Custom TCPProfiles
===================

tcpprofile naming
-----------------

The name format is <vip|svc>\_<text\_description\_using"\_"-as-spacer>\_tcpprofile.  
Characters will all be lower case.

We will be renaming all existing profiles to match this format.

vip\_std\_tcpprofile
--------------------

*   Standard vip profile
    
*   General purpose use
    
*   Use as a starting point for further tuning
    

**V10.5**

add ns tcpProfile vip\_std\_tcpprofile -WS ENABLED -SACK ENABLED -WSVal 4 -nagle DISABLED -ackOnPush ENABLED -maxBurst 10 -initialCwnd 10 -delayedAck 100 -oooQSize 64 -maxPktPerMss 0 -pktPerRetx 2 -minRTO 400 -slowStartIncr 4 -bufferSize 16380 -synCookie DISABLED -flavor CUBIC -sendBuffsize 16380 -tcpSegOffload AUTOMATIC -TimeStamp DISABLED

**V11**

add ns tcpProfile vip\_std\_tcpprofile -WS ENABLED -SACK ENABLED -WSVal 4 -nagle DISABLED -ackOnPush ENABLED -maxBurst 10 -initialCwnd 10 -delayedAck 100 -oooQSize 64 -maxPktPerMss 0 -pktPerRetx 2 -minRTO 400 -slowStartIncr 4 -bufferSize 16380 -synCookie DISABLED -flavor CUBIC -sendBuffsize 16380 -tcpSegOffload AUTOMATIC -TimeStamp DISABLED -maxcwnd 1048576
**Apply the profile**

`set lb vserver <vipname> -tcpProfileName fd_vip_std_tcpprofile`

**Assumptions** 

*   The mean propagation delay (RTT) is 75 ms (0.075 sec)
    
*   If the rcvBuffsize in the tcpparam is set >32,736, then reduce the WSVal to 3
    

vip\_mobile-client\_tcpprofile
------------------------------

Implements changes to improve the the mobile (3G, 4G) client experience by 

*   Increasing tolerance for RTT shifts as the device moves between cells and WiFi points.
    
*   This reduces retransmissions that can flood a device and use up the device battery.
    
*   Changes from vip\_std\_tcpprofile:
    
    *   Enable Nagle to reduce battery consumption.
        
    *   Increase the -minRTO
        
    *   Reduced –maxburst
        
    *   Reduced -delayedAck
        
    *   Reduced -slowstartincr
        

  
**V10.5**

add ns tcpProfile vip\_mobile-client\_tcpprofile -WS ENABLED -SACK ENABLED -WSVal 4 -nagle ENABLED -ackOnPush ENABLED -maxBurst 10 -initialCwnd 6 -delayedAck 50 -oooQSize 100 -maxPktPerMss 0 -pktPerRetx 2 -minRTO 1000 -slowStartIncr 4 -bufferSize 16380 -synCookie DISABLED -flavor CUBIC -sendBuffsize 16380 -tcpSegOffload AUTOMATIC -TimeStamp DISABLED

  
**V11**

add ns tcpProfile vip\_mobile-client\_tcpprofile -WS ENABLED -SACK ENABLED -WSVal 4 -nagle ENABLED -ackOnPush ENABLED -maxBurst 10 -initialCwnd 6 -delayedAck 50 -oooQSize 100 -maxPktPerMss 0 -pktPerRetx 2 -minRTO 1000 -slowStartIncr 4 -bufferSize 16380 -synCookie DISABLED -flavor CUBIC -sendBuffsize 16380 -tcpSegOffload AUTOMATIC -TimeStamp DISABLED -maxcwnd 1048576

**Assumptions:**

*   RTT can rapidly shift from 300ms to 900ms and back during the session.
    
*   bps is limited to < 4mb/s
    

**Apply the profile**
set lb vserver <vipname>-tcpProfileNamefd\_vip\_mobile-client\_tcpprofile

vip\_xfer\_tcpprofile
---------------------

*   Used for large data transfer from the Client towards the Servers
    
*   Increased WSVal
    
*   Increased bufferSize
    
*   Increased sendBuffsize
    

**V10.5**

add ns tcpProfile vip\_xfer\_tcpprofile -WS ENABLED -SACK ENABLED -WSVal 10 -nagle DISABLED -ackOnPush ENABLED -maxBurst 10 -initialCwnd 10 -delayedAck 100 -oooQSize 64 -maxPktPerMss 0 -pktPerRetx 2 -minRTO 400 -slowStartIncr 4 -bufferSize 16380 -synCookie DISABLED -flavor CUBIC -sendBuffsize 16380 -TimeStamp DISABLED

**V11**

add ns tcpProfile vip\_xfer\_tcpprofile -WS ENABLED -SACK ENABLED -WSVal 10 -nagle DISABLED -ackOnPush ENABLED -maxBurst 10 -initialCwnd 10 -delayedAck 100 -oooQSize 64 -maxPktPerMss 0 -pktPerRetx 2 -minRTO 400 -slowStartIncr 4 -bufferSize 16380 -synCookie DISABLED -flavor CUBIC -sendBuffsize 16380 -TimeStamp DISABLED-maxcwnd 1048576
**Apply the profile**

`set lb vserver <vipname> -tcpProfileName `fd\_vip\_xfer\_tcpprofile

Service and Service Group Profiles
==================================

svc\_std\_tcpprofile
--------------------

*   For services and service groups
    
*   Covers the vast majority of use cases.
    
*   Rememebr, this is from the NetScaler to the Servers and has a lower RTT and higher possible BW then from the VIP to the Client
    
*   Use as a base for further tuning
    

**V10.5**

add ns tcpProfile svc\_std\_tcpprofile -WS ENABLED -SACK ENABLED -WSVal 10 -nagle DISABLED -ackOnPush ENABLED -maxBurst 10 -initialCwnd 10 -delayedAck 100 -oooQSize 64 -maxPktPerMss 0 -pktPerRetx 2 -minRTO 100 -slowStartIncr 4 -bufferSize 16380 -synCookie DISABLED -flavor CUBIC -sendBuffsize 16380 -TimeStamp DISABLED
**V11**
add ns tcpProfile svc\_xfer\_tcpprofile -WS ENABLED -SACK ENABLED -WSVal 10 -nagle DISABLED -ackOnPush ENABLED -maxBurst 10 -initialCwnd 10 -delayedAck 100 -oooQSize 64 -maxPktPerMss 0 -pktPerRetx 2 -minRTO 100 -slowStartIncr 4 -bufferSize 16380 -synCookie DISABLED -flavor CUBIC -sendBuffsize 16380 -TimeStamp DISABLED-maxcwnd 1048576
**Apply the profile**
set service <sgname> -tcpProfileName fd\_svc\_xfer\_tcpprofile

Working with Profiles
=====================

*   See if a profile exists on a NetScaler:
    

sh tcpprofile -format old

*   See all the options in a profile
    

sh tcpprofile <profilename> -format old -level verbose

*   Add a profile to the NetScaler:
    

add ns tcpProfile <new\_profile\_name> <variable=value>...<variable=value>

*   To change a variable in a tcpprofile:
    

set ns tcpprofile <profile\_name> <variable=value>...<variable=value>

*   To remove a tcpprofile:
    

rm ns tcpprofile <profile\_name>

*   To see what profile is applied to a vserver or service:
    

sh lb vserver <vservername:port> sh cs vserver <vservername:port> sh service <service:port> sh servicegroup <servicegroup:port>

Manually Applying Profiles
--------------------------

There may be times that a profile needs to be applied or changed manually.

### Manually apply a tcpprofile to a cs vip

set cs vserver <vservername:port> -tcpprofile <profilename>

set cs vserver foo-443-VIP -tcpProfileName vip\_std\_tcpprofile

to return to default

unset cs vserver <vservername:port> -tcpprofile nstcp\_default\_profile

unset cs vserver foo-443-VIP -tcpProfileName nstcp\_default\_profile

### Manually apply or remove a tcpprofile to a lb vip

set lb vserver <vservername:port> -tcpprofile <profilename>

set lb vserver foo-443-VIP -tcpProfileName fd\_vip\_std\_tcpprofile

to return to default

unset lb vserver <vservername:port> -tcpprofile nstcp\_default\_profile

unset lb vserver foo-443-VIP -tcpProfileName nstcp\_default\_profile

### Manually apply a tcpprofile to a service or service group

set service <servicename:port> -tcpprofile <profilename>

set service foo-443-SF -tcpProfileName fd\_svc\_std\_tcpprofile

to return to default

unset service <servicename:port> -tcpprofile nstcp\_default\_profile

unset service foo-BIZ-443-SF -tcpProfileName nstcp\_default\_profile

