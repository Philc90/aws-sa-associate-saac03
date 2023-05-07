https://learn.cantrill.io/courses/2022818/

# OSI model
- Not all networks implemented using this exactly but good model
- media layers: physical, datalink, network
  - deals with how data moves b/t pA & pB
- host layers: transport, session, presentation, application
  - how data is serialized & transported and how the it is parsed

Layer X devices
- has capabilities for that layer and layers below
- higher layers build on lower layers to add functionality & capabilities
- layers are independent
  - e.g. web browser (layer 7 device) talks to web server (another layer 7 device) directly, even though they're using lower levels
  - lower levels are abstracted

# Layer 1: Physical
- provides timing, voltages levels, etc, and defines connectors
- medium: wire, RF, light
- multicasting: anything received on port is transmitted to other ports
- no device addressing
- hub: layer 1 device. Repeats activity from 1 port to other ports
- collision: only one device can comm at a time, otherwise signals collide and corrupt data
  - cannot detect collisions
- *1 broadcast & 1 collision domain*
  - means it doesn't scale well
- not intelligent; layer 2 provides intelligence

# Layer 2: Data Link
- provides device to device comm.
- *MAC (Media Access Control) address*: globally unique, assigned to physical device (not sw-assigned)
  - 2 parts
    - OUI (organizationally unique id): assigned to manufacturer
    - NIC (network interface controller)
- *frames*: container
  - preamble: start of frame
  - MAC header
    - dest & src MAC addr's
      - broadcast = all F's in dest
    - ET (Ethertype): which layer 3 protocol, e.g. IP
      - layer 3 uses layer 2 to put data in frame
  - payload
    - *encapsulation*: payload is encapsulated before transmission.
      - layer 3 generates (e.g. IP packet) and is put in a layer 2 ethernet frame
      - every layer encapsulates, and is de-encapsulated as it moves up the layers
  - *FCS (Frame check sequence)*: CRC error check
- *Carrier Sense Multiple Access / Collision Detection (CSMA/CD)*
  - CSMA: detect if any signal on layer 1 before passing frame
    - if carrier is detected, wait until free before passing frame
  - CD: if collision detected, jam signal sent by all devices detecting, then random back-off occurs
    - back-off: no transmission attempted
      - if successful, one device transmits, other devices detect carrier and wait
      - fail, back-off period is increased
- hub - layer 1 device, collisions can still occur
- switch - layer 2 device
  - works like a hub
  - understands frames & mac addr's
  - maintains mac addr table
    - learns mac addr's conn'd to each port
  - stores & forwards frames
    - only forwards frame to destination
    - collisions limited to one port
      - switch only fwds valid frames
      - deals w/ frames, not layer 1
    - *N port switch has N collision domains*
- foundation for all networks
- allow unicast (1:1) & broadcast (1:all) comm.

# Layer 3: Network
- layer 2 networks can only be conn'd with direct point to point links
  - must be same L2 protocol
    - ethernet: most popular L2 protocol
    - for long-dist, may use PPP, MPLS, ATM
- IP (Internet Protocol)
  - cross-network IP Addressing
  - routing
  - don't need direct P2P links
  - IP packet is encap'd for local network, then as it moves to other networks, de-encap'd and encap'd again for local network
  - v4 & v6
  - v4 struct
    - src/dest ip addr
    - protocol: Layer 4 protocol (e.g. ICMP, TCP, UDP)
    - data: from L4
    - TTL (time to live): how many hops the packets can move thru, stops packets from looping forever
  - v6:
    - src/dest are larger
    - hop limit: like ttl
- *packet*
  - like frames
  - in frames, src/dest are local, but in packets they can be anywhere
  - in transit, the packets remain the same (mostly), but encap'd in frames
