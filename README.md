# stm32-bootloader-led-demo

## 📖 Introduction
This project demonstrates a simple secure bootloader concept on the STM32F407 microcontroller.  
- **Bootloader (Sector 0):** Blinks an LED for a finite number of cycles, then transfers control.  
- **Main Application (Sector 2):** Runs independently, blinking the LED forever.  

The goal is to showcase bootloader design, memory layout, and controlled application handover.

---

## 📦 Block Diagram

```
+-------------------+
|   STM32F407 MCU   |
+-------------------+
       |
       v
+-------------------+       +-------------------+
|   Sector 0 Flash  |       |   Sector 2 Flash  |
|   Bootloader Code |       |   Main App Code   |
+-------------------+       +-------------------+
       |                           |
       v                           v
   Blink LED (finite)        Blink LED (forever)
       |
       v
   Jump to Main
```
---

## 🔌 Interface Specifications
- **GPIO (LED):**
  - Bootloader: Blink N times (e.g., 25 cycles).
  - Main: Blink continuously.
- **Flash Memory Layout:**
  - Sector 0 → Bootloader.
  - Sector 2 → Application.
- **Control Flow:**
  - Bootloader ends loop → sets VTOR (vector table offset) → jumps to Main reset handler.

---

## 🔄 State Machine
```
[RESET]
   |
   v
[BOOTLOADER_INIT]
   |
   v
[BLINK_LED_N_TIMES]
   |
   v
[JUMP_TO_MAIN]
   |
   v
[MAIN_INIT]
   |
   v
[BLINK_LED_FOREVER]
```
---

## 📑 Sequence Diagram
```
Reset
  |
  v
Bootloader executes (Sector 0)
  |
  v
Blink LED N times
  |
  v
Set VTOR to Sector 2
  |
  v
Jump to Main Reset Handler
  |
  v
Main executes (Sector 2)
  |
  v
Blink LED forever
```
---

## 🛡️ Threat Model
- **Threats:**
  - Tampered main application in Sector 2.
  - Bootloader bypass (if BL is modified).
  - Incorrect jump address → crash.
- **Mitigations:**
  - Lock Sector 0 (write protection).
  - Optional: Validate application header before jump.
  - Watchdog reset if jump fails.

---

## 🚀 Getting Started
1. Flash Bootloader binary to **Sector 0**.  
2. Flash Main Application binary to **Sector 2**.  
3. Reset MCU → observe LED blinking finite times (Bootloader), then continuous blinking (Main).  

---

## 📂 Repository Structure
```
/bootloader
│   ├── Core
│   │   ├── Inc/
│   │   └── Src/
│   │       └── main.c
│   ├── Startup/
│   │   └── startup_stm32f407.s
│   ├── Drivers/
│   ├── STM32F407VGTX_FLASH.ld
│   ├── STM32F407VGTX_RAM.ld
│   └── Bootloader.ioc

/main_app
│   ├── Core
│   │   ├── Inc/
│   │   └── Src/
│   │       └── main.c
│   ├── Startup/
│   │   └── startup_stm32f407.s
│   ├── Drivers/
│   ├── STM32F407VGTX_FLASH.ld
│   ├── STM32F407VGTX_RAM.ld
│   └── Main_App.ioc

/docs
│   ├── README.md
```
---

## 📌 Notes
- This is a **demo project** for portfolio/resume purposes.  
- Security features (hash/signature validation) can be extended later.  
- Current focus: memory layout, bootloader handover, and basic threat awareness.

