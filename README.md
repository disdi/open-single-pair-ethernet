
# Open Single Pair Ethernet

## Details

Single Pair Ethernet (IEEE 802.3cg 10BASE-T1S) provides a simple, deterministic multidrop bus for distributed embedded systems. It carries data (and optionally power) over a single twisted pair, making it well-suited for industrial, automotive, and edge sensor networks.

The plan is to start with a reconfigurable 10BASE-T1S MAC driving an external PHY, move the digital PHY into the FPGA, and finally tape out the digital block on an open PDK.

## Roadmap

### Step 1 

External PHY (now). Half-duplex MAC + MDIO + RMII to a LAN8670 on an Eclypse Z7 with SYZYGY breakouts. Validates PLCA, mixing-segment behaviour, and a thin application wrapper for smart-sensor logic. Uses open tooling (e.g. liteeth).

### Step 2

DigPHY- Same MAC and wrapper. New RTL implements Clause 147 PCS, Clause 148 PLCA, and the OPEN Alliance TC14 3-pin interface. The external part shrinks to any TC14-compliant PMD (e.g. TJA1410). Vendor-agnostic digital side.

### Step 3

Open-PDK tape-out. Target a compact digPHY + MAC + smart-sensor block for an affordable shuttle such as wafer.space (GF180MCU open PDK, slots from ~5 mm² / $2 k for 1000 dies upward). Open PDKs (SkyWater, IHP, GlobalFoundries) and open tooling (LibreLane, open DRC, etc.) keep the entire flow inspectable.
