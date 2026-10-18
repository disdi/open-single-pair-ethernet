# Open Single Pair Ethernet

Single Pair Ethernet (IEEE 802.3cg 10BASE-T1S) provides a simple, deterministic multidrop bus for distributed embedded systems. It carries data (and optionally power) over a single twisted pair, making it well-suited for industrial, automotive, and edge sensor networks.

The plan is to start with a reconfigurable 10BASE-T1S MAC driving an external PHY, move the digital PHY into the FPGA, and finally tape out the digital block on an open PDK.

The stages are described on the [Roadmap](roadmap.md).
