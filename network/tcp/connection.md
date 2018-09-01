# TCP Connection Management

TCP is a connection-oriented transport protocol which means it needs to establish a connection before sending data and close the connection after sending data. During all the connection phases, TCP must handle the connection state.

The 4-tuple of source and destination IP addresses and source and destination port numbers define a TCP connection.

## Connection establishment

### Three-way handshake

    Active Opener                                              Passive Opener
      (Client)                                                    (Server)
          │                                                           │
          │                 SYN Seq: ISNc; TCP options                │
       1  │ ────────────────────────────────────────────────────────> │
          │                                                           │
          │       SYN + ACK; Seq: ISNs; Ack: ISNc+1; TCP options      │
          │ <──────────────────────────────────────────────────────── │  2
          │                                                           │
          │         ACK; Seq: ISNc+1; Ack: ISNs+1; TCP options        │
       3  │ ────────────────────────────────────────────────────────> │
          │                                                           │
          │                                                           │
          │                       Data Transfer                       │
          │ <═══════════════════════════════════════════════════════> │

In this method, three packets are required to establish a new TCP connection. This method is the most common way to initiate a TCP connection. Takes around 1.5 times the round-trip time to establish a connection.
1. The Active Opener (client) will send an SYN segment to the Passive Opener (server). This SYN segment contains the Initial Sequence Number (ISN) and the initial TCP options.
2. The server replies with an SYN segment which also ACK the SYN packet of the client with an acknowledgment number of client's ISN + 1 (The SYN packet consumes a sequence number and requires retransmission if lost). The server's SYN segment sets its ISN and the TCP options.
3. The client ACKs the server's ISN.

### Simultaneous Open

    Active Opener 1                                             Active Opener 2
          │                                                           │
          │                 SYN Seq: ISN1; TCP options                │
       1  │ ────────────────────────────────────────────────────────> │
          │                                                           │
          │                 SYN Seq: ISN2; TCP options                │
          │ <──────────────────────────────────────────────────────── │  2
          │                                                           │
          │       SYN + ACK; Seq: ISN2; Ack: ISN1+1; TCP options      │
          │ <──────────────────────────────────────────────────────── │  3
          │                                                           │
          │       SYN + ACK; Seq: ISN1; Ack: ISN2+1; TCP options      │
       4  │ ────────────────────────────────────────────────────────> │
          │                                                           │
          │                                                           │
          │                       Data Transfer                       │
          │ <═══════════════════════════════════════════════════════> │

    Although not seen in the diagram both SYN segments are being sent at almost the same time.
    Both endpoints receive the SYN segment after sending its own SYN.
    The SYN+ACK segments are being sent when the SYN segment arrives at each active opener.

In this method, four packets are required to establish a new TCP connection both endpoints should arrange the initiation before it takes place. It takes between 1 and 1.5 times the round-trip time for this method to complete a connection. This method is not widely used, some intermediate devices don't support it.
1. The two active openers send an SYN segment with their options and ISN to the other endpoint.
2. The two active openers acknowledge the received SYN packets and retransmit its own SYN segment.
