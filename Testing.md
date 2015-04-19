# Introduction #

This relies on tools from the [JustDVb-It](http://www.cineca.tv/labs/mhplab/JustDVb-It%202.0.html), [dvbsnoop](http://dvbsnoop.sourceforge.net/) and [emcast](http://www.gizmolabs.org/~dhelder/junglemonkey/emcast/) projects.

# Details #

In one text window run a pipeline from mpe to dvbsnoop via sec2ts:

```
mpe dvb0 | sec2ts 430 | dvbsnoop -s ts -if - -tssubdecode -softcrc 430
```

In another text window configure the ip interface and run emcast:

```
ifconfig dvb0 192.168.9.1 netmask 255.255.255.0 up
route add -net 224.0.0.0 netmask 224.0.0.0 dvb0
emcast 224.0.1.20:9999
hello
world
```

# What's going on #

mpe creates a tun device called dvb0 and reads ip datagrams from it, encapsulates them in the MPE compliant DSM-CC section and outputs this to standard output.

sec2ts (from the JustDVb-It suite) reads sections from standard input and sends transport packets with the specified PID to standard output.

dvbsnoop decodes the transport stream.

The dvbsnoop parameters are as follows:

|-s ts|expect the input to be a transport stream|
|:----|:----------------------------------------|
|-if -|read from standard input|
|-tssubdecode|decode the contents of the transport stream|
|-softcrc|run the crc on the decoded sections|
|430|decode this PID|

ifconfig brings the tun interface up. The address is not important - a point-to-point might make more sense.

route directs all multicast to dvb0. Really we should be using the multicast routing table for this, but it works this way.

emcast joins the specified group and sends standard input to the specified port on the group.

# Expected Results #

We should see the decoded IP datagrams from dvbsnoop. emcast will join the group and we should see this as a datagram to 224.0.0.22. dvbsnoop sometimes runs behind on its decodes so we may not see the decode for the second line of text.

Check that the MAC address is correctly filled in.