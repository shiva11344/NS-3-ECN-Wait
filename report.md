## ECN+/ WAIT:
*  REFERNCES:
           1. RFC 5562 (Link: https://tools.ietf.org/html/rfc5562 )
           2. ECN support for TCP in ns-3 (Link:
              https://www.nsnam.org/docs/models/html/tcp.html#support-for-explicit-
              congestion-notification-ecn )
           3. http://delivery.acm.org/10.1145/1090000/1080100/p61-
              kuzmanovic.pdf?ip=218.248.46.107&id=1080100&acc=PUBLIC&key=045416EF4DDA69D9
              %2E4A4590D5C9BCB165%2E4D4702B0C3E38B35%2E4D4702B0C3E38B35&__acm__=154193489
              9_2f78bd63fdd21f83bdb011453bd6d979
              
## DIFFERENCES BETWEEN ECN AND ECN+:
 
   ECN specification enables congested routers to mark TCP data packets during congestion, this is not
           the case with TCP control (TCP SYN and SYN ACK) packets. This is simply because these packets are
           used initially to negotiate the use of ECN options between the two endpoints. But, there can be
           devastating effects on system performance, particularly in AQM-enabled environments dominated
           by web-traffic. So, This is where ECN+ is different from ECN. Negotiation packets (Control packets)
           are also involved in marking.
           
## APPROACH:

 1. To simulate our concept first and necessary thing we need is a topology with nodes and
             routers
          2. Tcp-BulkSend Application is to be installed on the nodes in order to create web-traffic
              between the nodes. To enable ECN, our Routers must follow ACTIVE QUEUE MANAGEMENT.
              So, set the Routers to RED AQM.
  ```
  InstallBulkSend (leftNodes.Get (0), routerToRightIPAddress [0].GetAddress (1), port, 2, 0, 
                 MakeCallback   (&CwndChangeA));
  InstallBulkSend (leftNodes.Get (1), routerToRightIPAddress [1].GetAddress (1), port, 3, 0, 
                 MakeCallback (&CwndChangeB));
  InstallBulkSend (leftNodes.Get (2), routerToRightIPAddress [2].GetAddress (1), port, 4, 0, 
                  MakeCallback (&CwndChangeC));
  InstallBulkSend (leftNodes.Get (3), routerToRightIPAddress [3].GetAddress (1), port, 5, 0, 
                 MakeCallback (&CwndChangeD));
  InstallBulkSend (leftNodes.Get (4), routerToRightIPAddress [4].GetAddress (1), port, 6, 0, 
                  MakeCallback (&CwndChangeE));                      
  ```  
              
   3. Pcap has to be enabled in our topology.cc to observe the packet specifications i.e., number of
              packets, the flags enabled in those packets etc..
              
```
pointToPointLeaf.EnablePcapAll (dir + "pcap/N", true);
```
              
   4. Now in the Tcp-SocketBase.cc, Already Ecn related code is available. It means it has the code
              to handle congestion for the data packets without dropping them. What we need is to enable
              the same methodology for our negotiation packets i.e., control packets( SYN, SYN-ACK
              packets).
          5.  Press cntrl+f and search for ECN related code in the available socketbase. The main functions
             that ECN involves is SendEmptyPacket( ), AddSockettags( ), getiptos( ), setEcnEct0( )and etc..
             Then find the snippet where SYN-ACK is being sent. Where the receiver differentiates between
             normal ACK and SYN-ACK.
             
             
         6. We modified in the SendEmptyPacket( ). Where upon receiving SYN packet the receiver send
             an empty packet setting the ECE, SYN, ACK flags. This is where the modification is to be done.
             when sending the SYN-ACK packet we need to change the state of the receiver to ECN_IDLE
             and to enable our ECT bit in the IP Header which we can do using AddSockettags( ) and
             setiptos( )7. Now using pcap, we can observe the SYN-ACK being marked as ECT(0).
             We already have withECT in the AddSocketTags( ) as false by default.we make it true for 
             identifying our syn-ack.
 ```
 if (hasSyn & hasAck & hasEce & markect)
{
m_tcb->m_ecnState = TcpSocketState::ECN_IDLE;
//std::cout<<m_tcb->m_ecnState;
AddSocketTags (p,true);
SetCE(p);

}
else
{
// std::cout<<"hi";
AddSocketTags (p,false);
}
 ```
 The AddSocketTags function is as follows. It is the function responsible to mark Ip flags on the packets 
 ```
 TcpSocketBase::AddSocketTags (const Ptr<Packet> &p,bool withEct) const //withEct boolean variable added here
{
if (GetIpTos ())
{
SocketIpTosTag ipTosTag;

if (m_tcb->m_ecnState != TcpSocketState::ECN_DISABLED && (CheckEcnEct0 (GetIpTos ()) ||
withEct))
{
ipTosTag.SetTos (MarkEcnEct0 (GetIpTos ()));
}
p->AddPacketTag (ipTosTag);
}
else
{
// std::cout<<m_tcb->m_ecnState
if (m_tcb->m_ecnState != TcpSocketState::ECN_DISABLED && (p->GetSize () > 0 || withEct))
{
// Set ECT(0) if ECN is enabled and ipTos is 0
SocketIpTosTag ipTosTag;
ipTosTag.SetTos (MarkEcnEct0 (GetIpTos ()));
p->AddPacketTag (ipTosTag);
}
```             
             
   7. Next, since we were unable to create congestion explicitly for the SYN/Ack packets, we manually set the CE bit in the SYN/ACK packet to see what happens once the syn-ack packet gets CE marked. We added a function SetCE() to set the CE bit in any packet desired in tcp-socket-base.h . We set the CE flag with this function:
  ```
  void
TcpSocketBase::SetCE(Ptr<Packet> p)
{
  SocketIpTosTag ipTosTag;
  ipTosTag.SetTos (MarkEcnCe (0));
  p->ReplacePacketTag (ipTosTag);
}
```
When we are sending syn-ack from reciever.we mark the syn-ack with our CE using our defined function SetCE(<packet>).
           
```
 if (hasSyn & hasAck & hasEce & markect)
    {
      m_tcb->m_ecnState = TcpSocketState::ECN_IDLE;  
      AddSocketTags (p,true);
      SetCE(p);
      std::cout<<"Marking CE bit\n";
       std::cout<< m_tcb->m_ecnState<<"-receiver ecn-state\n" ;
```
   9. In case of Downloading: After Connection is established the ACK is sent with ECHO messages
              when we experienced congestion in SYN-ACK packet.
   10. In case of Uploading : After connection is established, The client can send data packets with
       reduced window size. Sending ECHO messages is not necessary as the server has no contribution of data.
   11. Then we can observe in pacp when we get the ACK with ECE set the server sends the data with reduced window size.
   12. We yet need to implement Wait algorithm, That is to make the server wait for a RTT before
             sending its datapackets. i.e., set the state of it to WAIT until an RTT (from ESTIMATERTT( )).
### ECN+/WAIT ADVANTAGES:
