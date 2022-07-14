# TCP Handshakes 101: from zero to basics

I'm going to keep it short and sweet: Basically, **handshake** is a coordination/negotiation process between two devices to start or finish the communication between them.

Initially, there are two main types of handshake:


&nbsp;
## Three - Way Handshake


It is the process designated by [RFC 793](https://datatracker.ietf.org/doc/html/rfc793#page-30) to establish TCP connections.

To put it simply: This process is very similar to something that we all know well, especially after 2020.

&nbsp;
![GIF of a woman talking to an elderly person via a video conference](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1lnyyio1vj0b9k1sc8mz.gif)

**Videocall starts:**

_- Hello. Can you hear me?_
_- Hey. Yeah, I can hear you. Is the audio working?_
_- Yes, I can hear you loud and clear._


&nbsp;
That is to say, is a process that assures proper communication between both sides.

Given that one of the characteristics of the TCP protocol is **reliability**, even before communication between hosts begins and data transfer starts, the handshake confirms the necessary conditions to establish the connection. Before any data is exchanged between client and server, a trust connection must be established between them.

As the name implies, a _three-way handshake_ takes place in 3 steps, all involving data packet exchanges between client and server. When establishing a connection, the client and server must synchronize sequence numbers with each other. It happens like this:


&nbsp;
1. The client takes the initiative and initiates the connection to the server. In this first contact, he "proposes" the form of communication that will be used in the "conversation" (in short: the order of sending the data packets).

2. The server responds to the client by acknowledging the receipt of the first message and agreeing to the order in which the packets will be sent.

3. The client, in turn, also confirms that it has received the message from the server and initiates the communication.




&nbsp;
![Simplified layout of client-server communication via three-way handshake](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/po71rhycqxthg0e92px9.png)


&nbsp;
## Flags



&nbsp;
TCP connections use control bits inserted in the packet header. These control bits are called flags and have the function of establishing the data transmission protocols.


![Schema of a TCP packet, showing the headers and flags that are in the packet](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/56yc9a0b13ie45zgz1xw.png)


&nbsp;
**ACK (Acknowledgement) :**The main function of this flag is to confirm the reception of the packet at the destination. As I said just above, one of the characteristics of the TCP protocol is reliability. The packet sender needs to receive confirmation that the packet has arrived at the desired location and that there is no need to retransmit the same packet.

**ISN (Initial Sequence Number) :** It is not a flag, but a unique sequence of numbers to identify each packet.

**SYN (Synchronize) :** Okay, we already know how to confirm the receipt of packages and how to identify them. It remains now to sort them in a sequence that will be used by the hosts. The SYN flag is about this, the synchronization of these packet sequence numbers. As hosts must agree on the order of these sequential numbers, the flag has this function of establishing the sending order during communication. The order of these numbers is not standard or predefined for security reasons. After all, by knowing the order of the sequential numbers, an attacker could forge one of those numbers.

&nbsp;
Knowing this, we can understand this process in more detail now:


&nbsp;
1. The client sends a request to the server asking to synchronize its sequence numbers. A packet that has the **SYN** flag enabled is sent specifying its starting sequence number (**ISN**).
   
2. The server also sends its request to the client to sync its sequence numbers (with the **SYN** and **ACK** flags enabled), while acknowledging and confirming the receipt of the request by sending the client's sequence number incremented by one (**ISN** + 1).
   
3. The client acknowledges the receipt by sending a new packet with the **ACK** flag and the server's sequence number is also incremented by one. (**ISN** + 1).



&nbsp;
![More detailed layout of a three-way handshake, showing how flags are exchanged between client and server](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8r7imxp9a4u6p05s8w44.png)



&nbsp;
Using Wireshark, we were able to see the process happening sequentially:


![Wireshark filter showing TCP packet exchange in a 3-way handshake](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jmuyvlhdmjn2lj1wuss8.png)


&nbsp;
Right after this packet exchange, the server should receive an HTTP request and the "conversation" starts.




&nbsp;


## Four-Way Handshake





&nbsp;
Ok, we have already seen how the process of starting the connection of the hosts is. The finalization process is not that different (it is even described by the same RFC as the 3-way handshake, but on a different [page](https://datatracker.ietf.org/doc/html/rfc793#page-37)). This time, instead of 3 data segments, 4 segments will be exchanged between the hosts, justifying the name _"4-way handshake"_.



&nbsp;
Simply put, the process goes like this:


1. The customer has no more data to send. For this reason, it decides to close the connection and sends a packet, communicating this decision to the server.
   
2. Server receives the packet. You confirm receipt of this package by also sending a package to the customer.
   
3. The server sends a second packet stating that there is nothing more to send and that it will also terminate the connection.
   
4. The client responds with a new packet, acknowledging the receipt of previous packets and closing the connection.


&nbsp;
![Esquema simplificado da comunicação entre cliente e servidor via handshake de quatro vias](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/euvivjwl85152jmzzhdz.png)



&nbsp;
## FIN Flag



&nbsp;
FIN (Finish): In this type of handshake, the FIN flag is used, a finalization flag. After the data transfer is complete, client and server end the connection using this flag. 


&nbsp;
Reviewing the handshake process, now with the flags:

1. The client, when starting to close the connection, sends a segment with the FIN flag activated (bit 1) + the ISN. The ACK flag is also contained in the segment, but it is not activated (bit 0).
   
2. Upon receiving the connection termination request, the server sends a packet with the ACK flag activated, showing that the request has been received. The "Acknowledgement number" is equal to the received ISN plus 1 (ISN + 1).
   
3. The server sends a new segment to the client, this time with the FIN flag set and a new ISN, to end the connection. After this submission, the connection is closed on the server side.
   
4. The client receives the FIN flag from the server, sets the ACK flag with the Acknowledgment number equal to the received ISN plus 1 (ISN +1) and sends the last packet. Upon completion of this step, the connection is also closed on the client side.




&nbsp;
![Detailed layout of a four-way handshake, showing the exchange of flags between client and server](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0bwup6x12m04i394bb2x.png)

