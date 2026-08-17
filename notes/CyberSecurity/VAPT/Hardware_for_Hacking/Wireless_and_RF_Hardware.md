## 1. Introduction

Hardware for Wi-Fi, RF, and RFID/NFC work — directly supports [[Wireless_Pentesting]] (Folder 8) and the RFID/NFC side of physical access testing. Most laptop built-in Wi-Fi chipsets don't support monitor mode/packet injection, which is why a dedicated external adapter is the first purchase most people make in this category.

## 2. Alfa AWUS036ACH — Dual-Band Wi-Fi Adapter

![Alfa AWUS036ACH](https://alfa-network.eu/media/catalog/product/cache/af62ca62d6d9cb3bf8cf5eda3a38a799/a/w/awus036ach-c_deviceleftfront.jpg)

**Use:** Dual-band (2.4/5GHz) USB 3.0 adapter with monitor mode and packet injection — the most commonly recommended all-round adapter for WPA2/WPA3 auditing with Aircrack-ng/hcxdumptool. Still an actively sold (non-discontinued) model.

| Region | Approx. Price | Buy Link |
|---|---|---|
| India | ~₹8,700 (varies, watch for the Type-C revision at a different price point) | [Amazon.in](https://www.amazon.in/Alfa-AWUS036ACH-Wireless-Wi-Fi-Adapter/dp/B00VEEBOPG) |
| US | ~$80 | [Amazon.com](https://www.amazon.com/ALFA-AWUS036ACH-%E3%80%90Type-C%E3%80%91-Long-Range-Dual-Band/dp/B08SJC78FH) |

## 3. Alfa AWUS036NHA — Single-Band Wi-Fi Adapter (Discontinued by Manufacturer)

**Use:** The classic single-band 2.4GHz adapter with the Atheros AR9271 chipset — for years the default Aircrack-ng-recommended card thanks to rock-solid Linux driver support.

**⚠️ Note:** Officially marked end-of-life on Alfa's own site. Still findable through retailer stock while supplies last, but no longer in active production — treat listings as "while stock lasts," not a permanently available product.

| Region | Approx. Price | Buy Link |
|---|---|---|
| India | ~₹7,750-8,200 | [Flipkart](https://www.flipkart.com/alfa-network-awus036nha-usb-adapter/p/itmb225e23442126) · [Amazon.in](https://www.amazon.in/Alfa-AWUS036NHA-150Mbps-Wireless-Adaptor/dp/B004Y6MIXS) |
| US | ~$36 (frequently out of stock) | [Amazon.com](https://www.amazon.com/Alfa-AWUS036NHA-Wireless-USB-Adaptor/dp/B004Y6MIXS) |

## 4. HackRF One — Software-Defined Radio

![HackRF One](https://greatscottgadgets.com/images/h1-preliminary1-445.jpeg)

**Use:** Open-source SDR (1MHz-6GHz) for RF signal analysis, transmission, and protocol reverse-engineering — sub-GHz devices, GSM analysis, and general RF research beyond Wi-Fi/Bluetooth. Complements Flipper Zero's sub-GHz features with far wider spectrum coverage.

**Note:** Pricing varies significantly by bundle (bare board vs. antenna/case kit) — expect a wide range.

| Region | Approx. Price | Buy Link |
|---|---|---|
| India | ~₹16,000-25,000 depending on bundle | [Zbotic.in](https://zbotic.in/product/hackrf-one/) |
| US | ~$330 (check official reseller list for current stock) | [Great Scott Gadgets — Official Resellers](https://greatscottgadgets.com/hackrf/one/) |

**Legal note:** No issue for RF *receiving*; RF *transmission* is subject to normal spectrum regulations in your country (in India, governed by WPC rules) — know what frequencies/power levels you're legally allowed to transmit on before testing.

## 5. Flipper Zero — Multi-Tool

![Flipper Zero](https://flipper.net/cdn/shop/files/product_flipper-zero_1.png?v=1765368572&width=3840)

**Use:** A pocket multi-tool covering RFID/NFC read-write-clone, sub-GHz (garage doors, key fobs), infrared, BadUSB, and Bluetooth — the most beginner-friendly single device for physical/RFID security research.

| Region | Approx. Price | Buy Link |
|---|---|---|
| India | No official direct-to-India sale; unofficial resellers exist at a steep markup (~₹30,000+) with unconfirmed authenticity/warranty | Buy from the [official Flipper store](https://flipper.net/products/flipper-zero) with international shipping instead — see their [shipping & duties explainer](https://blog.flipper.net/explaining-shipping-and-taxes/) |
| US | $199 (official) | [flipper.net](https://flipper.net/products/flipper-zero) |

**⚠️ Legal/import note:** Flipper Zero has faced real customs scrutiny and even proposed import bans in some countries (e.g., a 2024 Canadian proposal, citing vehicle-theft misuse) due to its sub-GHz/RFID capabilities. It's not illegal to own in India, but budget for possible customs delays/duties on import, and don't expect a smooth "buy on Amazon.in" experience.

## 6. Proxmark3 Easy — RFID/NFC Research Device

![Proxmark3 Easy](https://dangerousthings.com/wp-content/uploads/product_10260_proxmark3easy-back-angle.jpg)

**Use:** Low/high-frequency RFID and NFC card reading, cloning, sniffing, and cryptographic analysis (e.g., MIFARE Classic attacks) — the standard tool for access-card security research, one step more serious/capable than Flipper Zero's RFID feature.

**Variant note:** Proxmark3 comes in tiers — **Easy** (~$90-120, beginner/learning-focused, the recommended starting point, commonly runs the community "Iceman" firmware) vs. **RDV4** (~$300-360+, professional-grade RF front-end). Start with Easy.

**⚠️ Important:** Most cheap Proxmark3 units sold on Amazon/AliExpress/Indian marketplaces are unofficial clones of wildly varying quality, not the genuine article — buy from a reputable specialist if budget allows.

| Region | Approx. Price | Buy Link |
|---|---|---|
| India | ~₹3,000-8,000 for clone units (quality varies — verify seller reputation first) | [Snapdeal listing example](https://www.snapdeal.com/product/pm3-proxmark-3-easy-30/685034189703) |
| US | $90-117 for the genuine Iceman-firmware Easy | [Dangerous Things (reputable specialist)](https://dangerousthings.com/product/proxmark3-easy/) |

**Legal note:** Owning a Proxmark3 is legal; cloning an access-control card you don't own without authorization is not — see the folder-level legal note in [[00_Overview]].

## 7. Interview Questions

1. Why is a dedicated external Wi-Fi adapter usually necessary for wireless pentesting, rather than a laptop's built-in card? → **Most built-in laptop Wi-Fi chipsets don't support monitor mode or packet injection, which wireless auditing techniques (via Aircrack-ng and similar) require**
2. What's the practical difference between Flipper Zero and Proxmark3 for RFID/NFC work? → **Flipper Zero is a more beginner-friendly, general-purpose multi-tool with RFID/NFC as one of several features; Proxmark3 is a more specialized, capable device purpose-built for deeper RFID/NFC research and cryptographic analysis**
3. Why does HackRF One complement rather than replace Flipper Zero's sub-GHz capability? → **HackRF covers a much wider RF spectrum (1MHz-6GHz) for general signal analysis and transmission, while Flipper Zero's sub-GHz feature is narrower and more consumer-focused (garage doors, key fobs)**
4. Why should someone be cautious about cheap Proxmark3 listings on general marketplaces? → **Most inexpensive units on Amazon/AliExpress/Indian marketplaces are unofficial clones of inconsistent quality, not the genuine Proxmark3 Easy — reputable specialist retailers are safer despite higher cost**

## 8. Key Points

- A dedicated **Wi-Fi adapter with monitor mode/injection support** (Alfa AWUS036ACH being the current recommendation) is the essential first wireless hardware purchase.
- **HackRF One**, **Flipper Zero**, and **Proxmark3 Easy** each cover a different slice of the RF/RFID/NFC space — general SDR, beginner multi-tool, and specialized card research respectively.
- **Flipper Zero and genuine Proxmark3 have thin or nonexistent official India retail** — budget for import shipping/duties and watch out for unofficial clones.
- Every device here is legal to **own**; legality depends entirely on **what you use it against** — see [[Legal_and_Ethical_Considerations]].

---
*Related: [[00_Overview]], [[Wireless_Pentesting]] (Folder 8), [[Legal_and_Ethical_Considerations]] (Folder 1)*
