<img src="kcp-go.png" alt="kcp-go" height="50px" />


[![GoDoc][1]][2] [![Powered][9]][10] [![MIT licensed][11]][12] [![Build Status][3]][4] [![Go Report Card][5]][6] [![Coverage Statusd][7]][8]

[1]: https://godoc.org/github.com/xtaci/kcp-go?status.svg
[2]: https://godoc.org/github.com/xtaci/kcp-go
[3]: https://travis-ci.org/xtaci/kcp-go.svg?branch=master
[4]: https://travis-ci.org/xtaci/kcp-go
[5]: https://goreportcard.com/badge/github.com/xtaci/kcp-go
[6]: https://goreportcard.com/report/github.com/xtaci/kcp-go
[7]: https://codecov.io/gh/xtaci/kcp-go/branch/master/graph/badge.svg
[8]: https://codecov.io/gh/xtaci/kcp-go
[9]: https://img.shields.io/badge/KCP-Powered-blue.svg
[10]: https://github.com/skywind3000/kcp
[11]: https://img.shields.io/badge/license-MIT-blue.svg
[12]: LICENSE

## Introduction

**kcp-go** is a **Production-Grade Reliable-UDP** library for [golang](https://golang.org/). 

This library intents to provide a **smooth, resilient, ordered, error-checked and anonymous** delivery of streams over **UDP** packets, it has been battle-tested with opensource project [kcptun](https://github.com/xtaci/kcptun). Millions of devices(from low-end MIPS routers to high-end servers) have deployed **kcp-go** powered program in a variety of forms like **online games, live broadcasting, file synchronization and network acceleration**.

[Lastest Release](https://github.com/xtaci/kcp-go/releases)

## Features

1. Designed for **Latency-sensitive** scenarios.
1. **Cache friendly** and **Memory optimized** design, offers extremely **High Performance** core.
1. Handles **>5K concurrent connections** on a single commodity server.
1. Compatible with [net.Conn](https://golang.org/pkg/net/#Conn) and [net.Listener](https://golang.org/pkg/net/#Listener), a drop-in replacement for [net.TCPConn](https://golang.org/pkg/net/#TCPConn).
1. [FEC(Forward Error Correction)](https://en.wikipedia.org/wiki/Forward_error_correction) Support with [Reed-Solomon Codes](https://en.wikipedia.org/wiki/Reed%E2%80%93Solomon_error_correction)
1. Packet level encryption support with [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), [TEA](https://en.wikipedia.org/wiki/Tiny_Encryption_Algorithm), [3DES](https://en.wikipedia.org/wiki/Triple_DES), [Blowfish](https://en.wikipedia.org/wiki/Blowfish_(cipher)), [Cast5](https://en.wikipedia.org/wiki/CAST-128), [Salsa20]( https://en.wikipedia.org/wiki/Salsa20), etc. in [CFB](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher_Feedback_.28CFB.29) mode, which generates completely anonymous packet.
1. Only **A fixed number of goroutines** will be created for the entire server application, costs in **context switch** between goroutines have been taken into consideration.
1. Compatible with [skywind3000's](https://github.com/skywind3000) C version with various improvements.

## Documentation

For complete documentation, see the associated [Godoc](https://godoc.org/github.com/xtaci/kcp-go).

## Specification

<img src="frame.png" alt="Frame Format" height="109px" />

```
+-----------------+
| SESSION         |
+-----------------+
| KCP(ARQ)        |
+-----------------+
| FEC(OPTIONAL)   |
+-----------------+
| CRYPTO(OPTIONAL)|
+-----------------+
| UDP(PACKET)     |
+-----------------+
| IP              |
+-----------------+
| LINK            |
+-----------------+
| PHY             |
+-----------------+
(LAYER MODEL OF KCP-GO)
```


## Usage

Client:   [full demo](https://github.com/xtaci/kcptun/blob/master/client/main.go)
```go
kcpconn, err := kcp.DialWithOptions("192.168.0.1:10000", nil, 10, 3)
```
Server:   [full demo](https://github.com/xtaci/kcptun/blob/master/server/main.go)
```go
lis, err := kcp.ListenWithOptions(":10000", nil, 10, 3)
```

## Benchmark
```
  Model Name:	MacBook Pro
  Model Identifier:	MacBookPro14,1
  Processor Name:	Intel Core i5
  Processor Speed:	3.1 GHz
  Number of Processors:	1
  Total Number of Cores:	2
  L2 Cache (per Core):	256 KB
  L3 Cache:	4 MB
  Memory:	8 GB
```
```
$ go test -v -run=^$ -bench .
beginning tests, encryption:salsa20, fec:10/3
goos: darwin
goarch: amd64
pkg: github.com/xtaci/kcp-go
BenchmarkSM4-4                 	   50000	     34575 ns/op	  86.77 MB/s	       0 B/op	       0 allocs/op
BenchmarkAES128-4              	  300000	      3990 ns/op	 751.88 MB/s	       0 B/op	       0 allocs/op
BenchmarkAES192-4              	  300000	      4094 ns/op	 732.67 MB/s	       0 B/op	       0 allocs/op
BenchmarkAES256-4              	  300000	      4384 ns/op	 684.24 MB/s	       0 B/op	       0 allocs/op
BenchmarkTEA-4                 	  100000	     16178 ns/op	 185.43 MB/s	       0 B/op	       0 allocs/op
BenchmarkXOR-4                 	20000000	        89.6 ns/op	33477.09 MB/s	       0 B/op	       0 allocs/op
BenchmarkBlowfish-4            	   50000	     27717 ns/op	 108.23 MB/s	       0 B/op	       0 allocs/op
BenchmarkNone-4                	30000000	        45.5 ns/op	65971.84 MB/s	       0 B/op	       0 allocs/op
BenchmarkCast5-4               	   50000	     35501 ns/op	  84.50 MB/s	       0 B/op	       0 allocs/op
Benchmark3DES-4                	   10000	    119019 ns/op	  25.21 MB/s	       0 B/op	       0 allocs/op
BenchmarkTwofish-4             	   30000	     40286 ns/op	  74.47 MB/s	       0 B/op	       0 allocs/op
BenchmarkXTEA-4                	   30000	     47714 ns/op	  62.87 MB/s	       0 B/op	       0 allocs/op
BenchmarkSalsa20-4             	  500000	      3263 ns/op	 919.15 MB/s	       0 B/op	       0 allocs/op
BenchmarkCRC32-4               	20000000	        66.1 ns/op	15497.83 MB/s
BenchmarkCsprngSystem-4        	 1000000	      1150 ns/op	  13.91 MB/s
BenchmarkCsprngMD5-4           	10000000	       146 ns/op	 109.42 MB/s
BenchmarkCsprngSHA1-4          	10000000	       161 ns/op	 123.92 MB/s
BenchmarkCsprngNonceMD5-4      	10000000	       153 ns/op	 104.27 MB/s
BenchmarkCsprngNonceAES128-4   	100000000	        18.9 ns/op	 847.36 MB/s
BenchmarkFlush-4               	10000000	       237 ns/op	       0 B/op	       0 allocs/op
BenchmarkEchoSpeed4K-4         	    5000	    234518 ns/op	  17.47 MB/s	    5474 B/op	     149 allocs/op
BenchmarkEchoSpeed64K-4        	    1000	   1594354 ns/op	  41.11 MB/s	   55551 B/op	    1611 allocs/op
BenchmarkEchoSpeed512K-4       	     100	  12218965 ns/op	  42.91 MB/s	  490837 B/op	   12589 allocs/op
BenchmarkEchoSpeed1M-4         	      50	  27100728 ns/op	  38.69 MB/s	  929398 B/op	   24573 allocs/op
BenchmarkSinkSpeed4K-4         	   50000	     30076 ns/op	 136.19 MB/s	    1349 B/op	      29 allocs/op
BenchmarkSinkSpeed64K-4        	    5000	    311182 ns/op	 210.60 MB/s	   21037 B/op	     453 allocs/op
BenchmarkSinkSpeed256K-4       	    1000	   2518828 ns/op	 208.15 MB/s	  154623 B/op	    3511 allocs/op
BenchmarkSinkSpeed1M-4         	     200	   6019194 ns/op	 174.21 MB/s	  289861 B/op	    6890 allocs/op
PASS
ok  	github.com/xtaci/kcp-go	49.441s
```

## Key Design Considerations

1. slice vs. container/list

`kcp.flush()` loops through the send queue for retransmission checking for every 20ms(interval).

I've wrote a benchmark for comparing sequential loop through *slice* and *container/list* here:

https://github.com/xtaci/notes/blob/master/golang/benchmark2/cachemiss_test.go

```
BenchmarkLoopSlice-4   	2000000000	         0.39 ns/op
BenchmarkLoopList-4    	100000000	        54.6 ns/op
```

List structure introduces **heavy cache misses** compared to slice which owns better **locality**, 5000 connections with 32 window size and 20ms interval will cost 6us/0.03%(cpu) using slice, and 8.7ms/43.5%(cpu) for list for each `kcp.flush()`.

2. Timing accuracy vs. syscall clock_gettime

Timing is **critical** to **RTT estimator**, inaccurate timing leads to false retransmissions in KCP, but calling `time.Now()` costs 42 cycles(10.5ns on 4GHz CPU, 15.6ns on my MacBook Pro 2.7GHz). 

The benchmark for time.Now() lies here:

https://github.com/xtaci/notes/blob/master/golang/benchmark2/syscall_test.go

```
BenchmarkNow-4         	100000000	        15.6 ns/op
```

In kcp-go, after each `kcp.output()` function call, current clock time will be updated upon return, and for a single `kcp.flush()` operation, current time will be queried from system once. For most of the time, 5000 connections costs 5000 * 15.6ns = 78us(a fixed cost while no packet needs to be sent), as for 10MB/s data transfering with 1400 MTU, `kcp.output()` will be called around 7500 times and costs 117us for `time.Now()` in **every second**.

## Connection Termination

Control messages like **SYN/FIN/RST** in TCP **are not defined** in KCP, you need some **keepalive/heartbeat mechanism** in the application-level. A real world example is to use some **multiplexing** protocol over session, such as [smux](https://github.com/xtaci/smux)(with embedded keepalive mechanism), see [kcptun](https://github.com/xtaci/kcptun) for example.

## FAQ

Q: I'm handling >5K connections on my server, the CPU utilization is so high.

A: A standalone `agent` or `gate` server for running kcp-go is suggested, not only for CPU utilization, but also important to the **precision** of RTT measurements(timing) which indirectly affects retransmission. By increasing update `interval` with `SetNoDelay` like `conn.SetNoDelay(1, 40, 1, 1)` will dramatically reduce system load, but lower the performance.

## Who is using this?

1. https://github.com/xtaci/kcptun -- A Secure Tunnel Based On KCP over UDP.
2. https://github.com/getlantern/lantern -- Lantern delivers fast access to the open Internet. 
3. https://github.com/smallnest/rpcx -- A RPC service framework based on net/rpc like alibaba Dubbo and weibo Motan.
4. https://github.com/gonet2/agent -- A gateway for games with stream multiplexing.
5. https://github.com/syncthing/syncthing -- Open Source Continuous File Synchronization.
6. https://play.google.com/store/apps/details?id=com.k17game.k3 -- Battle Zone - Earth 2048, a world-wide strategy game.

## Links

1. https://github.com/xtaci/libkcp -- FEC enhanced KCP session library for iOS/Android in C++
2. https://github.com/skywind3000/kcp -- A Fast and Reliable ARQ Protocol
3. https://github.com/klauspost/reedsolomon -- Reed-Solomon Erasure Coding in Go
