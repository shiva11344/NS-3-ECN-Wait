# SIM-06: Implementation of ECN+/Wait algorithm in ns-3 

## About project:
        ● Explicit Congestion Notification (ECN) is a signaling mechanism used to inform the TCP 
          sender about congestion.
        ● Whereas ECN+ is a minor extension of ECN wherein SYN/ACK packets also carry the ECN marks.
        ● This project is a further extension of ECN+.This algorithm is described in RFC 5562. 
         This project intends to implement ECN+/Wait in ns-3.
         
## References:
        ● RFC 5562 (Link: ​https://tools.ietf.org/html/rfc5562​) 
        ● ECN support for TCP in ns-3 (Link: https://www.nsnam.org/docs/models/html/tcp.html#support-for-explicit-congestion-notification-ecn​)
        
## Group:
        ● shiva swaroop.v (16CO252)
        ● sanjith geddam  (16CO111)
        ● kunal M         (16CO227)

## How to run the files:
         
         * Download the ns3-dev-gitmaster from ns3 repositry of github
         * extract the folder and build the tools necessary (works on ubuntu 16.04)
         * copy the following topology into the scratch folder.
         * copy the tcp-socketbase.cc and tcp-socketbase.h files in the respective locations src/internet/model/
         * run this with the command "./waf --run <topology-file name>
         * there will be a results repositry created. pcaps will be stored.open pcap for any node and you can 
           observe the packets flow from that node.
