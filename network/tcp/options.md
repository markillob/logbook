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
