## week1 (07 oct):
          downloaded ns-3 in ubuntu 16.04. faced problem when installing in ubuntu 18.04 
          because of some packages not being supported in ubuntu 18.04 LTE 
          i.e., pyVIZ is not being enabled in 18.04 . 
          
## week2 (24 oct):
           . read properly about ns-3 and ran the example scripts 7.
           . read the givenj RFC and understood the concept of ECN.
           
           NS-3 dev has to be downloaded as the ns-3 3.27 does not have about ecn in its tcp-socketbase.
           
           
## To-Do :

   1. create a simple topology
   2. Install Bulk-senedr application on the nodes inorder to create more traffic in the medium
   3. Enable RED queue model. It supports Classic Ecn. Enable ECN related stuff like ECT(0),ECT(1),2 bits in header of TCP etc.,
   4. have to note the differences between ECN enabled and disabled . i.e., packets lost when disabled must be same as packets marked when enabled 

 * enabled pcap and ran a output file on wireshark and got to know about the
    Ip headers and fields in them of every packet.
 * studied tcp-socketbase and noted the places where changes to be done reguarding ECN. 

* created dumbell topology with 5 left nodes 2 routers(with red active queue management) and 5 right nodes
* enabled ect bit for control packet (SYN_ACK) by writing some snippet in tcp-socketbase.cc (mainly on addsockettags,sendemptypackets) and header file
* ecn+ is enabled
