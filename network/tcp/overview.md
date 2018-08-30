# TCP Overview

TCP is a transport layer protocols which uses IPv4 or IPv6 as the network layer. TCP provides a connection-oriented, full-duplex, reliable, byte stream service to the application layer.

It is **connection-oriented** because both parties have to contact each other before the exchange of data. Multicast or Broadcast doesn't apply to TCP.

**Full-duplex** means data can flow in both directions, independent of each other.

It is **reliable** because will deliver in-order data to the application layer and in case of loss it will retransmit the missing segments. 

**Byte stream service** means there are no message boundaries. The sending application might write 20 bytes and 40 bytes, and the receiving application might read a single stream of 60 bytes.

## TCP Header

    0               1               2               3
    0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
    ┌───────────────────────────────┬───────────────────────────────┐
    │                               │                               │
    │          Source Port          │        Destination Port       │
    │            16 bits            │            16 bits            │
    ├───────────────────────────────┴───────────────────────────────┤
    │                                                               │
    │                        Sequence Number                        │
    │                            32 bits                            │
    ├───────────────────────────────────────────────────────────────┤
    │                                                               │
    │                     Acknowledgment Number                     │
    │                            32 bits                            │
    ├───────┬───────┬─┬─┬─┬─┬─┬─┬─┬─┬───────────────────────────────┤
    │Header │       │C│E│U│A│P│R│S│F│                               │
    │Length │Reserv │W│C│R│C│S│S│Y│I│          Window Size          │
    │4 bits │4 bits │R│E│G│K│H│T│N│N│            16 bits            │
    ├───────┴───────┴─┴─┴─┴─┴─┴─┴─┴─┼───────────────────────────────┤
    │                               │                               │
    │          TCP Checksum         │        Urgent Pointer         │
    │            16 bits            │            16 bits            │
    ├───────────────────────────────┴───────────────────────────────┤
    │                                                               │
    │                          TCP Options                          │
    │                            variable                           │
    └───────────────────────────────────────────────────────────────┘

- Source port: Used to identify the connection along with the destination port and the source and destination IP addresses.
- Destination port: Identifies the connection along with the source port and the source and destination IP addresses.
- Sequence Number: Identifies the byte number in the stream of data. The sequence number in a segment represents the first byte of data of the segment. The initial sequence number is chosen randomly.
- Acknowledgment Number: Contains the next sequence number the sender of the acknowledgment expects to receive.
- Header Length: The header length in 32-bit words. With 4 bits field, the header is limited to a 60-byte header.
- TCP flags:
    - CWR (Congestion Window Reduced ): The sender decreased its transmission rate.
    - ECE (Explicit Congestion Notification Echo): The sender received a congestion notification.
    - URG (Urgent pointer): Not used
    - ACK (Acknowledgment): The ACK field is valid, always on after the session has been established.
    - PSH (Push): Receiver should pass this data to the application as soon as possible. Not reliable used.
    - RST (Reset): Abort the connection.
    - SYN (Synchronization): Synchronize sequence numbers to initiate a connection.
    - FIN: Sender has finished sending data.
- Window size: Number of bytes the receiver is willing to accept. Up to 64 KB without Window Scale option.
- TCP Checksum: The checksum includes some IP header fields, the TCP header, and the data.
- Urgent Pointer: An offset added to the sequence number to mark the last byte of urgent data.
