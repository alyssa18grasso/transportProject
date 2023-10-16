# cs3700transport
In this repository, you will find our implementation of a simple transport protocol that provides a reliable datagram service. The UDP transport protocol should in practice deliver data in order, without duplicates, missing data, or errors between two nodes (a sender and receiver). 

The starter code of this project, and elements of its README.md, were taken from https://3700.network/docs/projects/transport/#description

## Builtwith
* Python

## Testing
* Tests gauge both performance and correctness
* The results of this code are being compared to our classmates during the grading process
* The provided testing script instantiates the nodes, sends packets from the sender and is received+interpreted+acknowledged by the receiver, then retires both nodes.

### Usability 

```sh
  $ ./run <config-file>
  ```
  
Alternatively, you can run an entire testing suite using the "test" program:

```sh
  $ ./test
  ```

## High-Level Approach

There are two classes of interest in this implementation:

1. Sender
2. Receiver

On startup, the simulator instantiates an instance of the Sender and Receiver. The Receiver binds to a UDP port and announces its arrival to the network. A Receiver could--in theory--run forever, as it's job is to receive "msg" type data messages and respond to the source with an "ack" type data message. The Receiver is also expected to print the received datagrams to STDOUT in correct order and without errors. The Sender is responsible for taking datagrams from STDIN, transmitting to the Receiver, and anticipating an acknowledgement packet. The Sender supports an initial window size of 5 packets, satisfying the minimum requirement of the assignment and optimizing the overall speed of the program. The window size can be adjusted based on the available bandwith. Both the Sender and Receiver should print debug errors to STDERR, and exit without causing an error to the program. 

The network is considered unreliable and both the Sender and Receiver must be resilient to common issues. Packet sequence numbers let the Sender and Receiver know if a packet is a duplicate or not, and should therefore not be printed. Dropped packets are resent by the Sender if an ack packet is not received soon enough, and a Receiver will know to resend based on the contents of the datagram. Packet timeouts are estimated by the round=trip time (RTT) and retransmission time outs (RTO) at the Sender. Packets that are corrupted in transit are detected by a bit-parity check and dropped at both/either nodes. 

## Challenges Faced

A challenge faced by our team during the final stages of development was improving our performance score for test 8-2-intermediate-2.conf, which was the only test at the end of our development stage that did not receive the highest possible performance score. We attempted to improve the speed of the program by increasing our Sender's intial window size from 4 to 5 (which decreased the test-time by ~0.1 seconds, and changing the RTO formula from 2 * RTT to 1.75 * RTT (reduced test-time by ~0.8 seconds). Our test was still 0.5 seconds above the 6.0 second threshold. If we had more time, we would have addressed this problem by attempting to reduce overhead in our program by finding more efficient ways to process a file and being more conservative with what bytes/packets get sent over the network. 

## Useful Properties and Features of Design

Our implementation is a comparatively high-performing and well-documented program. Our congestion control allows for "fast catch-up", since our window doubles in size if it is below the threshhold. When above the threshold, it increases linearly instead of exponentially. Therefore, even though our window goes back to size one whenever a packet is dropped, it will increase exponentially to the best size. This helps when the best window size is a very large number.
