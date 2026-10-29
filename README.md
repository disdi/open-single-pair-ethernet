
# Open Single Pair Ethernet

## Introduction

The worlds of networking and FPGA design have become increasingly intertwined. Modern FPGAs are now powering everything from high-speed network interfaces to complex packet processing pipelines and industrial automation systems.

Single Pair Ethernet (IEEE 802.3cg 10BASE-T1S) provides a simple, deterministic multidrop bus for distributed embedded systems. It carries data (and optionally power) over a single twisted pair, making it well-suited for industrial, automotive, and edge sensor networks without need of a Switch.

The plan is to start with a reconfigurable 10BASE-T1S MAC driving an external PHY and finally move the digital PHY into the FPGA.

---

## Roadmap

### Step 0 :

Create a small, open-hardware PMOD that exposes a Microchip LAN8670 (10BASE-T1S PHY) over a standard 12-pin PMOD header.

KiCad 9 sources, revision 0.2, are in [disdi/10BaseT1S-PMOD](https://github.com/disdi/10BaseT1S-PMOD) under the MIT license.

This module lets any FPGA or MCU board with a PMOD (or a simple adapter) drive a 10BASE-T1S link via RMII + MDIO. It serves as the practical hardware foundation before the FPGA-based Step 1.

```

PMOD header
   │
   ├── RMII (TXD0/1, TXEN, RXD0/1, CRS_DV, CLK50, RST_N)
   ├── MDIO / MDC
   └── 3.3 V / GND
        │
        ▼
   LAN8670 (RMII mode)
        │
        ▼
   Bus Interface Network interfacing the PHY IC to the MDI connector in 10BASE-T1S
        │
        ▼
   2-pin screw terminal (or SPE connector)

```


### Step 1 : MAC with External PHY (10BaseT1S PHY from Microchip LAN8670)

![Step 1 roadmap](images/step1.png)

Using [LiteEth](https://github.com/enjoy-digital/liteeth) implement half-duplex MAC which already supports MDIO and RMII for LAN8670 PHY.

```

FPGA (any LiteX board)
  ├── LiteEthPHYRMII
  ├── LiteEthPHYMDIO
  └── Small software routine  ← to writes PLCA registers
         │
         └── RMII + MDIO wires → connect to LAN8670 PMOD

```


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
---

### Advantages

- To make SPE Ethernet over twisted pair cable accessible to FPGAs for deterministic workloads.

- Step 2 adds small, cheap, analog transceiver replacing expensive Ethernet PHY as shown below :

```
Step 1 (external PHY)          Step 2 (digPHY)
---------------------          ----------------
FPGA: MAC + MDIO               FPGA: MAC + PCS + PLCA + TC14 adapter
       │                              │
       │ RMII                         │ 3-pin TC14
       ▼                              ▼
LAN8670 (full PHY)             TC14 PMD only (analog)
       │                              │
       └──── single pair ─────────────┘
```
---
