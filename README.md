ebusd - README
==============

Daemon to communicate with the heating system via ebus


Gerneral Information
====================

ebus (energy bus) is a serial communication protocoll used in heating systems. 

ebusd acts like a proxy. On the one hand it is listing on a TCP port 8888 for
incoming telnet connections. On the other hand it sends and receive ebus message
via ebus-usb adapter to or from the ebus.


Features:
=========

 * ebus - send and receive ebus commands
   - send Broadcast (BC), Master-Master (MM) and Master-Slave (MS) messages
   - calculate CRC for sending data and check CRC of received data
   - not allowed byte sequences inside data will be escaped and unescaped
   - it saved data from specified cycle messages
   
 * telnet - multi telnet session (listen on TCP port 8888)
   The available commands are different of each heating system and provider.
   This commands are defined in csv files which will be parsed on daemon startup.
   
   Commands:
   - get        send message (BC, MM or MS) to slave and decoded received answer
   - set        send data to the slave with the intention to adjust this value
   - cyc        return data from collected cycle messages
   - loglevel   change loglevel at runtime
   - quit       disconnect from daemon
   - shutdown   stop daemon
   

Build:
======

$ ./autogen.sh

This script execute the following commands in this order.

$ aclocal
$ autoconf
$ autoheader
$ automake
$ ./configure
$ make


Daemon Configuration:
=====================

The daemon will search ist configuration file ebusd.conf primary at /etc/ebusd.
This can be override by given commandline option -C. For detailed help of daemon
configuration try ./ebusd -h or take a look at ebusd.conf file.



Command Configuration:
======================

In short some description to the neccassarry structure of the csv files.

 * [    3] type     - type of command (get, set, cyc)
 * [    5] class    - comand class (ci, ...)
 * [   30] cmd      - command (password, ...)
 * [  256] com      - description of the command
 * [  int] s_type   - ebus message type (BC = 1, MM = 2 or MS = 3)
 * [    2] s_zz     - hex address from slave  (15)
 * [    4] s_cmd    - primary and secendory ebus command (e.g. B509)
 * [  int] s_len    - length of data bytes
 * [   32] s_msg    - data bytes of message
 * [  int] d_elem   - number of elements from different data types

For each different data type must defined the follow struture

 * [   20] d_sub    - subcommand (pin1, ...)
 * [    2] d_part   - part of message for data bytes (MD, SA, SD, MA)
 * [   10] d_pos    - position at data bytes (1, 2, 3, ...)
 * [    3] d_type   - data type (asc, bcd, ...))
 * [float] d_fac    - facter to scale data (1.0, 0.02, ...)
 * [    6] d_unit   - unit of data (°C, ...)
 * [   30] d_valid  - possible entrys (01, 02, 03, ...)
 * [  256] d_com    - description of sub command


Different data types [decode|encode]:

 * [de] x - asc - ascii
 * [de] 1 - bcd - bcd
 * [de] 1 - d1b - data1b
 * [de] 1 - d1c - data1c
 * [de] 2 - d2b - data2b
 * [de] 2 - d2c - data2c
 * [d ] 3 - bda - date (bcd format)
 * [de] 3 - hda - date (hex format)
 * [d ] 3 - bti - time (bcd format)
 * [de] 3 - hti - time (hex format)
 * [d ] 1 - bdy - day (bcd format)
 * [de] 1 - hdy - day (hex format)
 * [d ] x - hex - print data only with 2-nibble hex format
 * [d ] 4 - ulg - unsigned long



The author can be contacted at roland.jax@liwest.at.

