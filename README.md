
# Open Single Pair Ethernet

## Introduction

The worlds of networking and FPGA design have become increasingly intertwined. Modern FPGAs are now powering everything from high-speed network interfaces to complex packet processing pipelines and industrial automation systems.

Single Pair Ethernet (IEEE 802.3cg 10BASE-T1S) provides a simple, deterministic multidrop bus for distributed embedded systems. It carries data (and optionally power) over a single twisted pair, making it well-suited for industrial, automotive, and edge sensor networks without need of a Switch.

The plan is to start with a reconfigurable 10BASE-T1S MAC driving an external PHY and finally move the digital PHY into the FPGA.

## Roadmap

### Step 1 : MAC with External PHY (10BaseT1S PHY from Microchip LAN8670)

![Step 1 roadmap](images/step1.png)

Half-duplex MAC + MDIO + RMII to a LAN8670 on an Eclypse Z7 with SYZYGY breakouts. Validates PLCA and mixing-segment behaviour using [LiteEth](https://github.com/enjoy-digital/liteeth).

### Step 2 : digital PHY (PCS + PLCA) inside FPGA

![Step 2 roadmap](images/step2.png)


Implement DigPHY using same MAC but with new RTL implementing Clause 147 PCS, Clause 148 PLCA, and interfacing with the [OPEN Alliance TC14 3-pin interface](https://opensig.org/wp-content/uploads/2024/01/OPEN_Alliance_10BASE-T1S_PMD_Transceiver_Interface_1p5_Final.pdf). The external part shrinks to a minimal external analog PMD over the 3-pin TC14 interface(e.g. [TJA1410](https://www.nxp.com/products/TJA1410)) which is  Vendor-agnostic.

```
┌─────────────────────────────────────────┐
│  MAC (LiteEth )                         │
│  ↑↓                                     │
│  Clause 148 PLCA                        │  ← schedules who may transmit
│  ↑↓                                     │
│  Clause 147 PCS                         │  ← encodes/decodes symbols
│  ↑↓                                     │
│  TC14 3-pin interface                   │  ← digital link to analog chip
│  ↑↓                                     │
│  TC14-compliant PMD (TJA1410, etc.)     │  ← analog front-end + cable
└─────────────────────────────────────────┘

```
