# TCP Retransmissions

TCP has two different mechanisms to initiate retransmissions, one based on timeouts and other based on inferring losses by the acknowledgment structure. TCP sets a timer when it sends data if the timer expires and the data is not acknowledged a timer-based retransmission occurs. A fast retransmission happens without a delay when the cumulative acknowledgment doesn't advance or when receiving a segment with a SACK block.
