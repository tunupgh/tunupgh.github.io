---
layout: post
title: 'Cisco IOS VPN error: peer does not do paranoid keepalives'
date: 2013-07-13 10:37
comments: true
categories:
- cisco
- ios
- vpn
- troubleshooting
---
Recently I was troubleshooting a VPN tunnel and the tunnel appeared to be at MM_NO_STATE whenever I'd try to bring the tunnel up. I turned on `debug crypto isakmp` and saw the following output:

```
ISAKMP: Created a peer struct for 77.77.77.77, peer port 500
ISAKMP: New peer created peer = 0x66440AA0 peer_handle = 0x8007F09C
ISAKMP: Locking peer struct 0x66440AA0, refcount 1 for isakmp_initiator
ISAKMP: local port 500, remote port 500
ISAKMP: set new node 0 to QM_IDLE      
ins.ert sa successfully sa = 66825864
ISAKMP:(0):Can not start Aggressive mode, trying Main mode.
ISAKMP:(0):found peer pre-shared key matching 77.77.77.77

ISAKMP:(0): beginning Main Mode exchange
ISAKMP:(0): processing SA payload. message ID = 0
ISAKMP:(0): processing vendor id payload
ISAKMP:(0): vendor ID seems Unity/DPD but major 123 mismatch
ISAKMP:(0): vendor ID is NAT-T v2
ISAKMP:(0): processing vendor id payload
ISAKMP:(0): vendor ID seems Unity/DPD but major 194 mismatch
ISAKMP:(0):found peer pre-shared key matching 77.77.77.77
ISAKMP:(0): local preshared key found
ISAKMP : Scanning profiles for xauth ...
ISAKMP:(0):Checking ISAKMP transform 2 against priority 3 policy
ISAKMP:      encryption 3DES-CBC
ISAKMP:      hash SHA
ISAKMP:      default group 2
ISAKMP:      auth pre-share
ISAKMP:      life type in seconds
ISAKMP:      life duration (VPI) of  0x0 0x1 0x51 0x80 
ISAKMP:(0):Encryption algorithm offered does not match policy!
ISAKMP:(0):atts are not acceptable. Next payload is 0
ISAKMP:(0):Checking ISAKMP transform 2 against priority 5 policy
ISAKMP:      encryption 3DES-CBC
ISAKMP:      hash SHA
ISAKMP:      default group 2
ISAKMP:      auth pre-share
ISAKMP:      life type in seconds
ISAKMP:      life duration (VPI) of  0x0 0x1 0x51 0x80 
ISAKMP:(0):atts are acceptable. Next payload is 0
ISAKMP:(0): processing vendor id payload
ISAKMP:(0): vendor ID seems Unity/DPD but major 123 mismatch
ISAKMP:(0): vendor ID is NAT-T v2
ISAKMP:(0): processing vendor id payload
ISAKMP:(0): vendor ID seems Unity/DPD but major 194 mismatch
ISAKMP:(0):Input = IKE_MESG_INTERNAL, IKE_PROCESS_MAIN_MODE
ISAKMP:(0):Old State = IKE_I_MM2  New State = IKE_I_MM2 

ISAKMP:(0): sending packet to 77.77.77.77 my_port 500 peer_port 500 (I) MM_SA_SETUP
ISAKMP:(0):Input = IKE_MESG_INTERNAL, IKE_PROCESS_COMPLETE
ISAKMP:(0):Old State = .IKE_I_MM2  New State = IKE_I_MM3 

ISAKMP (0:0): received packet from 77.77.77.77 dport 500 sport 500 Global (I) MM_SA_SETUP
ISAKMP:(0):Input = IKE_MESG_FROM_PEER, IKE_MM_EXCH
ISAKMP:(0):Old State = IKE_I_MM3  New State = IKE_I_MM4 

ISAKMP:(0): processing KE payload. message ID = 0
ISAKMP:(0): processing NONCE payload. message ID = 0
ISAKMP:(0):found peer pre-shared key matching 77.77.77.77
ISAKMP:(9577): processing vendor id payload
ISAKMP:(9577): vendor ID is Unity
ISAKMP:(9577): processing vendor id payload
ISAKMP:(9577): vendor ID seems Unity/DPD but major 181 mismatch
ISAKMP:(9577): vendor ID is XAUTH
ISAKMP:(9577): processing vendor id payload
ISAKMP:(9577): speaking to another IOS box!
ISAKMP:(9577): processing vendor id payload
ISAKMP:(9577):vendor ID seems Unity/DPD but hash mismatch
ISAKMP:received payload type 20
ISAKMP:received payload type 20
ISAKMP:(9577):Input = IKE_MESG_INTERNAL, IKE_PROCESS_MAIN_MODE
ISAKMP:(9577):Old State = IKE_I_MM4  New State = IKE_I_MM4 

ISAKMP:(9577):Send initial contact
ISAKMP:(9577):SA is doing pre-shared key authentication using id type ID_IPV4_ADDR
ISAKMP (0:9577): ID payload 
	next-payload : 8
	type         : 1 
	address      : 122.122.122.122 
	protocol     : 17. 
	port         : 500 
	length       : 12
ISAKMP:(9577):Total payload length: 12
ISAKMP:(9577): sending packet to 77.77.77.77 my_port 500 peer_port 500 (I) MM_KEY_EXCH
ISAKMP:(9577):Input = IKE_MESG_INTERNAL, IKE_PROCESS_COMPLETE
ISAKMP:(9577):Old State = IKE_I_MM4  New State = IKE_I_MM5 

ISAKMP (0:9577): received packet from 77.77.77.77 dport 500 sport 500 Global (I) MM_KEY_EXCH
ISAKMP:(9577): processing ID payload. message ID = 0
ISAKMP (0:9577): ID payload 
	next-payload : 8
	type         : 1 
	address      : 77.77.77.77 
	protocol     : 17 
	port         : 0 
	length       : 12
ISAKMP:(0):: peer matches *none* of the profiles
ISAKMP:(9577): processing HASH payload. message ID = 0
ISAKMP:received payload type 17
ISAKMP:(9577): processing vendor id payload
ISAKMP:(9577): vendor ID is DPD
ISAKMP:(9577):SA authentication status:	authenticated
ISAKMP:(9577):SA has been authenticated with 77.77.77.77
ISAKMP: Trying to insert a peer 122.122.122.122/77.77.77.77/500/,  and inserted successfully 66440AA0.
ISAKMP:(9577):Input = IKE_MESG_FROM_PEER, IKE_MM_EXCH
ISAKMP:(9577):Old State = IKE_I_MM5  New State = IKE_I_MM6 

ISAKMP (0:9577): received packet from 77.77.77.77 dport 500 sport 500 Global (I) MM_KEY_EXCH
ISAKMP: set new node 1201896175 to QM_IDLE      
ISAKMP:(9577): processing NOTIFY RESPONDER_LIFETIME protocol 1
ISAKMP:(9577):SA authentication status:	authenticated
ISAKMP:(9577): processing responder lifetime
ISAKMP:(9577): start processing isakmp responder lifetime
ISAKMP:(9577): restart ike sa timer to 3600 secs
ISAKMP:(9577):deleting node 1201896175 error FALSE reason "Informational (in) state 1"
ISAKMP:(9577):Input = IKE_MESG_FROM_PEER, IKE_INFO_NOTIFY
ISAKMP:(9577):Old State = IKE_I_MM6  New State = IKE_I_MM6 

ISAKMP:(9577):Input = IKE_MESG_INTERNAL, IKE_PROCESS_MAIN_MODE
ISAKMP:(9577):Old State = IKE_I_MM6  New State = IKE_I_MM6 

ISAKMP:(9577):Input = IKE_MESG_INTERNAL, IKE_PROCESS_COMPLETE
ISAKMP:(9577):Old State = IKE_I_MM6  New State = IKE_P1_COMPLETE 

ISAKMP:(9577):beginning Quick Mode exchange, M-ID of -889293300
ISAKMP:(9577):QM Initiator gets spi
ISAKMP:(9577): sending packet to 77.77.77.77 my_port 500 peer_port 500 (I) QM_IDLE      
ISAKMP:(9577):Node -889293300, Input = IKE_MESG_INTERNAL, IKE_INIT_QM
ISAKMP:(9577):Old State = IKE_QM_READY  New State = IKE_QM_I_QM1
ISAKMP:(9577):Input = IKE_MESG_INTERNAL, IKE_PHASE1_COMPLETE
ISAKMP:(9577):Old State = IKE_P1_COMPLETE  New State = IKE_P1_COMPLETE 

ISAKMP (0:9577): received packet from 77.77.77.77 dport 500 sport 500 Global (I) QM_IDLE      
ISAKMP: set new node -469585405 to QM_IDLE      
ISAKMP:(9577): processing HASH payload. message ID = -469585405
ISAKMP:(9577): processing NOTIFY PROPOSAL_NOT_CHOSEN protocol 3
ISAKMP:(9577):deleting node -469585405 error FALSE reason "Informational (in) state 1"
ISAKMP:(9577):Input = IKE_MESG_FROM_PEER, IKE_INFO_NOTIFY
ISAKMP:(9577):Old State = IKE_P1_COMPLETE  New State = IKE_P1_COMPLETE 

ISAKMP (0:9577): received packet from 77.77.77.77 dport 500 sport 500 Global (I) QM_IDLE      
ISAKMP: set new node -1042074812 to QM_IDLE      
ISAKMP:(9577): processing HASH payload. message ID = -1042074812
ISAKMP:(9577): processing DELETE payload. message ID = -1042074812
ISAKMP:(9577):peer does not do paranoid keepalives.

ISAKMP:(9577):deleting SA reason "No reason" state (I) QM_IDLE       (peer 77.77.77.77)
ISAKMP:(9577):deleting node -1042074812 error FALSE reason "Informational (in) state 1"

ISAKMP: set new node 1453291904 to QM_IDLE      
ISAKMP:(9577): sending packet to 77.77.77.77 my_port 500 peer_port 500 (I) QM_IDLE      
ISAKMP:(9577):purging node 1453291904
ISAKMP:(9577):Input = IKE_MESG_INTERNAL, IKE_PHASE1_DEL
ISAKMP:(9577):Old State = IKE_P1_COMPLETE  New State = IKE_DEST_SA 

ISAKMP:(9577):deleting SA reason "No reason" state (I) QM_IDLE       (peer 77.77.77.77) 
ISAKMP: Unlocking peer struct 0x66440AA0 for isadb_mark_sa_deleted(), count 0
ISAKMP: Deleting peer node by peer_reap for 77.77.77.77: 66440AA0
ISAKMP:(9577):deleting node -889293300 error FALSE reason "IKE deleted"
ISAKMP:(9577):deleting node 1201896175 error FALSE reason "IKE deleted"
ISAKMP:(9577):deleting node -469585405 error FALSE reason "IKE deleted"
ISAKMP:(9577):deleting node -1042074812 error FALSE reason "IKE deleted"
ISAKMP:(9577):Input = IKE_MESG_FROM_PEER, IKE_MM_EXCH
ISAKMP:(9577):Old State = IKE_DEST_SA  New State = IKE_DEST_SA 

ISAKMP:(0): sending packet to 77.77.77.77 my_port 500 peer_port 500 (I) MM_NO_STATE
ISAKMP (0:0): received packet from 77.77.77.77 dport 500 sport 500 Global (I) MM_NO_STATE
```

I see this router going through each of the MM states. 

`IKE_I_MM2` -> `IKE_I_MM3` -> `IKE_I_MM4` -> `IKE_I_MM5` -> `IKE_I_MM6` -> `QM_IDLE`

This looks great. It's completing the entire Phase one key exchange process. So I know nothing is wrong with my ISAKMP settings.

Shortly after it becomes QM_IDLE it starts deleting SAs and says:

`ISAKMP:(9577):peer does not do paranoid keepalives.`

This sounds like the keepalives between both systems is mismatched but actually what solved this problem is that **one side had PFS on while the other did not**. When we disabled PFS on both sides the tunnel was able to establish perfectly.

If you had this same problem and you used a different way to resolve it, let me know in the comments below.











