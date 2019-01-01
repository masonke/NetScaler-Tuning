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

<table cellpadding="0" cellspacing="2">
	<tr>
		<td style="border: none; padding: 0in"><p align="center"><b>Arguments</b></p>
		</td>
		<td style="border: none; padding: 0in"><p align="center"><b>Ver</b></p>
		</td>
		<td style="border: none; padding: 0in"><p align="center"><b>Default
			Value</b></p>
		</td>
		<td style="border: none; padding: 0in"><p align="center"><b>Min</b></p>
		</td>
		<td style="border: none; padding: 0in"><p align="center"><b>Max</b></p>
		</td>
		<td style="border: none; padding: 0in"><p align="center"><b>sh cmd
			value</b></p>
		</td>
		<td style="border: none; padding: 0in"><p align="center"><b>Description</b></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>ackAggregation</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>ACK Aggregation</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable ACK
			Aggregation.</p>
			<p>NetScaler TCP Stack does delayedAck by default i.e. waits for
			the second packet or configured delayedAck time in profile before
			ACK'ing the TCP Segment. When ackAggregation is enabled, NetScaler
			can ack up to 16 TCP Segments together.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>ackOnPush</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>Immediate ACK on PUSH
			packet</p>
		</td>
		<td style="border: none; padding: 0in"><p>Send immediate
			acknowledgement (ACK) on receipt of TCP packets with the PUSH bit
			set.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>bufferSize</p>
		</td>
		<td style="border: none; padding: 0in"><p>9.3</p>
		</td>
		<td style="border: none; padding: 0in"><p>8190</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>20971520</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Buffer Size</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Recieve Buffer Size.
			The value that you set is the minimum receive window that is
			advertised by the NetScaler appliance, and this buffer size is
			reserved when a client initiates a connection that is associated
			with an endpoint-application function, such as compression or SSL.
			<br/>
The managed application can request a larger buffer, but if
			it requests a smaller buffer, the request is not honored, and the
			specified buffer size is used. If the TCP buffer size is set both
			at the global level and at the entity level (virtual server or
			service level), the buffer specified at the entity level takes
			precedence. If the buffer size that you specify for a service is
			not the same as the buffer size that you specify for the virtual
			server to which the service is bound, the NetScaler appliance uses
			the buffer size specified for the virtual server for the
			client-side connection and the buffer size specified for the
			service for the server-side connection. However, for optimum
			results, make sure that the values specified for a virtual server
			and the services bound to it have the same value. The buffer size
			that you specify is used only when the connection is associated
			with endpoint-application functions, such as SSL and compression.
			<br/>
This is not related to &nbsp;TCP buffering.</p>
			<p>Note: A high TCP buffer value could limit the number of
			connections that can be made to the NetScaler appliance.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>burstRateControl</p>
		</td>
		<td style="border: none; padding: 0in"><p>11</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>Burst Rate Control</p>
		</td>
		<td style="border: none; padding: 0in"><p><font color="#59595b">In
			a NetScaler appliance, this technique evenly spreads the
			transmission of a packets across the entire duration of the
			round-trip-time (RTT). This is achieved by using a TCP stack and
			network packet scheduler that identifies the various network
			conditions to output packets for ongoing TCP sessions in order to
			reduce the bursts. &nbsp;See &nbsp;</font><a href="https://docs.citrix.com/en-us/netscaler/11-1/system/TCP_Congestion_Control_and_Optimization_General.html">TCP
			Optimization</a><font color="#59595b"> for details.<br/>
Possible
			values: Disabled / Fixed / Dynamic<br/>
Uses&nbsp;tcprate&nbsp;and
			rateqmax for tuning</font></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>delayedAck</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>100</p>
		</td>
		<td style="border: none; padding: 0in"><p>10</p>
		</td>
		<td style="border: none; padding: 0in"><p>300</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Delayed-ACK Timer</p>
		</td>
		<td style="border: none; padding: 0in"><p>The time-out for TCP
			delayed ACK, in milliseconds.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>dsack</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>Duplicate Selective
			Acknowledgement(DSACK)</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable TCP
			Duplicate Selective Acknowledgement (DSACKs). <font color="#59595b">Detection
			of spurious retransmissions can be done using TCP duplicate
			selective acknowledgement (D-SACK) and forward RTO-Recovery
			(F-RTO). In case of spurious retransmissions, the congestion
			control configurations are reverted to their original state.&nbsp;
			The NetScaler implementation of D-SACK is RFC&nbsp;</font><a href="https://tools.ietf.org/html/rfc2883">2883</a><br/>
<strong>Question:
			Not clear about OS support on clients and servers.</strong></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>dupackthresh</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>3</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>15</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP dupack threshold</p>
		</td>
		<td style="border: none; padding: 0in"><p>This is the number of
			duplicate ACKs after which NS will start fast-retransmit.
			<a href="https://tools.ietf.org/html/rfc5681#section-3.2">https://tools.ietf.org/html/rfc5681#section-3.2</a><br/>
<br/>
Entering
			fast-retransmit/fast-recovery will reduce the current congestion
			window and hence will affect throughput. This is generally not
			tuned and left as RFC default. But if you know for sure that the
			network doesn’t cause out-of-order but any out-of-order packets
			are definitely due to lost packets, or if the number of packets
			outstanding is generally low for some traffic patterns, then one
			can lower the threshold. Or if the network is more prone to
			out-of-order, one can increase the threshold by a little. SACK and
			early retransmit/limited retransmit algorithms usually take care
			of such network behavior and is better to leave this value at 3.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>dynamicReceiveBuffering</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Dynamic Receive
			Buffering</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable dynamic
			receive buffering. When enabled, allows the receive buffer to be
			adjusted dynamically based on memory and network conditions.
			<br/>
Note: The buffer size argument must be set for dynamic
			adjustments to take place.&nbsp;</p>
			<p>bufferSize and recvBufferSize take effect when the transaction
			is <a href="#NetScalertcpprofilev10.5-11.1-NetScaler">ENDPOINT</a>.
			By default, for ENDPOINT transactions, NetScaler will advertise
			the whole recvBufferSize as the window available to the Peer. When
			the NetScaler(NS) is under load, this may result in over-promising
			on available buffer/memory resulting to drops and further stress
			on the system. When dynamicReceiveBuffering is enabled, NS uses a
			internal algorithm to determine the amount of window to advertise,
			which takes into account, the minimum requested by Apps (like
			AppFW, CMP, SSL), the amount of memory available, the configured
			recvBufferSize and the window advertised by client (for server
			connections and vice versa for client connections)</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>ecn</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Explicit Congestion
			Notification(ECN)</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable TCP
			Explicit Congestion Notification. <font color="#59595b">The
			NetScaler implementation of ECN is RFC&nbsp;</font><a href="http://tools.ietf.org/html/rfc3168">3168</a>
			<font color="#59595b">compliant.</font></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>EstablishClientConn</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>AUTOMATIC</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>Establishing Client
			Connection</p>
		</td>
		<td style="border: none; padding: 0in"><p>Establishing Client
			Client connection on First data/ Final-ACK / Automatic.<br/>
Possible
			values: AUTOMATIC, DISABLED</p>
			<p>With SYNCookie enabled, NetScaler doesn't create the TCP
			session even after TCP 3-way handshake for HTTP/SSL type vservers
			as NS knows that the Client should always send the first data
			packet. This behavior can be changed through this knob to create
			the session after 3-way handshake.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>fack</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Forward
			Acknowledgment(FACK)</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable FACK
			(Forward ACK). (FACK) is a TCP option which works in conjunction
			with SACK and helps avoid TCP congestion by measuring the total
			number of data bytes that are outstanding in the network. Using
			the information from SACK, it can more precisely calculate how
			much data it can retransmit.</p>
			<p><font color="#1f497d">enable/disable use of Forward
			ACKnowledgment (fack) algorithm during congestion control. FACK
			uses the information provided by the Selective ACK (sack) received
			during packet loss events to improve on existing congestion
			control algorithms.</font></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>flavor</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Default / New Reno</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP flavor</p>
		</td>
		<td style="border: none; padding: 0in"><p>Set TCP congestion
			control algorithm. Possible values: Default (New Reno), Westwood,
			BIC, CUBIC, Nile.</p>
			<p>Nile was added in v11</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>frto</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>Forward RTO
			recovery(FRTO)</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable FRTO
			(Forward RTO-Recovery). <font color="#59595b">Detection of
			spurious retransmissions can be done using TCP duplicate selective
			acknowledgement (D-SACK) and forward RTO-Recovery (F-RTO). In case
			of spurious retransmissions, the congestion control configurations
			are reverted to their original state.&nbsp; </font>The NetScaler
			implementation <font color="#59595b">is RFC&nbsp;</font><a href="http://tools.ietf.org/html/rfc5682">5682</a><font color="#59595b">&nbsp;compliant.</font></p>
			<p>Forward RTO recovery [<a href="https://tools.ietf.org/html/rfc5682">https://tools.ietf.org/html/rfc5682</a>]
			algorithm to detect spurious retransmissions i.e. retransmitting
			packets unnecessarily as the receiver has delayed
			acknowledgement/RTT changes etc.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>hystart&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>11</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Hybrid Start(HYSTART)</p>
		</td>
		<td style="border: none; padding: 0in"><p>A new TCP profile
			parameter, hystart, enables the Hystart algorithm, which is a
			slow-start algorithm that dynamically determines a safe point at
			which to terminate (ssthresh). It enables a transition to
			congestion avoidance without heavy packet losses. This new
			parameter is disabled by default.</p>
			<p>If congestion is detected, Hystart enters a congestion
			avoidance phase. Enabling it gives you better throughput in
			high-speed networks with high packet loss. This algorithm helps
			maintain close to maximum bandwidth while processing transactions.
			It can therefore improve throughput.&nbsp;<font color="#59595b">See
			&nbsp;</font><a href="https://docs.citrix.com/en-us/netscaler/11-1/system/TCP_Congestion_Control_and_Optimization_General.html">TCP
			Optimization</a><font color="#59595b"> for details.</font></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>initialCwnd</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>4</p>
		</td>
		<td style="border: none; padding: 0in"><p>2</p>
		</td>
		<td style="border: none; padding: 0in"><p>44</p>
		</td>
		<td style="border: none; padding: 0in"><p>Initial congestion
			window(cwnd) setting</p>
		</td>
		<td style="border: none; padding: 0in"><p>The initial maximum upper
			limit on the number of TCP packets that can be outstanding on the
			TCP link to the server.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>KA</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>Keep-alive probes</p>
		</td>
		<td style="border: none; padding: 0in"><p>Send periodic TCP
			keep-alive (KA) probes to check if peer is still up.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>KAconnIdleTime</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>900</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>4095</p>
		</td>
		<td style="border: none; padding: 0in"><p>Connection idle time
			before starting keep-alive probes</p>
		</td>
		<td style="border: none; padding: 0in"><p>Duration, in seconds, for
			the connection to be idle, before sending a keep-alive (KA) probe.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>KAmaxProbes</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>3</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>255</p>
		</td>
		<td style="border: none; padding: 0in"><p>Maximum keep-alive probes
			to be missed before dropping connection</p>
		</td>
		<td style="border: none; padding: 0in"><p>&quot;Number of
			keep-alive (KA) probes to be sent when not acknowledged, before
			assuming the peer to be down.&quot;</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>KAprobeInterval</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>75</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>4095</p>
		</td>
		<td style="border: none; padding: 0in"><p>Keep-alive probe interval</p>
		</td>
		<td style="border: none; padding: 0in"><p>Time interval, in
			seconds, before the next keep-alive (KA) probe, if the peer does
			not respond.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>KAprobeUpdateLastactivity</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>Update Last activity on
			KA Probes</p>
		</td>
		<td style="border: none; padding: 0in"><p>This knob determines if
			any KeepAlive probe received on a connection will be treated as
			activity on that connection (thus updating the last activity time
			for that connection) for calculating the idletime of the
			connection. if disabled, a connection with only KA probe will get
			flushed as inactive after the idleTimeout.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>maxBurst</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>6</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>255</p>
		</td>
		<td style="border: none; padding: 0in"><p>Maximum TCP segments
			allowed in a burst</p>
		</td>
		<td style="border: none; padding: 0in"><p>The maximum number of TCP
			segments allowed in a burst. The higher this value, the more
			frames are able to be sent at one time.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>maxcwnd</p>
		</td>
		<td style="border: none; padding: 0in"><p>11</p>
		</td>
		<td style="border: none; padding: 0in"><p>524288</p>
		</td>
		<td style="border: none; padding: 0in"><p>8190</p>
		</td>
		<td style="border: none; padding: 0in"><p>20971520</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Max congestion
			window(CWND)</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Maximum Congestion
			Window</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>maxPktPerMss</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p>512</p>
		</td>
		<td style="border: none; padding: 0in"><p>Maximum packets per MSS</p>
		</td>
		<td style="border: none; padding: 0in"><p>The maximum number of TCP
			packets allowed per maximum segment size (MSS). A value of 0 means
			that no maximum is set.</p>
			<p><strong><font color="#ff6600">This Profile parameter doesn't
			change much of the functionality in present releases. Please
			Ignore.</font></strong></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>minRTO</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>100</p>
		</td>
		<td style="border: none; padding: 0in"><p>10</p>
		</td>
		<td style="border: none; padding: 0in"><p>64000</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP minimum
			Restransmission Timeout(RTO) in millisec</p>
		</td>
		<td style="border: none; padding: 0in"><p>&quot; The minimum
			Receive Time Out (RTO) value, in milliseconds. The NetScale
			supports
			<a href="http://en.wikipedia.org/wiki/TCP_congestion_avoidance_algorithm%20New%20Reno">http://en.wikipedia.org/wiki/TCP_congestion_avoidance_algorithm
			New Reno</a> and conforms to RFC 2001 and RFC 5827</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>mptcp</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>Multipath TCP</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable
			Multipath TCP.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>mptcpDropDataOnPreEstSF</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>Multipath TCP drop data
			on pre-established subflow</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable
			silently dropping the data on Pre-Established subflow. When
			enabled, DSS data packets are dropped silently instead of dropping
			the connection when data is received on pre established subflow.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>mptcpFastOpen</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>Multipath TCP fastopen</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable
			Multipath TCP fastopen. When enabled, DSS data packets are
			accepted before receiving the third ack of SYN handshake.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>mptcpSessionTimeout</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p>86400</p>
		</td>
		<td style="border: none; padding: 0in"><p>Multipath TCP session
			timeout</p>
		</td>
		<td style="border: none; padding: 0in"><p>MPTCP session timeout in
			seconds. If this value is not set, idle MPTCP sessions are flushed
			after vserver's client idle timeout.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>mss</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p><font color="#59595b">9176</font></p>
		</td>
		<td style="border: none; padding: 0in"><p>Maximum Segment Size(MSS)</p>
		</td>
		<td style="border: none; padding: 0in"><p><font color="#59595b">Maximum
			number of octets to allow in a TCP data segment, used for Jumbo
			frames and VPNs</font></p>
			<p>From NetScaler 10.5 onwards, if the MSS value of the bound
			TCPprofile is 0, the MSS value is derived from the interface (and
			if applicable, VLAN) MTUs.&nbsp; <a href="http://docs.citrix.com/en-us/netscaler/10-5/ns-rn-main-wrapper-10-5-con/main-releases/whats-new-in-previous-10-5-builds.html">[From
			Build 50.10] [#422126, 425696]</a></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>nagle</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>Nagle's Algorithm</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable the
			Nagle algorithm on TCP connections. When enabled, reduces the
			number of small segments by combining them into one packet.
			Primary use is on slow or congested links such as mobile or dial.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>oooQSize</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>64</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p>65535</p>
		</td>
		<td style="border: none; padding: 0in"><p>Maximum out-of-order
			packets to queue</p>
		</td>
		<td style="border: none; padding: 0in"><p>The maximum size of
			out-of-order packets queue. A value of 0 means infinite. <br/>
The
			name is a misnomer, this buffer contains sent frames that are
			awaiting acks or received frames that are not sequential, meaning
			some packets are missing due to SACK</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>pktPerRetx</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>512</p>
		</td>
		<td style="border: none; padding: 0in"><p>Maximum packets per
			retransmission</p>
		</td>
		<td style="border: none; padding: 0in"><p>The maximum limit on the
			number of packets that should be retransmitted on receiving a
			&quot;partial ACK&quot;. Partial ACKare ACKs indicating not all
			outstanding frames were acked.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>rateqmax</p>
		</td>
		<td style="border: none; padding: 0in"><p>11</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Rate Maximum Queue</p>
		</td>
		<td style="border: none; padding: 0in"><p>Used with tcprate to tune
			burstRateControl</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>rstMaxAck</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>Accept RST with last
			acknowledged sequence number</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable
			acceptance of RST that is out of window yet echoes highest ACK
			sequence number.<br/>
Useful only in proxy mode. Could be disabled
			to prevent an attack. The NetScaler implementation of window
			attenuation is RFC&nbsp;<a href="http://tools.ietf.org/html/rfc4953">4953</a>&nbsp;compliant</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>rstWindowAttenuate</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>RST window attenuation
			(spoof protection)</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable attack
			protection for TCP Reset (RST) spoofing. When enabled, this
			argument restricts TCP to accept only RST in the Sequence number
			range.</p>
			<p>If the NetScaler receives a RST with an invalid sequence number
			it will reply with a <a href="#NetScalertcpprofilev10.5-11.1-NS_Correc">corrective
			ACK<sup>2</sup></a>.
			<a href="https://tools.ietf.org/html/rfc4953#section-3.1.2">https://tools.ietf.org/html/rfc4953#section-3.1.2</a>
			.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>SACK</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>Selective
			Acknowledgement(SACK) status</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable
			selective acknowledgement (SACK). Unless there is a bug in the
			code, there is NO reason this should be off.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>sendBuffsize</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>8190</p>
		</td>
		<td style="border: none; padding: 0in"><p>8190</p>
		</td>
		<td style="border: none; padding: 0in"><p>20971520</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Send Buffer Size</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Send Buffer Size</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>slowStartIncr</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>2</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>100</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Slow start increment</p>
		</td>
		<td style="border: none; padding: 0in"><p>The multiplier that
			determines the rate at which slow start increases the size of the
			TCP transmission window after each acknowledgement of successful
			transmission.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>spoofSynDrop</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>SYN spoof protection</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable drop of
			invalid SYN packets to protect against spoofing. When disabled,
			established connections will be reset when a SYN packet is
			received.</p>
			<p>A TCP stack will send a <a href="#NetScalertcpprofilev10.5-11.1-NS_Correc">corrective
			ACK <font color="#1f497d"><sup>2</font></sup></a><font color="#1f497d"><sup>&nbsp;</sup></font>
			when a TCP SYN is received for an already established session.</p>
			<p>When this option is disabled, a corrective ACK is sent only if
			the TCP SYN is within the window advertised by the existing
			session. otherwise the session gets RESET</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>synCookie</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Syncookie</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable the
			SYNCOOKIE mechanism for TCP handshake with clients. Disabling
			SYNCOOKIE prevents SYN attack protection on the NetScaler
			appliance.</p>
			<p>When enabled, SYNCookie is ALWAYS used for connection
			established on Client side. There is no minimum threshold after
			which SYNCookie gets triggered. when disabled, NetScaler will
			always create the session after receiving the TCP SYN from Client
			(Useful for cases where server sends data first, like SMTP).
			<br/>
<font color="#008000">Note: This is different from normal
			implementations where the session is created only after the ACK is
			received, precluding the use of TCP Options</font></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p><a href="#NetScalertcpprofilev10.5-11.1-NetScaler">tcpFastOpen</a></p>
		</td>
		<td style="border: none; padding: 0in"><p>11</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Fastopen</p>
		</td>
		<td style="border: none; padding: 0in"><p><font color="#59595b">TCP
			Fast Open (TFO) is a TCP mechanism that enables speedy and safe
			data exchange between a client and a server during TCP’s initial
			handshake. This feature is available as a TCP option in the TCP
			profile bound to a virtual server of a NetScaler appliance. TFO
			uses a TCP Fast Open Cookie (a security cookie) that the NetScaler
			appliance generates to validate and authenticate the client
			initiating a TFO connection to the virtual server. By using the
			TFO mechanism, you can reduce an application's network latency by
			the time required for one full round trip, which significantly
			reduces the delay experienced in short TCP transfers. See &nbsp;</font><a href="https://docs.citrix.com/en-us/netscaler/11-1/system/TCP_Congestion_Control_and_Optimization_General.html">TCP
			Optimization</a><font color="#59595b"> for details.</font></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>tcpmode</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>TRANSPARENT</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Optimization mode</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Optimization modes
			TRANSPARENT / ENDPOINT. Possible values: TRANSPARENT,
			ENDPOINT&nbsp;enable or disable ENDPOINT mode for the flow.
			<font color="#1f497d">NetScaler by default works in non-ENDPOINT
			mode, where the client and server handle the
			window/duplicate-ack/retransmission etc. NS will simply remap the
			packets and buffer only necessary packets for protocol parsing.
			The connection moves to ENDPOINT when NS needs to remake the
			packets. SSL/CMP/</font><a href="#NetScalertcpprofilev10.5-11.1-NetScaler">TCPB</a><font color="#1f497d">/
			of tcpMode=ENDPOINT in tcpProfile.</font></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>tcprate</p>
		</td>
		<td style="border: none; padding: 0in"><p>11</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Rate</p>
		</td>
		<td style="border: none; padding: 0in"><p>Used with rateqmax to
			tune burstRateControl</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>tcpSegOffload&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p><font color="#59595b">AUTOMATIC</font></p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Segmentation Offload</p>
		</td>
		<td style="border: none; padding: 0in"><p>Offload TCP segmentation
			to the NIC. If set to AUTOMATIC, TCP segmentation will be
			offloaded to the NIC, if the NIC supports it.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>TimeStamp</p>
		</td>
		<td style="border: none; padding: 0in"><p>10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Timestamp Option</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or Disable TCP
			Timestamp option. <font color="#59595b">The NetScaler
			implementation of TimeStamp option is RFC&nbsp;</font><a href="http://tools.ietf.org/html/rfc1323">1323</a><font color="#59595b">&nbsp;compliant.
			</font>
			</p>
			<p><strong><font color="#ff0000">Do not use this option in ANY
			version</font></strong><br/>
[From Build 61.11] [#593209] &nbsp;The
			NetScaler appliance does not reduce the received Maximum Segment
			Size (MSS) to accommodate TCP options (such as timestamps).
			Therefore, the NIC drops such packets.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>WS</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>Disabled</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>–</p>
		</td>
		<td style="border: none; padding: 0in"><p>Window Scaling status</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable window
			scaling. If Disabled, Window Scaling is disabled for both sides of
			the conversation. There is NO reason this should be disabled</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>WSVal</p>
		</td>
		<td style="border: none; padding: 0in"><p>9</p>
		</td>
		<td style="border: none; padding: 0in"><p>4</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p>14</p>
		</td>
		<td style="border: none; padding: 0in"><p>Window Scaling factor</p>
		</td>
		<td style="border: none; padding: 0in"><p>The left shift factor
			used to calculate the receive window size. For a value of 3,
			multiply the Window size by 8. Has no impact if WS is disabled.
			See <a href="https://support.citrix.com/article/CTX113656">How to
			Configure, Verify, and Troubleshoot TCP Window Scaling on a
			NetScaler Appliance</a> for details<br/>
Note: Wireshark shows the
			multiplication value, not the shift value.</p>
		</td>
	</tr>
</table>

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

