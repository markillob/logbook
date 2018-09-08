# TCP Options

The TCP header might contain options. Every option has a **kind** byte that specifies the type of option. Except for kind 0 and 1, all other options have a **length** byte to indicate the option length including the kind and length bytes. The TCP header's length must be a multiple of 32 bits because of the header length field is specified in this unit. Up to 40 bytes are available to hold options.

## End of Option List (EOL)
- Kind: 0
- Length: 1

Used to end all the options.

## No-Operation (NOP)
- Kind: 1
- Length: 1

Used to align subsequent options to word boundaries. Not strictly necessary.

## Maximum Segment Size (MSS)
- Kind: 2
- Length: 4

Maximum TCP data the host is willing to accept. Usual values are 1460 for IPv4 and 1440 for IPv6. The MSS defaults to 536 bytes if this option is not present. This option is present only in SYN segments.

## Window Scale (WS)
- Kind: 3
- Length: 3

The Window Scale option expands the TCP Window to about 30 bits.  The value of this option left-shifts the window field from 0 to 14 (inclusive), for a maximum window of 1 GB.  WS option is used in SYN segments and should be included in the header even if is set to 0, to advertise the capability to the remote endpoint.

## Selective Acknowledgement Permitted (SACK-Permitted)
- Kind: 4
- Length: 2

This TCP option informs the Selective Acknowledgment capability of the sender. TCP's cumulative acknowledgment mechanism doesn't allow to ACK non-contiguous data. The SACK option allows acknowledging data even if is not contiguous. Only the SYN segments carry the SACK-permitted option.

## Selective Acknowledgement (SACK)
- Kind: 5
- Length: Variable

The SACK option is a range of sequence numbers which represents the data blocks received. Each data block consists of two 32-bits sequence numbers. Each SACK block would consume 8 bytes, so a TCP header having n SACK blocks would be 8n+2 bytes long. When used SACK with Timestamps option up to 3 SACK blocks can be included in the TCP header.

## Timestamps (TSopt)
- Kind: 8
- Length: 10

TSopt adds two 4-byte timestamps to measure RTT and for Protection Against Wrapped Sequence (PAWS). The Timestamp Value (TSval) is included in data and ACK segments and just echoed in the TimeStamp Echo Reply (TSecr) by the other endpoint. The timestamp is a monotonically non-decreasing value. The negotiation of this TCP option happens during the connection establishment (SYN, SYN/ACK segments) and use during the rest of the connection.
