# NeoPixelBus

> [!NOTE]
> This repository is a fork of [Makuna's NeoPixelBus](https://github.com/Makuna/NeoPixelBus).
> It preserves the original project's work and documentation while adding
> further ESP32 adaptations maintained by OrtMed.

## Changes Included in This Fork

- Added support for the new RMT transmit and encoder APIs introduced with Arduino-ESP32 Core 3.x and ESP-IDF 5.x.
- Preserved backward compatibility with Arduino-ESP32 Core 2.x and the legacy RMT driver.
- Added ESP32-S3 compatibility to the LCD/GDMA method by replacing GPIO functions no longer exposed by ESP-IDF 5.x and correcting resource cleanup when initialization fails.
- Enabled DMA for logical RMT channel 0 on ESP32 targets that support RMT DMA. Other channels continue to use the dedicated RMT symbol memory.
- Increased the interrupt priority used by non-DMA RMT transmissions to improve waveform stability while Wi-Fi, ESP-NOW, or other interrupt-intensive subsystems are active.
- Added compile-time configuration macros for selecting the DMA-enabled RMT channel, buffer sizes, non-DMA symbol memory, and interrupt priority.

### RMT Interrupt Priority

Non-DMA transmissions performed by the ESP-IDF 5.x RMT driver depend on an
interrupt handler to refill the peripheral's symbol memory while a frame is
being transmitted. If this interrupt is delayed for too long by Wi-Fi,
ESP-NOW, or another subsystem, the RMT peripheral may run out of symbols.
Addressable LEDs can interpret the resulting pause as a reset signal and latch
an incomplete frame, causing some LEDs to briefly display incorrect colors.

To reduce this risk, this fork uses interrupt priority `3` by default, which is
the highest priority normally compatible with the FreeRTOS-aware RMT driver.
This gives RMT refill operations precedence over lower-priority interrupt work
without disabling Wi-Fi or ESP-NOW.

A higher priority does not reserve CPU time permanently: it only takes effect
while the short RMT interrupt handler is running. Nevertheless, applications
with strict interrupt-latency requirements may override the priority through
`NEOPIXELBUS_RMT_INTR_PRIORITY`.

When RMT DMA is available and enabled, the complete symbol stream is supplied
through DMA. This substantially reduces dependence on interrupt refill timing,
which is why DMA is enabled for logical channel 0 by default on supported
targets.

### Optional RMT Configuration

The following macros may be defined before including `NeoPixelBus.h`:

- `NEOPIXELBUS_RMT_DMA_CHANNEL`: logical RMT channel that uses DMA. The default is `RMT_CHANNEL_0`.
- `NEOPIXELBUS_RMT_DMA_BUFFER_SYMBOLS`: number of symbols reserved for the DMA-enabled channel. The default is `512`.
- `NEOPIXELBUS_RMT_MEM_BLOCK_SYMBOLS`: symbol memory assigned to each non-DMA channel. By default, one hardware memory block is reserved per channel.
- `NEOPIXELBUS_RMT_INTR_PRIORITY`: interrupt priority used by the ESP-IDF 5.x RMT driver. The default is `3`.

RMT DMA availability and the number of usable RMT channels depend on the ESP32
target. On targets without RMT DMA support, the library automatically uses
conventional non-DMA transmission.

---

## Original Documentation

The documentation below was provided by the original NeoPixelBus author.

[![Donate](https://img.shields.io/badge/paypal-donate-yellow.svg)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=6AA97KE54UJR4)

Arduino NeoPixel library

A library to control one wire protocol RGB, RGBW, and RGBWW leds like APA106, SK6812, WS2811, WS2812 and WS2813 that are commonly refered to as NeoPixels and two wire protocol RGB like Lpd8806, APA102 and SK9822 commonly refered to as DotStars.
Supports most Arduino platforms.  

For quick questions and support:  
* [Try the new Github Discussions](https://github.com/Makuna/NeoPixelBus/discussions)  
* [Discord NeoPixelBus Invitation](https://discord.gg/c6FrysvZyV) or if you are already a member of [Discord Server NeoPixelBus](https://discord.com/channels/789177382221119519/789177382221119521)  
* Or jump on Gitter   
[![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/Makuna/NeoPixelBus?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)  

For bugs, make sure there isn't an active issue and then create one.

## Documentation
[See Wiki](https://github.com/Makuna/NeoPixelBus/wiki)

## Installing This Library (preferred, you just want to use it)
Open the Library Manager and search for "NeoPixelBus by Makuna" and install

## Installing This Library From GitHub (advanced, you want to contribute)
Create a directory in your Arduino\Library folder named "NeoPixelBus"
Clone (Git) this project into that folder.  
It should now show up in the import list when you restart Arduino IDE.





