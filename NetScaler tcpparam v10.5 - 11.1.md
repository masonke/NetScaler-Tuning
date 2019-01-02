NetScaler tcpparam v10.5 - 11.1
===============================

tcpparam
========

*   This document pulls together the fractured information from the Citrix site, emails and testing.
*   TCPParameters are a global setting, affecting all the vservers and services on the device
*   Custom NetScaler TCPProfiles are bound per vserver or service and override the global tcpParam settings
*   When a TCPParam or TCPProfile change is made, the values only affect new connections. Existing sessions are not modified.

· Changes in tcpParam change the equivalent setting in the default Netscaler TCPProfile nstcp\_default\_profile  

These settings have the potential to make a huge impact, both positive or negative on traffic and/or the device. Make sure you know what you are changing before touching production.

Definitions
===========

Make sure we are on the same page

*   VIP - Load Balancing (LB) and Content Switching (CS) VServers
*   Service | ServiceGroup - Backend connectivity to Real Hosts
*   Outbound - Traffic outbound from the NetScaler VIP or Service
*   Inbound - Traffic inbound towards the NetScaler VIP or Service 

v10.5 - 11.1 TCPParam Variables


<table cellpadding="0" cellspacing="2">
	<tr>
		<td style="border: none; padding: 0in"><p align="center"><b>Variable</b></p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal" align="center">
			<b>Ver</b></p>
		</td>
		<td style="border: none; padding: 0in"><p align="center"><b>Default
			Value</b></p>
		</td>
		<td style="border: none; padding: 0in"><p align="center"><b>Min</b></p>
		</td>
		<td style="border: none; padding: 0in"><p align="center"><b>Max</b></p>
		</td>
		<td style="border: none; padding: 0in"><p align="center"><b>FirstData</b></p>
		</td>
		<td style="border: none; padding: 0in"><p align="center"><b>Description</b></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>ackOnPush</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>ENABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>ENABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>Send immediate positive
			acknowledgement (ACK) on receipt of TCP packets when doing Web 2.0
			PUSH.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>connFlushIfNoMem</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">NONE</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p style="margin-bottom: 0.2in">
			HALF_CLOSED_AND_IDLE</p>
			<p style="margin-bottom: 0.2in">FIFO</p>
			<p>NONE</p>
		</td>
		<td style="border: none; padding: 0in"><p style="margin-bottom: 0.2in">
			NONE</p>
			<p>&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>Flush an existing
			connection if no memory can be obtained for new
			connection.<br/>
HALF_CLOSED_AND_IDLE: Flush a connection that is
			closed by us but not by peer, or failing that, a connection that
			is past configured idle time. New connection fails if no such
			connection can be found.<br/>
FIFO: If no half-closed or idle
			connection can be found, flush the oldest non-management
			connection, even if it is active. New connection fails if the
			oldest few connections are management connections.<br/>
Note: If
			you enable this setting, you should also consider lowering the
			zombie timeout and half-close timeout, while setting the NetScaler
			timeout.<br/>
See Also: connFlushThres argument below.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">connFlushThres</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">4294967295</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">1</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p>Flush an existing
			connection (as configured through -connFlushIfNoMem FIFO) if the
			system has more than specified number of connections, and a new
			connection is to be established. Note: This value may be rounded
			down to be a whole multiple of the number of packet engines
			running.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>delayedAck</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>300</p>
		</td>
		<td style="border: none; padding: 0in"><p>10</p>
		</td>
		<td style="border: none; padding: 0in"><p>300</p>
		</td>
		<td style="border: none; padding: 0in"><p>50</p>
		</td>
		<td style="border: none; padding: 0in"><p>The time-out for TCP
			delayed ACK, in milliseconds. delayedAck sets how long NS waits
			for 2nd packet before acknowledging the first TCP segment</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>downStateRST</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>DISABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>ENABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>By default, a vServer
			that is in a DOWN state would simply drop any incoming SYN packet.
			When ENABLED, the NetScaler sends a RST instead. By not sending a
			RST, the client has to send multiple syns, timing out between each
			syn. Sending a RST tells the other end that the port is down.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>initialCwnd</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>4</p>
		</td>
		<td style="border: none; padding: 0in"><p>2</p>
		</td>
		<td style="border: none; padding: 0in"><p>44</p>
		</td>
		<td style="border: none; padding: 0in"><p>6</p>
		</td>
		<td style="border: none; padding: 0in"><p>The initial maximum upper
			limit on the number of TCP packets that can be outstanding on the
			TCP link to the server.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>learnVsvrMSS <a href="#NetScalertcpparamv10.5-11.1-NetScaler-m"><sup>(2)</sup></a></p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>DISABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>DISABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>enable/disable MSS
			learning for vservers</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>limitedPersist</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>ENABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>ENABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>If limitedPersist is
			disabled, then NS will continuously send persist/zero window
			probes when the peer advertises zero window and the connection
			will only be closed due to idle timeout on NS or due to RESET from
			peer.<br/>
<br/>
When enabled, NS will clean up the connection
			after 7 persist probes donât result in window opening from peer.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>maxBurst</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>6</p>
		</td>
		<td style="border: none; padding: 0in"><p>2</p>
		</td>
		<td style="border: none; padding: 0in"><p>255</p>
		</td>
		<td style="border: none; padding: 0in"><p>6</p>
		</td>
		<td style="border: none; padding: 0in"><p>The maximum number of TCP
			segments allowed in a burst. This pushes the traffic through the
			NetScaler faster.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>maxDynServerProbes <a href="#NetScalertcpparamv10.5-11.1-NetScaler-S"><sup>(1)</sup></a></p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>7</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>65535</p>
		</td>
		<td style="border: none; padding: 0in"><p>7</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">This is
			relevant only in case of wildcard (IP/Port is â*â)
			entities/vpn etc. where NetScaler learns the actual server-info by
			sending the probe to the backend/origin IP received from the
			client SYNâs destination IP. This knob limits the number of
			probes each Packet Engine/core on NetScaler can send in 10
			milliseconds. When this is exceeded, the incoming SYN are simply
			dropped. The client would generally retry and the connection
			should go through.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>maxPktPerMss</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p>1460</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p><font color="#ff6600">Not
			used anymore, please ignore.</font> The maximum number of TCP
			packets allowed per maximum segment size (MSS). A value of 0 means
			that no maximum is set.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">maxSynAckRetx</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">100</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">100</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">1048576</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">100</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">When
			'syncookie' is disabled in the TCP profile that is bound to the
			virtual server or service, and the number of TCP SYN+ACK
			retransmission by NetScaler for that virtual server or service
			crosses this threshold, the NetScaler appliance responds by using
			the TCP SYN-Cookie mechanism.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>maxSynhold &nbsp;<a href="#NetScalertcpparamv10.5-11.1-NetScaler-S"><sup>(1)</sup></a></p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>16384</p>
		</td>
		<td style="border: none; padding: 0in"><p>256</p>
		</td>
		<td style="border: none; padding: 0in"><p>65535</p>
		</td>
		<td style="border: none; padding: 0in"><p>16384</p>
		</td>
		<td style="border: none; padding: 0in"><p>Max number of client
			(assuming each SYN is a client) to hold onto, while waiting for
			Probe to complete, per Packet Engine while probing for backend
			services. <font color="#000000">Limit the number of client
			connections (SYN) waiting for status of probe system wide. Any new
			SYN packets will be dropped.</font></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>maxSynholdPerprobe &nbsp;<a href="#NetScalertcpparamv10.5-11.1-NetScaler-S"><sup>(1)</sup></a></p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>128</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>256</p>
		</td>
		<td style="border: none; padding: 0in"><p>128</p>
		</td>
		<td style="border: none; padding: 0in"><p>Maximum number<font color="#1f497d">
			of Client/SYN to hold, that is waiting for a Single Probe/backend.
			</font><font color="#000000">Any new SYN packets will be dropped.</font></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>maxTimeWaitConn</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>7000</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>?</p>
		</td>
		<td style="border: none; padding: 0in"><p>7000</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">Maximum
			number of connections to hold in the TCP TIME_WAIT state on a
			packet engine. New connections entering TIME_WAIT state are
			proactively cleaned up. This helps when the traffic has mostly
			very short lived connections reducing Memory usage.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">minRTO</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>1,000</p>
		</td>
		<td style="border: none; padding: 0in"><p>10</p>
		</td>
		<td style="border: none; padding: 0in"><p>64,000</p>
		</td>
		<td style="border: none; padding: 0in"><p>100</p>
		</td>
		<td style="border: none; padding: 0in"><p>Minimum re-transmission
			timeout, in milliseconds, specified in 10-millisecond increments
			(value must yield a whole number if divided by 10).&nbsp;</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">mptcpChecksum</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">ENABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">--</p>
		</td>
		<td style="border: none; padding: 0in"><p>Use MPTCP DSS checksum</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">mptcpCloseMptcpSessionOnLastSFClose</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">DISABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">--</p>
		</td>
		<td style="border: none; padding: 0in"><p>Allow to send DATA FIN or
			FAST CLOSE on mptcp connection while sending FIN or RST on the
			last subflow.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">mptcpConCloseOnPassiveSF</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">4</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">0</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">4</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">--</p>
		</td>
		<td style="border: none; padding: 0in"><p>Maximum number of subflow
			connections supported in pending join state per mptcp connection.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">mptcpImmediateSFCloseOnFIN</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">DISABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">--</p>
		</td>
		<td style="border: none; padding: 0in"><p>Allow subflows to close
			immediately on FIN before the DATA_FIN exchange is completed at
			mptcp level.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">mptcpMaxPendingSF</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">4</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">0</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">4</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">--</p>
		</td>
		<td style="border: none; padding: 0in"><p>Maximum number of subflow
			connections supported in pending join state per mptcp connection.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">mptcpMaxSF</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">4</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">2</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">6</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">--</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">Maximum
			number of subflow connections supported in established state per
			mptcp connection.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">mptcpPendingJoinThreshold</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">0</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">0</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">4294967294</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">--</p>
		</td>
		<td style="border: none; padding: 0in"><p>Maximum system level
			pending join connections allowed.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">mptcpRTOsToSwitchSF</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">2</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">1</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">6</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">--</p>
		</td>
		<td style="border: none; padding: 0in"><p>Number of RTO's at
			subflow level, after which MPCTP should start using other subflow.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">mptcpSFReplaceTimeout</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">31536000</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">--</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">The
			minimum idle time value in seconds for idle mptcp subflows after
			which the sublow is replaced by new incoming subflow if maximum
			subflow limit is reached. The priority for replacement is given to
			those subflow without any transaction</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">mptcpSFtimeout</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">0</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">31536000</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">--</p>
		</td>
		<td style="border: none; padding: 0in"><p>The timeout value in
			seconds for idle mptcp subflows. If this timeout is not set, idle
			subflows are cleared after cltTimeout of vserver</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">mptcpUseBackupOnDSS</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">DISABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">--</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">When
			enabled, if NS receives a DSS on a backup subflow, NS will start
			using that subflow to send data. And if disabled, NS will continue
			to transmit on current chosen subflow. In case there is some error
			on a subflow (like RTO's/RST etc.) then NS can choose a backup
			subflow irrespective of this tunable.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>mssLearnDelay&nbsp;<a href="#NetScalertcpparamv10.5-11.1-NetScaler-m"><sup>(2)</sup></a></p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>360000</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>1048576</p>
		</td>
		<td style="border: none; padding: 0in"><p>360000</p>
		</td>
		<td style="border: none; padding: 0in"><p>Vserver MSS learning
			delay</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>mssLearnInterval&nbsp;<a href="#NetScalertcpparamv10.5-11.1-NetScaler-m"><sup>(2)</sup></a></p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>18000</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>1048576</p>
		</td>
		<td style="border: none; padding: 0in"><p>18000</p>
		</td>
		<td style="border: none; padding: 0in"><p>Time period for which the
			backend service MSS are sampled for Vserver MSS learning</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>nagle</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>DISABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>DISABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable the
			Nagle algorithm on TCP connections.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>oooQSize</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>64</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p>65535</p>
		</td>
		<td style="border: none; padding: 0in"><p>64</p>
		</td>
		<td style="border: none; padding: 0in"><p>The maximum size of
			out-of-order packets queue. A value of 0 means infinite</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>pktPerRetx</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>100</p>
		</td>
		<td style="border: none; padding: 0in"><p>4</p>
		</td>
		<td style="border: none; padding: 0in"><p>The maximum limit on the
			number of packets that should be re-transmitted on receiving a
			partial ACK or SACK. If there are multiple packets outstanding,
			then send up to this number at the same time.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>recvBuffSize</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;11</p>
		</td>
		<td style="border: none; padding: 0in"><p>8190</p>
		</td>
		<td style="border: none; padding: 0in"><p>8190</p>
		</td>
		<td style="border: none; padding: 0in"><p>20,971,520</p>
		</td>
		<td style="border: none; padding: 0in"><p>65535</p>
		</td>
		<td style="border: none; padding: 0in"><p>TCP Receive buffer size.
			8190 is just too small for apps that cannot support Window
			Scaling. With WS enabled, you need a smaller WSVal.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>SACK</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>DISABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>ENABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable
			selective acknowledgement (SACK). This is a no-brainer.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>slowStartIncr</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>2</p>
		</td>
		<td style="border: none; padding: 0in"><p>1</p>
		</td>
		<td style="border: none; padding: 0in"><p>100</p>
		</td>
		<td style="border: none; padding: 0in"><p>4</p>
		</td>
		<td style="border: none; padding: 0in"><p>The multiplier that
			determines the rate at which slow start increases the size of the
			TCP transmission window after each acknowledgement of successful
			transmission.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">synAttackDetection</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">ENABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">ENABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p style="margin-bottom: 0.2in">
			Detect TCP SYN packet flood and send an SNMP trap.</p>
			<p>Possible values: ENABLED, DISABLED</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>synHoldFastGiveup &nbsp;<a href="#NetScalertcpparamv10.5-11.1-NetScaler-S"><sup>(1)</sup></a></p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p>1024</p>
		</td>
		<td style="border: none; padding: 0in"><p>256</p>
		</td>
		<td style="border: none; padding: 0in"><p>65535</p>
		</td>
		<td style="border: none; padding: 0in"><p>1024</p>
		</td>
		<td style="border: none; padding: 0in"><p>Max threshold after which
			NetScaler reduces the number of <font color="#1f497d">retransmission/retry
			of TCP SYN</font> for server probes from 7 to 3.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">tcpFastOpenCookieTimeout</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">11.1</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">0</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal"><font color="#666666">0</font></p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal"><font color="#666666">31536000</font></p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">0</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">Used
			with <a href="/display/NS/NetScaler+tcpprofile+v10.5+-+11.1#NetScalertcpprofilev10.5-11.1-NetScaler-tcpfastopen">tcpfastopen</a>
			tcpprofile argument. <font color="#666666">Timeout in seconds
			after which a new TFO Key is computed for generating TFO Cookie.
			If zero, the same key is used always. If timeout is less than
			120seconds, NS defaults to 120seconds timeout.</font></p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p class="msonormal">TcpMaxRetries</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">10.5</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;7</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;1</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">&nbsp;7</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">7</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">Number
			of RTO's after which a connection should be freed.</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>WS</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>DISABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>---</p>
		</td>
		<td style="border: none; padding: 0in"><p>ENABLED</p>
		</td>
		<td style="border: none; padding: 0in"><p>Enable or disable window
			scaling.&nbsp; If Disabled, Window Scaling is disabled for both
			sides of the conversation</p>
		</td>
	</tr>
	<tr>
		<td style="border: none; padding: 0in"><p>WSVal</p>
		</td>
		<td style="border: none; padding: 0in"><p class="msonormal">9</p>
		</td>
		<td style="border: none; padding: 0in"><p>4</p>
		</td>
		<td style="border: none; padding: 0in"><p>0</p>
		</td>
		<td style="border: none; padding: 0in"><p>8</p>
		</td>
		<td style="border: none; padding: 0in"><p>3</p>
		</td>
		<td style="border: none; padding: 0in"><p>The Scale Factor used to
			calculate the tcp window size. <a href="/pages/viewpage.action%3FpageId=100631907">Use
			this number to find the Scale Multiplier Value</a>. A value of 3
			is fine with a recvBuffSize that is &gt; 32768.</p>
		</td>
	</tr>
</table>


[(1)](#NetScalertcpparamv10.5-11.1-NetScaler-S)SynHold serves in case of wildcard configuration i.e. when the IP or Port or Both are defined as "\*"  and including VPN cases. In these cases, NetScaler will Probe (TCP 3-Way handshake) for the existence of the actual backend, based on the destination IP received from the client, and when the Probe is successful, will create an internal server for this destination and process the client.

 (2) When enabled, MSS learning is used to propagate the MSS of the most used services to the corresponding LB/CS vserver. many cases, not all the bound services are used evenly and hence the algorithm looks for the number of connections landing to particular service. The algorithm tracks the Load balancing decisions after every mssLearnDelay seconds for mssLearnInterval seconds and determines the mss for the vserver based on the LB decisions during this mssLearnInterval. this will repeat again after mssLearnDelay seconds. for example if mssLearnDelay is set to 55 minutes and mssLearnInterval set to 5 minutes, NS will monitor the LB decisions for these 5 minutes after every 55 minutes to determine the MSS for the vserver.

TCPParam NSCLI Commands

All arguments are specified to prevent changes with future software upgrades. 

**Show all current values**

sh ns tcpparam -format old -level verbose

**or**

sh ns tcpparam -level verbose

**Set all values to default**

set ns tcpParam -ackOnPush ENABLED -delayedAck 300 -downStateRST DISABLED -initialCwnd 4 -learnVsvrMSS DISABLED -limitedPersist ENABLED -maxBurst 6 -maxDynServerProbes 7 -maxPktPerMss 0 -maxSynhold 16384 -maxSynholdPerprobe 128 -maxTimeWaitConn 7000 -minRTO 1000 -mssLearnDelay 360000 -mssLearnInterval 18000 -nagle DISABLED -oooQSize 64 -pktPerRetx 1 -recvBuffSize 8190  -SACK DISABLED -slowStartIncr 2 -synHoldFastGiveup 1024 -WS Disabled -WSVal 4

**Set FDC tcpParam**

**V10.5 & V11**

set ns tcpParam -ackOnPush ENABLED -delayedAck 50 \-downStateRST ENABLED \-initialCwnd 6 -learnVsvrMSS DISABLED -limitedPersist ENABLED -maxBurst 6 -maxDynServerProbes 7 -maxPktPerMss 0 -maxSynhold 16384 -maxSynholdPerprobe 128 -maxTimeWaitConn 7000 -minRTO 100 \-mssLearnDelay 360000 -mssLearnInterval 18000 -nagle DISABLED -oooQSize 64 -pktPerRetx 4 -recvBuffSize 65535 -SACK ENABLED -slowStartIncr 4 -synHoldFastGiveup 1024 -WS ENABLED -WSVal 3

**Set a specific value**

set ns tcpParam -WS enabled