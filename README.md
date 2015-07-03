# dvb-mpe-encode
Small program for DVB MPE encoding of IP datagrams

Creates a tun interface and reads IP datagrams, encapsulating them in DVB/MPE DSM-CC sections and outputting them via standard output.

Puts the correct MAC address on for multicast datagrams.

Uses the broadcast MAC address for unicast.
