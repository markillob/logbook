# TCP Connection Management

TCP is a connection-oriented transport protocol which means it needs to establish a connection before sending data and close the connection after sending data. During all the connection phases, TCP must handle the connection state.

The 4-tuple of source and destination IP addresses and source and destination port numbers define a TCP connection.


## Connection establishment

### Three-way handshake

    Active Opener                                              Passive Opener
      (Client)                                                    (Server)
          │                                                           │
          │                 SYN Seq: ISNc; TCP options                │
          │ ────────────────────────────────────────────────────────> │
          │                                                           │
          │       SYN + ACK; Seq: ISNs; Ack: ISNc+1; TCP options      │
          │ <──────────────────────────────────────────────────────── │
          │                                                           │
          │         ACK; Seq: ISNc+1; Ack: ISNs+1; TCP options        │
          │ ────────────────────────────────────────────────────────> │
          │                                                           │
          │                                                           │
          │                       Data Transfer                       │
          │ <═══════════════════════════════════════════════════════> │

In this method, three packets are required to establish a new TCP connection.
1. The Active Opener (client) will send an SYN segment to the Passive Opener (server). This SYN segment contains the Initial Sequence Number (ISN) and the initial TCP options.
2. The server replies with an SYN segment which also ACK the SYN packet of the client with an acknowledgment number of client's ISN + 1 (The SYN packet consumes a sequence number and requires retransmission if lost). The server's SYN segment sets its ISN and the TCP options.
3. The client ACKs the server's ISN.

