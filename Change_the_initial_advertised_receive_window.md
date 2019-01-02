Change the initial advertised receive window v10.5 & up
============================================

The NetScaler always starts with a initial receive window (rwin) of 8190. This is fine, except if the peer does not have SACK, or they want to send a burst of packets. With a rwin of 8190, the peer can only send 5 full packets and then has to wait for a ACK before proceeding. With a rwin of 65535, that number is boosted to 44.

The only way to change the initial advertised window on the NetScaler is through nsapimgr.  You will have to put this in rc.netscaler to persist across reboots.

10:28:29.048875 IP 10.102.34.158.13293 > 10.102.34.92.http: S 1602311648:1602311648(0) **win 8190** <mss 1440>

10:28:29.051570 IP 10.102.34.92.http > 10.102.34.158.13293: S 3881786539:3881786539(0) ack 1602311649 win 5840 <mss 1460>

root@ns# nsapimgr\_wr.sh -ys advertisedwindow=16000

Number of PEs running: 1

Changing TCP advertised window from 8190 to 16000 bytes ...  Done.

10:29:22.745519 IP 10.102.34.158.6174 > 10.102.34.92.http: S 1603164009:1603164009(0) win **16000** <mss 1440>

10:29:22.745540 IP 10.102.34.92.http > 10.102.34.158.6174: S 4088133850:4088133850(0) ack 1603164010 win 5840 <mss 1460>

