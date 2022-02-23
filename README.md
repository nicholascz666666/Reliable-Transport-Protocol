# Reliable Transport Protocol
Reliable Transport Protocol (RTP for short) is project 4 for CS3700@NEU

## Our Approach

### 3700send

Our 3700send program will start by sending the first packet. Each time a packet is sent, we check that the full message is able to be sent. After, we add the packet to our list of sent packets so we can check if the packet has been received when we receive an ACK. 

After this first packet is sent, we continuously try to parse data. We have a timeout set so that should we hit the timeout without completing, we will know that we had a timeout issue and will readjust our tolerance accordingly. Additionally, our timeout indicates that we may have dropped packets so we will send all packets we have not received an ACK for. 

If we do not experience a timeout issue, we will parse our received data. If it is a successful ACK, we remove the packet from our sent list. Then, we increase our window for sending packets and send the next packets. 

### 3700recv

Our 3700recv program will bind itself to the given port and initialize a list of received packets. Once this is done, it will begin listening for packets.

If nothing is ready, there will be a timeout. Otherwise, we check to see if there is an EOF flag. If there is, we reassemble the sequence from our list of received packets. 

If there is no EOF flag, we add our data to our list of received packets based on the sequence number. We then send an ACK back to our sender so we know that it has been sent, checking to make sure the full message is sent. 

## Challenges

Our biggest challenges were with bandwidth and drop rate. When we started working with bandwidth, we found that our sender was often spending too long waiting on messages, causing time outs. We found that by changing window size adaptively, we were able to solve most issues with this. 

For packet drops, we struggled with coming up with a sound solution to detect and then retransmit once a packet had been dropped. In the end, we kept a list of dropped packets and would retransmit if we had not received an ACK in a while.

## Test Cases

In order to test our code, we ran testall on the VM. As we were debugging, we also would run nettests on specific cases that were failing in order to check where our code was breaking. Other than the tests included in the testall, we also ran a few additional tests in order to ensure thoroughness for worse case scenarios:

- bandwidth 1, latency 20, size huge
- bandwidth 1, latency 100, size huge
- bandwidth 1, latency 500, size huge
- bandwidth 0.5, latency 100, size huge
- bandwidth 5, latency 10, drop 50, size huge

