# TCP Retransmissions

TCP has two different mechanisms to initiate retransmissions, one based on timeouts and other based on inferring losses by the acknowledgment structure. TCP sets a timer when it sends data if the timer expires and the data is not acknowledged a timer-based retransmission occurs. A fast retransmission happens without a delay when the cumulative acknowledgment doesn't advance or when receiving a segment with a SACK block.

## Basic concepts

### Retransmission Timeout (RTO)

TCP keeps track of the Round-Trip Time (RTT) using the sequence numbers and acknowledgment numbers. The challenge is to set a good  RTO estimate based on the RTT measurements. Setting the RTO too low would generate spurious retransmissions, setting it too high would underutilize the network.

### Clock granularity

The TCP clock doesn't have infinite precision and doesn't reflect the real system clock. Usually, the TCP clock is a variable that is updated as the system clock advancesi not necessarily one-to-one. The time it takes to the TCP clock to advance is called its granularity. Modern implementations use a clock granularity in the order of milliseconds or tens of milliseconds.

### Karn's Algorithm

The retransmission ambiguity problem is as follow:
The sender retransmits a segment and receives the acknowledgment. Is the acknowledging the original packet? Is for the retransmitted segment? The Karn's algorithm solves the problem for the RTO calculation and involves two steps:
1. Ignore the acknowledgment of a retransmitted packet for RTO calculations.
2. Apply a backoff factor for each subsequent retransmission. Double the RTO every time a segment is retransmitted. Return the RTO to normal when the sender receives an acknowledgment for a non-retransmitted packet.

### Round-Trip Time Calculation Using Timestamps TCP Option (TSOPT)

The TSOPT allows measuring the RTT continuously. The sender sets its TCP clock in the TSV field the receiver echoes this value in the TSER field for packets which contain data (segments with the SYN and FIN flags count as data). The sender can measure the RTT using its current TCP clock and the TSER in the received packet. The process seems simple enough but is not as straightforward. There might be packet loss, reordering, and retransmissions, also TCP usually acknowledge every other segment. Most implementations use the following algorithm to calculate the RTT:
1. The sender sets its TCP clock in the TSV field of the TSOPT in every TCP segment.
2. The receiver keeps track of the most recent TSV value to send in the next acknowledgment.
3. Whenever a new in-order segment arrives, the TSV value to send is updated.
4. When the receiver generates an acknowledgment with TSOPT the receiver set the TSER value to the last valid TSV from the sender.
5. The sender receiving the acknowledgment substracts the TSER from its TCP clock to calculate the RTT.

The TSOPT algorithm works even in cases of reordering and retransmissions:
- **Out-of-order segments:** When a receiver receives an out-of-order segment it will generate an acknowledgment immediately to trigger the fast retransmit at the sender. This ACK includes as its TSER value the TSV of the most recent in-order segment. This usually causes the sender to increase the RTT measure, therefore increase its RTO.
- **Retransmission:** When a receiver receives a retransmitted segment the acknowledge generated includes as its TSER value the TSV of the retransmitted segment. This causes the RTT value on the sender to go down, therefore decrease its RTO.

When using the TSOPT to calculate the RTT there is no need to implement the first step of the Karn's algorithm.

## Methods to calculate the RTO

### RFC 793 method to set the RTO

The RFC 798 uses a smoothed round-trip time estimator (SRTT) to calculates the retransmission timeout:

```
SRTT = α(SRTT) + (1-α)RTTs
```

The recommended value for α is a value between 0.8 and 0.9. The newly estimated consists of 80% to 90% from the previous estimate and 10% to 20% of the new sample. This average is also called Exponentially Weighted Moving Average (EWMA). Given the SRTT the recommended RTO is set to the following:

```
RTO = min(ubound, max(lbound, β(SRTT) )
```

β is a delayed variance factor recommended between 1.3 to 2.0. ubond is the upper bound (suggested to 60 seconds), and lbound is the lower bound (recommended to 1 second). For a stable RTT this method works fine, but not for networks with highly variable RTT such as wireless networks.

## RFC 6298 method to calculate the RTO

For networks with high variability on the Round-Trip Time, it is possible to track an estimate of the RTT variability (RTTVAR) for a better calculation of the RTO. The mean deviation is a good enough standard deviation approximation and it is faster to calculate (because mean deviation doesn't require square root). The calculations are as follows:

```
SRTT = (1-α) SRTT + α (RTTs)
RTTVAR = (1 - β) RTTVAR + β * | SRTT - RTTs |
RTO = max(SRTT + max(G, 4 * RTTVAR), 1000)

α = 1/4
β = 1/8
G = Clock granularity
```

This method uses a smoothed SRTT and RTTVAR which varies over time. For TCP timer based retransmissions this algorithm or another less aggressive must be implemented as stated by the RFC 6298. Please note most TCP implementations doesn't set the RTO floor to one second.

### Initial Values

There is no information to set the RTO before initiating the connection (unless there is cached information in the system). Per the RFC 6298, the initial value for the RTO should be 1 second, 3 seconds when the first SYN is retransmitted. The endpoint set the following values once it receives the first data:

```
SRTT = RTTs
RTTVAR = RTTs/2
```
