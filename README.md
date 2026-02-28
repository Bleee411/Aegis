# AEGIS | Open Source Hardware Security Module & Encrypted Storage

![License](https://img.shields.io/badge/license-GPLv3-blue.svg)
![Hardware](https://img.shields.io/badge/Hardware-CERN--OHL--S-orange.svg)
![Platform](https://img.shields.io/badge/Platform-RP2040-red.svg)

**AEGIS** is a professional-grade, open-source Hardware Security Module (HSM) and encrypted storage device. It leverages the processing power of the **Raspberry Pi RP2040** and the industrial-strength security of the **Microchip ATECC608B** Secure Element to provide a "Zero Trust" environment for your sensitive data.

Unlike standard encrypted USB drives, AEGIS treats the host computer as potentially compromised. It requires **physical user interaction** (tactile buttons) and hardware-isolated cryptographic keys that never leave the security chip.



---

## ✨ Key Features

* **Hardware Key Isolation:** Master keys are stored inside the **ATECC608B** Secure Element. They are never exposed to the MCU or the host PC.
* **Physical Confirmation:** Sensitive operations (like encryption or data access) must be confirmed using the onboard **OK** button.
* **On-the-Fly AES-256:** High-speed encryption handled by the RP2040 using keys derived from the Secure Element.
* **Custom USB Protocol:** Uses a proprietary **USB Bulk Transfer** protocol instead of Mass Storage Class, preventing the OS from automatically mounting or indexing the drive.
* **Encrypted MicroSD Storage:** Scalable storage where data is encrypted before being written to the physical flash sectors.

---

## 🛠 Hardware Specifications

| Component | Specification |
| :--- | :--- |
| **MCU** | Raspberry Pi RP2040 (Dual-core ARM Cortex-M0+) |
| **Secure Element** | ATECC608B (ECC, SHA-256, AES-128, True RNG) |
| **Display** | 0.91" Monochrome OLED (128x32 px) via I2C |
| **Interface** | 3x Tactile Buttons (PREVIOUS, OK, NEXT) |
| **Storage** | MicroSD Card Slot (SPI Interface) |
| **Connection** | USB Type-C (Custom Vendor Class) |

---

## 📟 Communication Protocol

AEGIS uses a structured binary frame designed for reliability and protection against replay attacks.

### Packet Structure
Frames are typically 4KB to match MicroSD sector sizes:

| Offset (Bytes) | Field | Type | Description |
| :--- | :--- | :--- | :--- |
| `0-1` | **SYNC** | `uint16` | Magic bytes `0xAA55` for synchronization |
| `2` | **CMD** | `uint8` | Command code (Ping, Auth, Data, Finish, etc.) |
| `3` | **SEQ** | `uint8` | Sequence number for packet tracking |
| `4-5` | **LEN** | `uint16` | Payload length (0 - 4096 bytes) |
| `6...` | **PAYLOAD** | `bytes` | Raw data/metadata to be processed |
| `End (2b)` | **CRC16** | `uint16` | CRC16-CCITT checksum of the entire frame |



---

## 🚀 How it Works

1.  **Selection:** You select files/folders via the AEGIS Python Client on your PC.
2.  **Auth Request:** The PC sends an `AUTH` command. AEGIS wakes up and prompts on the OLED: **"ENCRYPT? [OK/CANCEL]"**.
3.  **Key Derivation:** Upon physical button press, ATECC608B generates a unique session key.
4.  **Streaming:** The PC streams data in blocks. The RP2040 encrypts each block using AES-256 and writes it directly to the MicroSD card.
5.  **Finalization:** The file system is synced, and the session key is securely wiped from the RP2040 RAM.



---

## 📂 Project Structure

* `/firmware`: C++ source code for RP2040 (Pico SDK & TinyUSB).
* `/client`: Python 3 desktop application (requires `pyusb` and `crcmod`).
* `/hardware`: KiCad schematics, PCB layouts, and 3D enclosure files.

---

## 📜 License

This project is fully Open Source:
* **Software/Firmware:** [GNU GPLv3](LICENSE)
* **Hardware (PCB & Case):** [CERN-OHL-S v2](LICENSE-HARDWARE)

---
