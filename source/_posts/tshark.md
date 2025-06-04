---
title: tshark
date: 2025-06-03 02:13:48
tags:
---

TShark Help

    tshark -h


where tshark

    which tshark

interfaces List

    tshark -D

list the Link-layer from interface

    tshark -L
    tshark -i ens33 -L

switch interface

    tshark -i 2

Selecting an Interface & Writing to a File

    tshark -i eth0 -w /tmp/test.pcap

Selecting an Interface & Applying Filters

    tshark -i eth0 -f "host 172.16.146.2"

|Switch Command	|Result|
|-----------|------------|
|D |	Will display any interfaces available to capture from and then exit out.|
|L	|Will list the Link-layer mediums you can capture from and then exit out. (ethernet as an example)|
|i	|choose an interface to capture from. (-i eth0)|
|f	|packet filter in libpcap syntax. Used during capture.|
|c	|Grab a specific number of packets, then quit the program. Defines a stop condition.|
|a	|Defines an autostop condition. Can be after a duration, specific file size, or after a certain number of packets.|
|r |(pcap-file)	Read from a file.|
|W |(pcap-file)	Write into a file using the pcapng format.|
|P	|Will print the packet summary while writing into a file (-W)|
|x	|will add Hex and ASCII output into the capture.|
|h	|See the help menu|