## 1. Introduction

This folder is a physical-equipment shopping and reference guide — the hardware actually used across wireless, physical-access, and hands-on VAPT engagements, alongside the general-purpose computing gear that supports a home lab. Unlike the rest of this vault, it's not a learning sequence; it's a catalog, organized into three notes by category.

## 2. Contents

| Note | Covers |
|---|---|
| [[Wireless_and_RF_Hardware]] | Wi-Fi adapters, SDR, and RFID/NFC tools for Folder 8 (Wireless_Pentesting) |
| [[Physical_Access_and_Payload_Devices]] | USB-based payload/implant devices and a portable pentest dropbox, relevant to physical engagements and Folder 10 |
| [[Computing_and_Storage_Accessories]] | Storage, memory cards, hub, and general laptop guidance for a home lab |

## 3. Legal and Ethical Note — Read This First

Every device catalogued here is a **legitimate commercial security research tool**, sold openly and used daily by professional pentesters. That said, several of them — RFID/NFC cloners, USB keystroke-injection devices, network implants — are functionally identical to what a malicious actor would use, and several carry real legal/import considerations. Before buying or using anything in this folder:

- Owning this hardware is legal in most jurisdictions (including India); **using it against any system, card, or network you don't own or aren't explicitly authorized to test is not** — the same boundary covered in [[Legal_and_Ethical_Considerations]] applies to hardware exactly as it does to software techniques.
- A few items (Flipper Zero especially) have faced customs friction or import scrutiny in some countries — see the specific note in [[Wireless_and_RF_Hardware]].
- Cloning an access card (RFID/NFC) you don't own, even "just to see if it works," can constitute unauthorized access under laws like India's IT Act — only ever do this on your own hardware or with explicit written authorization.

## 4. Budget Reality Check

This is genuinely expensive hardware to acquire all at once — a full kit across all three notes runs well into six figures in INR / low four figures in USD if bought new. A sensible approach for a student:

```
Tier 1 (~₹8,000-15,000 / ~$100-180): One Wi-Fi adapter (Alfa AWUS036ACH) + a Raspberry Pi 4
                                       — covers most of Folder 8 plus a portable lab dropbox
Tier 2 (+~₹15,000-30,000 / +~$180-350): Add a Flipper Zero or Proxmark3 Easy for RFID/NFC work
Tier 3 (+~₹15,000-30,000 / +~$180-500): Add Hak5 gear (Rubber Ducky, LAN Turtle, Bash Bunny) for
                                          physical/USB attack simulation
```

None of this is required to learn VAPT — most techniques in Folders 3-10 can be practiced entirely on virtual machines and home-lab networks with zero hardware spend. This folder is for when you want hands-on physical/RF practice specifically.

## 5. A Note on Prices and Links

Every price and link in this folder was checked at the time of writing but **hardware pricing fluctuates constantly and stock changes**, especially for niche security tools sold by a handful of specialist retailers. Treat every number here as approximate — always verify the current price and confirm the listing is genuine (not a random unofficial reseller) before buying, especially for Flipper Zero and Proxmark3, where India-specific retail is thin and many cheap listings are unofficial clones.

---
*Related: [[Legal_and_Ethical_Considerations]] (Folder 1), [[Wireless_Pentesting]] (Folder 8), [[Post_Exploitation_and_Privilege_Escalation]] (Folder 10)*
