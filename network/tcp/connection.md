# TCP Connection Management

TCP is a connection-oriented transport protocol which means it needs to establish a connection before sending data and close the connection after sending data. During all the connection phases, TCP must handle the connection state.

The 4-tuple of source and destination IP addresses and source and destination port numbers define a TCP connection.

## Connection establishment

### Initial Sequence Number (ISN)

Actual Operative Systems select the ISN semi-random, for a couple of reasons:
- A delayed packet from a previous connection might interfere with the new one if the 4-tuple is re-used for a new connection.
- A third party might forge a segment to interrupt a connection if the Sequence Number is known.

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

## Connection Termination

### Connection Close

    Active Closer                                              Passive Closer
          │                                                           │
          │                       Data Transfer                       │
          │ <═══════════════════════════════════════════════════════> │
          │                                                           │
          │                                                           │
          │           FIN + ACK; Seq: N; Ack: M; TCP options          │
       1  │ ────────────────────────────────────────────────────────> │
          │                                                           │
          │             ACK; Seq: M; Ack: N+1; TCP options            │
          │ <──────────────────────────────────────────────────────── │  2
          │                                                           │
          │          FIN + ACK; Seq: M; Ack: N+1; TCP options         │
          │ <──────────────────────────────────────────────────────── │  3
          │                                                           │
          │             ACK; Seq: N; Ack: M+1; TCP options            │
       4  │ ────────────────────────────────────────────────────────> │

The close operation starts when one of the endpoints wants to terminate the connection or when there is no more data to send. Both sides must complete the termination to close the connection. It takes four packets to clear a TCP connection. It takes about 1.5 times the round-trip time.
1. The Active Closer send a FIN segment. The FIN packet increases the Sequence Number by one.
2. The Passive Closer ACKs the FIN segment and notifies the application about the closure. Usually, the application starts with the termination process. The Passive Closer sends a FIN to the Active Closer.
3. The Active Closer ACKs the last FIN. The FIN is retransmitted until the ACK is received.

### Simultaneous Close

    Active Closer 1                                             Active Closer 2
          │                                                           │
          │                       Data Transfer                       │
          │ <═══════════════════════════════════════════════════════> │
          │                                                           │
          │                                                           │
          │           FIN + ACK; Seq: N; Ack: M; TCP options          │
       1  │ ────────────────────────────────────────────────────────> │
          │                                                           │
          │           FIN + ACK; Seq: M; Ack: N; TCP options          │
          │ <──────────────────────────────────────────────────────── │  3
          │                                                           │
          │             ACK; Seq: M; Ack: N+1; TCP options            │
          │ <──────────────────────────────────────────────────────── │  2
          │                                                           │
          │             ACK; Seq: N; Ack: M+1; TCP options            │
       4  │ ────────────────────────────────────────────────────────> │

A simultaneous close is very rare. It is almost like a normal termination just with interleaving segments.

### Half-Close 

    Active Closer                                              Passive Closer
          │                                                           │
          │                       Data Transfer                       │
          │ <═══════════════════════════════════════════════════════> │
          │                                                           │
          │                                                           │
          │           FIN + ACK; Seq: N; Ack: M; TCP options          │
       1  │ ────────────────────────────────────────────────────────> │
          │                                                           │
          │             ACK; Seq: M; Ack: N+1; TCP options            │
          │ <──────────────────────────────────────────────────────── │  2
          │                                                           │
          │                                                           │
          │                                                           │
          │                       Data Transfer                       │
          │ <════════════════════════════════════════════════════════ │
          │                                                           │
          │                                                           │
          │                                                           │
          │                                                           │
          │          FIN + ACK; Seq: O; Ack: N+1; TCP options         │
          │ <──────────────────────────────────────────────────────── │  3
          │                                                           │
          │             ACK; Seq: N; Ack: O+1; TCP options            │
       4  │ ────────────────────────────────────────────────────────> │


An application may close the connection in one direction. Not a usual behavior, but the standard allows it.

## Connection states

                                       ┌─────────────────────────────────────────────────┐
                                       │                                                 │
                                       V          Active Open                            │
                                ┌─────────────┐    Send: SYN                             │
                  Passive Open  │             │════════════════════╗                     │
               ┌────────────────│   CLOSED    │                    ║                     │
               │                │             │<------------┐      ║                     │
               │                └─────────────┘             |      ║                     │
               │                                   Close or |      ║                     │
               │                                   Timeout  |      ║                     │
               │                                            |      ║                     │
               V                                            |      V                     │
        ┌─────────────┐                                 ┌─────────────┐                  │
        │             │            Send: SYN            │             │                  │
        │   LISTEN    │-------------------------------->│  SYN-SENT   │                  │
        │             │                                 │             │                  │
        └─────────────┘                                 └─────────────┘                  │
            Λ     │                                         |      ║                     │
            |     │                                         |      ║                     │
      Recv: |     │ Recv: SYN                 Recv: SYN     |      ║                     │
        RST |     │ Send: SYN+ACK             Send: SYN+ACK |      ║                     │
            |     │                                         |      ║                     │
            |     V                                         |      ║                     │
        ┌─────────────┐                    Simultaneous     |      ║                     │
        │             │                        Open         |      ║                     │
        │  SYN-RECV   │<------------------------------------┘      ║                     │
        │             │                                            ║                     │
        └─────────────┘                                            ║                     │
           |       │                                               ║                     │
           |       │                                               ║                     │
           |       │ Recv:                                         ║ Recv: SYN+ACK       │
           |       │ ACK                                           ║ Send: ACK           │
           |       │                                               ║                     │
           |       │                                               ║                     │
           |       │            ┌─────────────┐                    ║                     │
           |       │            │             │                    ║                     │
           |       └───────────>│ ESTABLISHED │<═══════════════════╝                     │
           | Close              │             │                                          │
           | Send:              └─────────────┘                                          │
           | FIN                   ║       │                                             │
           |                       ║       │ Recv: FIN                                   │
           |       ╔═══════════════╝       │ Send: ACK                       Pasive      │
           |       ║                       │                                  Close      │
           |       ║                       V                                             │
           |       ║            ┌─────────────┐              ┌─────────────┐             │
           |       ║ Close      │             │    Close     │             │  Recv: ACK  │
           |       ║ Send:      │ CLOSE_WAIT  │─────────────>│  LAST-ACK   │────────────>┤
           |       ║ FIN        │             │  Send: FIN   │             │             │
           |       ║            └─────────────┘              └─────────────┘             │
           |       ║                                                                     │
           |       ║                                                                     │
           |       ║                                                                     │
           |       ║     Recv:  ┌─────────────┐                                          │
           |       ║     ACK    │             │                              Active      │
           |       ║     ╔═════>│ FIN-WAIT-2  │══╗                            Close      │
           |       ║     ║      │             │  ║ Recv: FIN                             │
           V       V     ║      └─────────────┘  ║ Send: ACK                             │
        ┌─────────────┐══╝                       ╚══════════>┌─────────────┐             │
        │             │     Recv: FIN+ACK                    │             │             │
        │ FIN-WAIT-1  │------------------------------------->│  TIME-WAIT  │════════════>┘
        │             │     Send: ACK                        │             │
        └─────────────┘──┐                             ┌────>└─────────────┘
                         │ Recv: FIN  ┌─────────────┐  │
                         │ Send: ACK  │             │  │               Simultaneous
                         └───────────>│   CLOSING   │──┘                      Close
                                      │             │  Recv:
                                      └─────────────┘  ACK

        Single       lines (═) represents typical server transitions
        Double       lines (─) represents typical client transitions
        Disconnected lines (-) represents not very common transitions

During its lifetime the TCP connection transition through different states:
- Listen: Waiting from a connection request from a remote endpoint
- SYN-Sent: Waiting from a matching connection request after having sent a connection request.
- SYN-Received: Waiting for the acknowledgment of the connection request after having received and sent a connection request.
- Established: An working connection. In this phase, the applications can exchange data.
- FIN-Wait-1: Waiting for the connection termination request from the other endpoint or the acknowledgment of the connection termination request previously sent.
- FIN-Wait-2: Waiting for a connection termination request from the other endpoint.
- Closing: Waiting for a connection termination request acknowledgment from a remote TCP.
- Time-Wait: Waiting for twice the Maximum Segment Lifetime (MSL) to be sure the remote endpoint receives the acknowledgment for its connection termination request.
- Close-Wait: Waiting for a connection termination request from the local user.
- Last-ACK: Waiting for an acknowledgment for the connection termination request previously sent.
- Closed: Fictional state representing no connection.
