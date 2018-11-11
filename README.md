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
        ● http://delivery.acm.org/10.1145/1090000/1080100/p61-kuzmanovic.pdf?ip=218.248.46.107&id=1080100&acc=PUBLIC&key=045416EF4DDA69D9%2E4A4590D5C9BCB165%2E4D4702B0C3E38B35%2E4D4702B0C3E38B35&__acm__=1541934899_2f78bd63fdd21f83bdb011453bd6d979
        
 The following is the explaination of this repositry:
 
### src:
    This repositry contains the modified code for the implementation of ECN+.
    It consists of files Tcp-Socketbase.cc, Tcp-Socketbase.h and the topology.
 
### results:
    This repositry contains the results obtained after running our files. It consists of pcaps for each node.

### concept.md :
    This file consists of the concept of ns-3 ECN and then ECN+ i,e.,why is it used and how it works etc..
    
### Activities.md :
    This file consists of the activities done by us in each week and our assumptions etc...
    
### Report.md :
    This file contains of the changes we have done to the ECN enabled Tcp-Socketbase files in order to 
    implement ECN+


## How to run the files:
         
         * Download the ns3-dev-gitmaster from ns3 repositry of github
         * extract the folder and build the tools necessary (works on ubuntu 16.04)
         * copy the following topology into the scratch folder.
         * copy the tcp-socketbase.cc and tcp-socketbase.h files in the respective locations src/internet/model/
         * run this with the command "./waf --run <topology-file name>
         * there will be a results repositry created. pcaps will be stored.open pcap for any node and you can 
           observe the packets flow from that node.
           
## Group:
        ● shiva swaroop.v (16CO252)
        ● sanjith geddam  (16CO111)
        ● kunal M         (16CO227)
