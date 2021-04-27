## NAME

bwping  and  bwping6  are  tools  to  measure  bandwidth and response times
between  two  hosts  using  Internet  Control  Message Protocol (ICMP) echo
request/echo  reply  mechanism.  bwping  deals  with  IPv4  networks, while
bwping6 - with IPv6 networks.

## SYNOPSIS

```
bwping [ -4 | -6 ] [ -u buf_size ] [ -r reporting_period ]
       [ -T tos(v4) | traf_class(v6) ] [ -B bind_addr ]
       -b kbps -s pkt_size -v volume target
```

```
bwping6 [ -4 | -6 ] [ -u buf_size ] [ -r reporting_period ]
        [ -T tos(v4) | traf_class(v6) ] [ -B bind_addr ]
        -b kbps -s pkt_size -v volume target
```

## OPTIONS

```
-4
```

Forces IPv4 mode. Default mode of operation is IPv4 for bwping and IPv6 for
bwping6 otherwise.

```
-6
```

Forces IPv6 mode. Default mode of operation is IPv4 for bwping and IPv6 for
bwping6 otherwise.

```
-u buf_size
```

Sets the size of the socket send/receive buffer in bytes. If not specified,
the  system  default  will  be  used.  Tune  this  parameter  if  the speed
measurement results are unexpectedly low or packet loss occurs.

```
-r reporting_period
```

Sets   the   interval  time in seconds between periodic bandwidth, RTT, and
loss  reports.  If  zero,  there  will be no periodic reports (default).

```
-T tos(v4) | traf_class(v6)
```

Sets  the  TOS  (in  IPv4  mode)  or  Traffic Class (in IPv6 mode) value of
outgoing ip packets. Default value is zero.

```
-B bind_addr
```

Sets   the  source  address  of outgoing ip packets. By default the address
of the outgoing interface will be used.

```
-b kbps
```

Sets the transfer speed in kilobits per second.

```
-s pkt_size
```

Sets the size of ICMP packet (excluding IPv4/IPv6 header) in bytes.

```
-v volume
```

Sets the volume to transfer in bytes.

## AUTHORS

Oleg Derevenetz <oleg.derevenetz@gmail.com>

## BUGS

[![Build Status](https://github.com/oleg-derevenetz/bwping/workflows/Build/badge.svg)](https://github.com/oleg-derevenetz/bwping/actions?query=workflow%3A%22Build%22)
[![CodeQL Analysis Status](https://github.com/oleg-derevenetz/bwping/workflows/CodeQL%20Analysis/badge.svg)](https://github.com/oleg-derevenetz/bwping/actions?query=workflow%3A%22CodeQL%20Analysis%22)
[![Coverity Scan Status](https://scan.coverity.com/projects/20880/badge.svg)](https://scan.coverity.com/projects/oleg-derevenetz-bwping)
[![SonarCloud Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=oleg-derevenetz_bwping&metric=alert_status)](https://sonarcloud.io/dashboard?id=oleg-derevenetz_bwping)

[![SonarCloud Reliability Rating](https://sonarcloud.io/api/project_badges/measure?project=oleg-derevenetz_bwping&metric=reliability_rating)](https://sonarcloud.io/dashboard?id=oleg-derevenetz_bwping)
[![SonarCloud Security Rating](https://sonarcloud.io/api/project_badges/measure?project=oleg-derevenetz_bwping&metric=security_rating)](https://sonarcloud.io/dashboard?id=oleg-derevenetz_bwping)
[![SonarCloud Maintainability Rating](https://sonarcloud.io/api/project_badges/measure?project=oleg-derevenetz_bwping&metric=sqale_rating)](https://sonarcloud.io/dashboard?id=oleg-derevenetz_bwping)

[![SonarCloud Bugs](https://sonarcloud.io/api/project_badges/measure?project=oleg-derevenetz_bwping&metric=bugs)](https://sonarcloud.io/dashboard?id=oleg-derevenetz_bwping)
[![SonarCloud Vulnerabilities](https://sonarcloud.io/api/project_badges/measure?project=oleg-derevenetz_bwping&metric=vulnerabilities)](https://sonarcloud.io/dashboard?id=oleg-derevenetz_bwping)
[![SonarCloud Code Smells](https://sonarcloud.io/api/project_badges/measure?project=oleg-derevenetz_bwping&metric=code_smells)](https://sonarcloud.io/dashboard?id=oleg-derevenetz_bwping)

## NOTES

These tools use raw sockets to deal with ICMP messages, so they may require
root privileges or extended  capabilities (such as CAP_NET_RAW on Linux) to
run. They can also be run as setuid root.

Although  bwping  and  bwping6 does not require any special software on the
remote  host  (only the ability to respond on ICMP echo request  messages),
there  are  some  special requirements to network infrastructure, local and
remote host performance:

1.  There  should  be  no ICMP echo request/reply filtering on the network.
This includes QoS mechanisms (which often affects ICMP) at any point in the
testing path;

1.  Local  host  should  have  enough  CPU  resources  to  send  ICMP  echo
request   messages   with   given   rate,  and  remote  host should quickly
respond  on  these  messages  and should have no  ICMP  bandwidth  limiting
turned on.

If  some  of  these  requirements  are  not  satisfied then the measurement
results  will  be  inadequate  or  fail completely. In general, for testing
bandwidth  where  QoS is implemented, always test with traffic that matches
the QoS class to be tested.

## Difference from native ping

Possible difference in reported time can be caused by timers used by ping and bwping. 
 - ping may use some coarser timer (for example, gettimeofday() instead of clock_gettime() 
   or different timer for clock_gettime().
 - ping may use sendto()/recvfrom() calls while bwping uses more efficient 
   sendmmsg()/recvmmsg() calls (if they are available), 
   this can have an impact due to the different speed of processing 
   these calls within the kernel.
 - bwping uses additional BPF-based kernel-level filtering (if available) 
   to quickly filter ICMP packets which are not related to a particular bwping process. 
   This can also have an impact if there is many ICMP traffic on this host. 
   ping most likely does not have such a filter and therefore 
   is forced to receive extra ICMP packets and throw away unrelated ones in user mode, 
   which may be more costly.

May be other reasons as well, i.e routing.
