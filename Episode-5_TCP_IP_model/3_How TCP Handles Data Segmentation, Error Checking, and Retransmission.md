How TCP Handles Data Segmentation, Error Checking, and Retransmission
Data Segmentation
When your application sends data through TCP, it doesn't travel as one large chunk. TCP breaks it into manageable pieces called segments.
The Segmentation Process
Maximum Segment Size (MSS) - TCP determines the largest amount of data that can fit in a single segment, typically 1460 bytes for standard Ethernet networks (1500 byte MTU minus 20 bytes for IP header minus 20 bytes for TCP header)
Sequence Numbers - Each segment is assigned a sequence number that represents its position in the data stream. This allows the receiver to reassemble segments in the correct order, even if they arrive out of sequence
Why Segmentation Matters to Developers - Large file uploads, video streaming, or bulk data transfers are automatically divided into segments. Understanding this helps you optimize payload sizes and understand why network performance varies
Error Checking
TCP ensures data integrity through multiple mechanisms.
Checksum Verification
Every TCP segment includes a checksum field calculated from the segment's header and data. The receiver recalculates the checksum upon receipt. If the calculated checksum doesn't match the received checksum, the segment is corrupted and discarded.
The checksum protects against bit errors that can occur during transmission due to electrical interference, faulty hardware, or radio interference in wireless networks.
Acknowledgment System
TCP uses acknowledgments (ACKs) to confirm successful receipt of data. When the receiver gets a segment, it sends back an ACK with the sequence number of the next expected byte.
Cumulative Acknowledgments - An ACK for sequence number 5000 means "I've successfully received everything up to byte 5000"
Duplicate ACKs - If the receiver gets segments out of order, it sends duplicate ACKs indicating the last correctly received sequence. Three duplicate ACKs trigger fast retransmission
Retransmission
When TCP detects data loss, it retransmits the missing segments.
Timeout-Based Retransmission
Each time TCP sends a segment, it starts a retransmission timer. If an ACK isn't received before the timer expires, TCP assumes the segment was lost and retransmits it.
Retransmission Timeout (RTO) - Dynamically calculated based on network conditions. TCP continuously measures round-trip time (RTT) and adjusts the timeout accordingly
Exponential Backoff - If retransmissions continue to fail, TCP doubles the timeout period with each attempt to avoid overwhelming a congested network
Fast Retransmit
When TCP receives three duplicate ACKs for the same sequence number, it immediately retransmits the missing segment without waiting for the timeout. This significantly reduces recovery time from packet loss.
Selective Acknowledgment (SACK)
Modern TCP implementations support SACK, which allows receivers to acknowledge non-contiguous blocks of data. Instead of retransmitting all data after a loss, TCP only retransmits the specific missing segments.
Why This Matters - On lossy networks (mobile, Wi-Fi), SACK dramatically improves performance by reducing unnecessary retransmissions
