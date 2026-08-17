## 1. Introduction

USB-based payload devices and a portable pentest platform — the hardware used to demonstrate physical/USB attack vectors during authorized engagements (e.g., "what happens if an employee plugs in a found USB drive"), and to run a self-contained lab or dropbox from a single small board. These are the most sensitive items in this vault's hardware catalog — read the legal note in [[00_Overview]] before buying any of them.

## 2. USB Rubber Ducky

![USB Rubber Ducky](https://shop.hak5.org/cdn/shop/products/usb-rubber-ducky_mk2.jpg?v=1659974440&width=1200)

**Use:** A keystroke-injection device that looks like a USB flash drive but registers as a keyboard to the target computer, typing a pre-programmed payload (in DuckyScript) at very high speed — used to demonstrate how quickly an unattended, unlocked machine can be compromised via a malicious "found" USB device.

| Region | Approx. Price | Buy Link |
|---|---|---|
| India | Not sold in India directly — order internationally | [Hak5 Official Store](https://shop.hak5.org/products/usb-rubber-ducky) (ships internationally; often sold out — check stock) |
| US | $100 (official) | [Hak5 Official Store](https://shop.hak5.org/products/usb-rubber-ducky) |

## 3. Bash Bunny (Mark II)

![Bash Bunny](https://shop.hak5.org/cdn/shop/products/bash-bunny-mk2_001_c58d9658-b151-4328-af26-11eef3c47355_600x.jpg?v=1624910237)

**Use:** A multi-vector USB attack platform — goes beyond keystroke injection alone, combining USB Ethernet/serial/mass-storage emulation with an onboard Linux system and scriptable payloads, for more sophisticated USB-based attack simulations than the Rubber Ducky alone.

| Region | Approx. Price | Buy Link |
|---|---|---|
| India | Not sold in India directly — order internationally | [Hak5 Official Store](https://shop.hak5.org/products/bash-bunny) (often sold out — check stock) |
| US | $200 (official) | [Hak5 Official Store](https://shop.hak5.org/products/bash-bunny) |

## 4. LAN Turtle

![LAN Turtle](https://shop.hak5.org/cdn/shop/products/lan-turtle_600x.jpg?v=1592003446)

**Use:** A covert network-access device disguised as a generic USB-to-Ethernet adapter — once plugged between a target machine and the network, it provides remote shell access and can be used for network reconnaissance and man-in-the-middle style testing, all while looking like an unremarkable adapter.

| Region | Approx. Price | Buy Link |
|---|---|---|
| India | Not sold in India directly — order internationally | [Hak5 Official Store](https://shop.hak5.org/products/lan-turtle) |
| US | $80 (official) | [Hak5 Official Store](https://shop.hak5.org/products/lan-turtle) · also listed on [Hacker Warehouse](https://hackerwarehouse.com/product/lan-turtle/) |

## 5. O.MG Cable

![O.MG Cable](https://shop.hak5.org/cdn/shop/products/CtoA-Wht-Basic_600x.jpg?v=1668118562)

**Use:** A USB cable that looks and functions completely normally for charging/data transfer, but contains a hidden implant capable of keystroke injection and remote payload delivery over its own Wi-Fi — used to demonstrate the risk of using untrusted charging cables, a scenario ordinary users have essentially no way to visually detect.

| Region | Approx. Price | Buy Link |
|---|---|---|
| India | Not sold in India directly — order internationally | [Hak5 Official Store](https://shop.hak5.org/products/omg-cable) (often sold out — check stock) |
| US | $150 (official) | [Hak5 Official Store](https://shop.hak5.org/products/omg-cable) |

## 6. Raspberry Pi 4 Model B (8GB) — Portable Pentest Dropbox

![Raspberry Pi 4](https://robocraze.com/cdn/shop/files/Raspberry_Pi_4_8GB_RAM.webp?v=1744783340)

**Use:** A credit-card-sized single-board computer that, loaded with Kali Linux (or a purpose-built distro like PwnPi/P4wnP1), becomes a low-cost, low-power, dischargeable "dropbox" — a device left running discreetly on a client's network (with authorization) for remote access during longer engagements, or simply a portable lab machine.

**Note:** Global chip-supply issues have kept Raspberry Pi pricing well above its original ~$75 (8GB) baseline for extended periods — check current official reseller pricing at [raspberrypi.com](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/) before assuming these numbers are current.

| Region | Approx. Price | Buy Link |
|---|---|---|
| India | ~₹18,500 (elevated vs. historical baseline — verify current price) | [Robocraze](https://robocraze.com/products/raspberry-pi-4-model-b-8-gb-ram) · [Amazon.in](https://www.amazon.in/Raspberry-Pi-8GB-Desktop-Computer/dp/B08B9XS3B6) |
| US | ~$165 (also elevated — verify current price via official resellers) | [PiShop.us](https://www.pishop.us/product/raspberry-pi-4-model-b-8gb/) · [Official Reseller List](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/) |

## 7. Interview Questions

1. What's the fundamental attack technique behind the USB Rubber Ducky, Bash Bunny, and O.MG Cable? → **They all abuse the USB Human Interface Device (HID) specification — the target computer trusts anything presenting itself as a keyboard, so the device can type an attacker-controlled payload at high speed without needing to exploit any software vulnerability**
2. How does the Bash Bunny go beyond what the Rubber Ducky alone can do? → **It adds an onboard Linux system and multiple simultaneous USB device emulation modes (Ethernet, serial, mass storage) rather than just keystroke injection, enabling more complex multi-stage attack simulations**
3. Why is the O.MG Cable considered a particularly effective demonstration tool for security awareness training? → **It's visually and functionally indistinguishable from a normal charging/data cable, meaning an ordinary user has no practical way to identify it as compromised just by looking at it**
4. Why would a pentester use a Raspberry Pi as a "dropbox" during an engagement, and what must be true before doing so? → **It's small, cheap, and low-power enough to leave running discreetly on a network for extended remote access during a longer engagement — but this must be explicitly authorized in the Rules of Engagement (see [[Scoping_and_Rules_of_Engagement]]), since covertly leaving a device on a client network otherwise crosses well outside authorized testing**

## 8. Key Points

- **USB Rubber Ducky, Bash Bunny, LAN Turtle, and O.MG Cable** are all Hak5 (or Hak5-ecosystem) products, sold directly from their official store — none currently have normal India retail, so budget for international shipping.
- All four exploit **trust in USB device types** (keyboard, network adapter, storage) rather than a software vulnerability — a fundamentally different attack class from network/web exploitation.
- A **Raspberry Pi 4** doubles as a portable lab machine or an authorized "dropbox" for extended-access engagements — Raspberry Pi pricing has been well above historical baseline due to ongoing supply constraints.
- These are the most sensitive items in this catalog — always re-read [[Legal_and_Ethical_Considerations]] before using any of them outside a lab you own.

---
*Related: [[00_Overview]], [[Post_Exploitation_and_Privilege_Escalation]] (Folder 10), [[Scoping_and_Rules_of_Engagement]] (Folder 1)*
