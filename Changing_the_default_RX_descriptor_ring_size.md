Changing the default RX descriptor ring size
============================================

The default Rx ring size is set as 512. However, you can use the nsif command through the NetScaler command line to change the Rx size to 1024 or 2048 at run time.

This will help eliminate drops in the RX ring caused by an overflow due to large numbers of small packets being received.

Items to look for in nsconmsg
=============================

nsconmsg -K /var/nslog/newnslog -d stats | grep 'nic\_err\_rx\\|netio\_tot'

nsconmsg -K /var/nslog/newnslog -d stats | grep 'nic\_err\_rx'

To monitor with snmp monitor the IfErrRxNoBuffs and ifErrTxNoNSB oids.