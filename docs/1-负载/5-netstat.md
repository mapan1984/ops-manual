# 1.4 网络

## /proc/net/netstat

    $ cat /proc/net/netstat
    TcpExt: SyncookiesSent SyncookiesRecv SyncookiesFailed EmbryonicRsts PruneCalled RcvPruned OfoPruned OutOfWindowIcmps LockDroppedIcmps ArpFilter TW TWRecycled TWKilled PAWSPassive PAWSActive PAWSEstab DelayedACKs DelayedACKLocked DelayedACKLost ListenOverflows ListenDrops TCPPrequeued TCPDirectCopyFromBacklog TCPDirectCopyFromPrequeue TCPPrequeueDropped TCPHPHits TCPHPHitsToUser TCPPureAcks TCPHPAcks TCPRenoRecovery TCPSackRecovery TCPSACKReneging TCPFACKReorder TCPSACKReorder TCPRenoReorder TCPTSReorder TCPFullUndo TCPPartialUndo TCPDSACKUndo TCPLossUndo TCPLostRetransmit TCPRenoFailures TCPSackFailures TCPLossFailures TCPFastRetrans TCPForwardRetrans TCPSlowStartRetrans TCPTimeouts TCPLossProbes TCPLossProbeRecovery TCPRenoRecoveryFail TCPSackRecoveryFail TCPSchedulerFailed TCPRcvCollapsed TCPDSACKOldSent TCPDSACKOfoSent TCPDSACKRecv TCPDSACKOfoRecv TCPAbortOnData TCPAbortOnClose TCPAbortOnMemory TCPAbortOnTimeout TCPAbortOnLinger TCPAbortFailed TCPMemoryPressures TCPSACKDiscard TCPDSACKIgnoredOld TCPDSACKIgnoredNoUndo TCPSpuriousRTOs TCPMD5NotFound TCPMD5Unexpected TCPSackShifted TCPSackMerged TCPSackShiftFallback TCPBacklogDrop PFMemallocDrop TCPMinTTLDrop TCPDeferAcceptDrop IPReversePathFilter TCPTimeWaitOverflow TCPReqQFullDoCookies TCPReqQFullDrop TCPRetransFail TCPRcvCoalesce TCPOFOQueue TCPOFODrop TCPOFOMerge TCPChallengeACK TCPSYNChallenge TCPFastOpenActive TCPFastOpenActiveFail TCPFastOpenPassive TCPFastOpenPassiveFail TCPFastOpenListenOverflow TCPFastOpenCookieReqd TCPSpuriousRtxHostQueues BusyPollRxPackets TCPAutoCorking TCPFromZeroWindowAdv TCPToZeroWindowAdv TCPWantZeroWindowAdv TCPSynRetrans TCPOrigDataSent TCPHystartTrainDetect TCPHystartTrainCwnd TCPHystartDelayDetect TCPHystartDelayCwnd TCPACKSkippedSynRecv TCPACKSkippedPAWS TCPACKSkippedSeq TCPACKSkippedFinWait2 TCPACKSkippedTimeWait TCPACKSkippedChallenge TCPWqueueTooBig
    TcpExt: 251 672 234 478 64 0 0 0 0 0 432594 0 0 0 0 0 13947251 548 3403 19270 19524 62307000 144918 1357582392 0 26224904 62024368 1518992 95084835 3 53 1 0 0 0 1 3 1 1 15883 5 0 47 3 198 15 287 16591 3090 2109 0 8 0 31610 3436 9 389 0 768 11401 0 24 0 0 0 0 0 192 1 0 0 0 0 309 0 0 0 0 1 0 251 0 0 99393 137 0 9 4 0 0 0 0 0 0 0 0 0 0 60 60 83 17103 133748943 5 138 0 0 0 0 8 0 0 0 0
    IpExt: InNoRoutes InTruncatedPkts InMcastPkts OutMcastPkts InBcastPkts OutBcastPkts InOctets OutOctets InMcastOctets OutMcastOctets InBcastOctets OutBcastOctets InCsumErrors InNoECTPkts InECT1Pkts InECT0Pkts InCEPkts ReasmOverlaps
    IpExt: 2 0 0 0 0 0 12519256155 12032704265 0 0 0 0 0 167570207 0 724 7 0

### 字段解释

    * EmbryonicRsts

## /proc/net/snmp

    $ cat /proc/net/snmp
    Ip: Forwarding DefaultTTL InReceives InHdrErrors InAddrErrors ForwDatagrams InUnknownProtos InDiscards InDelivers OutRequests OutDiscards OutNoRoutes ReasmTimeout ReasmReqds ReasmOKs ReasmFails FragOKs FragFails FragCreates
    Ip: 2 64 167275177 0 0 0 4 0 167275170 168684126 0 0 0 0 0 0 0 0 0
    Icmp: InMsgs InErrors InCsumErrors InDestUnreachs InTimeExcds InParmProbs InSrcQuenchs InRedirects InEchos InEchoReps InTimestamps InTimestampReps InAddrMasks InAddrMaskReps OutMsgs OutErrors OutDestUnreachs OutTimeExcds OutParmProbs OutSrcQuenchs OutRedirects OutEchos OutEchoReps OutTimestamps OutTimestampReps OutAddrMasks OutAddrMaskReps
    Icmp: 7363 91 0 94 1 0 0 0 7268 0 0 0 0 0 13880 0 6612 0 0 0 0 0 7268 0 0 0 0
    IcmpMsg: InType3 InType8 InType11 OutType0 OutType3
    IcmpMsg: 94 7268 1 7268 6612
    Tcp: RtoAlgorithm RtoMin RtoMax MaxConn ActiveOpens PassiveOpens AttemptFails EstabResets CurrEstab InSegs OutSegs RetransSegs InErrs OutRsts InCsumErrors
    Tcp: 1 200 120000 -1 458929 435474 526 770 137 167230085 168620207 20695 39 123048 39
    Udp: InDatagrams NoPorts InErrors OutDatagrams RcvbufErrors SndbufErrors InCsumErrors
    Udp: 30002 7855 0 30001 0 0 0
    UdpLite: InDatagrams NoPorts InErrors OutDatagrams RcvbufErrors SndbufErrors InCsumErrors
    UdpLite: 0 0 0 0 0 0 0

### 字段解释

* 常量
    * RtoAlgorithm
    * RtoMin
    * RtoMax
    * MaxConn
* 连接统计
    * ActiveOpens: 主动连接次数，CLOSE => SYN-SENT 次数
    * PassiveOpens: 被动连接次数，RFC原意是LISTEN => SYN-RECV次数，但Linux选择在三次握手成功后才加 1
    * AttemptFails: 连接失败次数
    * EstabResets: 连接被reset次数，ESTABLISHED => CLOSE次数 + CLOSE-WAIT => CLOSE次数
    * CurrEstab: 当前 TCP 连接数，Established 个数 + CloseWait 个数
* 数据包统计
    * InSegs: 接收 Tcp 数据包的数量（包括错误包），累积值，计算周期差值/周期时长，可得 Tcp 包接收速率
    * OutSegs: 发送 Tcp 数据包的数量，累积值，计算周期差值/周期时长，可得 Tcp 包发送速率
    * RetransSegs: 重传包个数
    * InErrs: 接收错误 Tcp 包的数量，累积值，计算周期差值/周期时长，可得 Tcp 错误包接收速率
    * OutRsts: 发送带有 reset 标记的包个数
    * InCsumErrors: 收到有 checksum 有问题的包个数，InErrs 中应该只有 *小部分* 属于改类型


## /proc/net/sockstat /proc/net/sockstat6

    $ cat /proc/net/sockstat
    sockets: used 1180
    TCP: inuse 9 orphan 0 tw 5 alloc 996 mem 1097
    UDP: inuse 2 mem 1
    UDPLITE: inuse 0
    RAW: inuse 0
    FRAG: inuse 0 memory 0

    $ cat /proc/net/sockstat6
    TCP6: inuse 979
    UDP6: inuse 1
    UDPLITE6: inuse 0
    RAW6: inuse 0
    FRAG6: inuse 0 memory 0

`ss` 命令解析这 2 个文件

    $ ss -s
    Total: 1180 (kernel 1425)
    TCP:   1001 (estab 974, closed 13, orphaned 0, synrecv 0, timewait 5/0), ports 0
    
    Transport Total     IP        IPv6
    *      1425      -         -
    RAW      0         0         0
    UDP      3         2         1
    TCP      988       9         979
    INET      991       11        980
    FRAG      0         0         0

``` c
// https://git.kernel.org/pub/scm/network/iproute2/iproute2.git/tree/misc/ss.c?h=v5.5.0#n4935
static int print_summary(void)
{
	struct ssummary s;
	int tcp_estab;

	if (get_sockstat(&s) < 0)
		perror("ss: get_sockstat");
	if (get_snmp_int("Tcp:", "CurrEstab", &tcp_estab) < 0)
		perror("ss: get_snmpstat");

	printf("Total: %d\n", s.socks);

	printf("TCP:   %d (estab %d, closed %d, orphaned %d, timewait %d)\n",
	       s.tcp_total + s.tcp_tws, tcp_estab,
	       s.tcp_total - (s.tcp4_hashed + s.tcp6_hashed - s.tcp_tws),
	       s.tcp_orphans, s.tcp_tws);

	printf("\n");
	printf("Transport Total     IP        IPv6\n");
	printf("RAW	  %-9d %-9d %-9d\n", s.raw4+s.raw6, s.raw4, s.raw6);
	printf("UDP	  %-9d %-9d %-9d\n", s.udp4+s.udp6, s.udp4, s.udp6);
	printf("TCP	  %-9d %-9d %-9d\n", s.tcp4_hashed+s.tcp6_hashed, s.tcp4_hashed, s.tcp6_hashed);
	printf("INET	  %-9d %-9d %-9d\n",
	       s.raw4+s.udp4+s.tcp4_hashed+
	       s.raw6+s.udp6+s.tcp6_hashed,
	       s.raw4+s.udp4+s.tcp4_hashed,
	       s.raw6+s.udp6+s.tcp6_hashed);
	printf("FRAG	  %-9d %-9d %-9d\n", s.frag4+s.frag6, s.frag4, s.frag6);

	printf("\n");

	return 0;
}
```

## 参考

- https://github.com/prometheus/node_exporter/blob/v1.3.1/collector/netstat_linux.go
- https://perthcharles.github.io/2015/11/10/wiki-netstat-proc/
- https://adil.medium.com/examination-of-the-closed-column-of-ss-command-d6f21aeaa5e1
