# TeensyRA8876-8080
## A RA8876 8080 parallel driver library for Teensy boards and variants.
## CHANGES:
09-07-26:
- Reworked "graphicCursor.ino" sketch. Installed new process_mouse() function for processing mouse delta X and delta Y scaling as well as processing mouse button single, double clicks and object dragging with mouse pointer.


### ************** NEW SPEED IMPROVEMENT!! SEE BELOW. ******************

This library is designed to be used with the TeensyRa8876-GFX-Common library and can be found here:
- https://github.com/wwatson4506/TeensyRA8876-GFX-Common

This driver also uses a new FlexIO library found here:
- https://github.com/KurtE/FlexIO_t4/tree/master

Communication with the Teensy is accomplished using the 8080 parallel mode of FlexIO. 

![https://github.com/wwatson4506/TeensyRA8876-8080/blob/main/extras/MEM_Transfer.jpg](https://github.com/wwatson4506/TeensyRA8876-8080/blob/main/extras/MEM_Transfer.jpg)

## TOUCH SCREEN
The capacitive touch controller now used on the ER-TFTM101-1 is the Goodix GT9271. I2C communication is used with the GT9371 controller.
A minimal driver adapted from the arduino-goodix library can be found here: 
- https://github.com/wwatson4506/Arduino-goodix-GT9271
***

## LIBRARY INSTALLATION
1. Unzip this library to the **Arduino/libraries** folder.
2. Unzip **TeensyRA8876-GFX-Common**  library to the **Arduino/libraries** folder.
3. Download **ILI9341_fonts** [https://github.com/wwatson4506/ILI9341_fonts](https://github.com/wwatson4506/ILI9341_fonts) and install into the **Arduino/libraries** folder.
***

# PINOUTS

### CONNECTING THE TEENSY TO THE ER-TFTM101-1 in FlexIO 8080 mode:
40 pin dual inline connector pinouts for 8080 8/16Bit parallel buss mode can be found here:

https://www.buydisplay.com/download/interfacing/ER-TFTM101-1_CTP_Interfacing.pdf

### Teensy 4.1 <--------------> RA8876
```
      PIN                      PIN

Use These 8 data lines for 8-bit data bus.
- D0  19 --------------------> 15
- D1  18 --------------------> 16
- D2  14 --------------------> 17
- D3  15 --------------------> 18
- D4  40 --------------------> 19
- D5  41 --------------------> 20
- D6  17 --------------------> 21
- D7  16 --------------------> 22
Add The 8 data lines for 16-bit data bus.
- D8  22 --------------------> 23 
- D9  23 --------------------> 24
- D10 20 --------------------> 25
- D11 21 --------------------> 26
- D12 38 --------------------> 27
- D13 39 --------------------> 28
- D14 26 --------------------> 29
- D15 27 --------------------> 30
Control Signals.
- RD  37 --------------------> 05
- WR  36 --------------------> 06
- CS  11 --------------------> 07
- RS  13 --------------------> 08
- RST 12 --------------------> 11
- BL  3.3V (BACKLITE) -------> 14 or  I/O pin.
Power and Grounds
- TFT 5V --------------------> 3,4,37,38
- GND -----------------------> 1,2,13,31,39,40
NOTE: All power and ground pins should be connected.

Touch Screen (ER_TFTM101-1 40 pin dual inline connector)
Teensy 4.1               GT9371
- 3.3V ---------------> CPT_/RST 36
- 28   ---------------> CPT_INT  33
- 25   ---------------> CPT_SDA  34
- 24   ---------------> CPT_SCL  35
```
***
### Dev Board 5                 RA8876

```
      PIN                      PIN

Use These 8 data lines for 8-bit data bus.
- D0  40 <-------------------> 15 
- D1  41 <-------------------> 16
- D2  42 <-------------------> 17 
- D3  43 <-------------------> 18 
- D4  44 <-------------------> 19
- D5  45 <-------------------> 20
- D6  06 <-------------------> 21
- D7  09 <-------------------> 22
Add These 8 data lines for 16-bit data bus.
- D8  32 <-------------------> 23  
- D9  47 <-------------------> 24
- D10 48 <-------------------> 25 
- D11 49 <-------------------> 26 
- D12 08 <-------------------> 27
- D13 07 <-------------------> 28
- D14 50 <-------------------> 29
- D15 51 <-------------------> 30
Control Signals.
- RD  52 --------------------> 05
- WR  56 --------------------> 06
- CS  11 --------------------> 07
- RS  13 --------------------> 08
- RST 12 --------------------> 11
Power and Grounds
- BL  3.3V (BACKLITE) -------> 14
- TFT 5V --------------------> 3,4,37,38
- GND -----------------------> 1,2,13,31,39,40
NOTE: All power and ground pins should be connected.
      I2C Capacitive Touch screen not tested yet on this device.
```
***
### CONFIG FILE
## UPDATE: A little over 200ms performance improvement can be had by defining "USE_FAST_CLOCK_MODE" in the config file shown below.
Shown below is the difference in clock speeds for fast and normal modes. All examples have been tested in fast mode without issue.
Defaults to normal mode...
```
#ifdef USE_FAST_CLOCK_MODE
#define OSC_FREQ	10  // OSC clock frequency, unit: MHz.
#define DRAM_FREQ	160 // 120 // SDRAM clock frequency, unit: MHz. RA8876
#define CORE_FREQ	130 // 120 // Core (system) clock frequency, unit: MHz. 
#define SCAN_FREQ	35  // 50  // Panel Scan clock frequency, unit: MHz.
#else
#define OSC_FREQ	10  // OSC clock frequency, unit: MHz.
#define DRAM_FREQ	120 // 120 // SDRAM clock frequency, unit: MHz. RA8876
#define CORE_FREQ	120 // 120 // Core (system) clock frequency, unit: MHz. 
#define SCAN_FREQ	50  // 50  // Panel Scan clock frequency, unit: MHz.
#endif
```
Both 8080 Parallel and SPI libraries have config file.
Config file for FlexIO 8080 parallel:
```
/* RA8876_Config-8080.h
 A file to place user defines and configs.
*/

#ifndef RA8876_CONFIG_H
#define RA8876_CONFIG_H

//********************* NEW OPTION ****************************
// Uncomment next line to increase SDRAM and CORE clock speeds.
//#define USE_FAST_CLOCK_MODE
//*************************************************************

// Select 8 or 16 for your bus width.
#define RA8876_8080_BUS_WIDTH 8

// Set the bus speed in megahertz. 
#define BUS_SPEED 20 //Available settings 2,4,8,12,20,24,30,40,60,120

// The following are the default defines for the Teensy 4.1 and Dev Board 5 (DB5).
// External backlight control connected to this Arduino pin. Can be controlled with PWM.
// Otherwise 3.3v
// Un-comment one of these defines for pin control of backlite.
//#define BACKLITE 5 or change to your pin choice.
//#define BACKLITE 29 // Kurt's DB5 shield.

#if defined(ARDUINO_TEENSY41)
// Hardware defines T4.1
#define RA8876_8080_CS 11
#define RA8876_8080_DC 13
#define RA8876_8080_RESET 12
// Example usage in sketch: RA8876_t41_p tft = RA8876_t41_p(RA8876_8080_DC,RA8876_8080_CS,RA8876_8080_RESET);

#define RA8876_D0 19
#define RA8876_RD 37    // FlexIO3:19: RD
#define RA8876_WR 36    // FlexIO3:18: WR

#elif defined(ARDUINO_TEENSY_DEVBRD5)
// Hardware defines DB5 board and Kurt's shield
#define RA8876_8080_CS 53
#define RA8876_8080_RESET 54
#define RA8876_8080_DC 55
#define RA8876_D0 40
#define BACKLITE 29
#define RA8876_RD 52    // FlexIO3:10: RD
#define RA8876_WR 56    // FlexIO3:11 WR
#endif

// Uncomment to use FT5206 touch. (Not used on the ER-TFTM1010-1)
#define USE_FT5206_TOUCH 

#endif // RA8876_CONFIG_H
```

### MINIMAL SKETCH EXAMPLE
```
// sketch.ino

#include "Arduino.h"
#include "RA8876_Config_8080.h" // Global config file.
#include <RA8876_t41_p.h>

// RA8876_8080_DC, RA8876_8080_CS and RA8876_8080_RESET are defined in
// src/RA8876_Config_8080.h.
RA8876_t41_p tft = RA8876_t41_p(RA8876_8080_DC,RA8876_8080_CS,RA8876_8080_RESET);

void setup() {
  Serial.begin(115200);
  while (!Serial && millis() < 3000) {} //wait for Serial Monitor (3 seconds).

  // Set 8/16bit bus mode. Default is 8bit bus mode. Must be called before tft.begin(BUS_SPEED).
  tft.setBusWidth(RA8876_8080_BUS_WIDTH); // RA8876_8080_BUS_WIDTH is defined in
                                          // src/RA8876_Config_8080.h. 
  tft.begin(BUS_SPEED); // RA8876_8080_BUS_WIDTH is defined in
                        // src/RA8876_Config_8080.h. Default is 20MHz. 
  ... // Rest of user setup code.
}

void loop() {
 ... // Users loop code.
}

```

***

## NOTES:

- ###  setBusWidth() if used must be called before begin().

- ### The T4.1 uses FlexIO2 which does not support DMA. Non-blocking async mode is     supported.

- ### There is still an issue with DMA being used with a buss speed greater than 12MHz.

***



## Examples Folder Listings:

Example sketches can be found in the TeensyRA8876-8080 and TeensyRA8876-SPI examples folders.

- gauges  ---------------------->  A Sumotoy example originally created for the RA8875.
- graphicCursor ------------->  A demonstration of defining and using a graphical mouse pointer. A USB mouse is required. Also demonstrates single and double mouse button clicks.
- graphics --------------------->  Demonstrates common graphics usage, lines, circles, rectangles and more.
- ILI_ADA_FontTest4 -------> Demonstrates usage of ILI9341 and Adafruit fonts on the RA8876.
- MemoryTransfer -----------> Shows usage of many BTE (Block Transfer Engine) functions.
- pipTest ------------------------> Example of PIP (Picture In Picture) usage.
- RA8876_pictureEmbed --> Displays 16Bit color images. Also demonstrates rotation.
- RA8876Rotate --------------> Also demontrates use of rotation.
- scroll ---------------------------> Simple demonstration of scrolliing screen up and down.
- TestCases --------------------> Folder containing various sketches used for testing.
  - DMA_ASYNC_testing -------> Tests usage of DMA and ASYNC together.
  - DMA_Testing_2 ---------------> More DMA tests using SDRAM on the Dev Board 5. Not compatible with  Teensy 4.0, 4.0 or MicroMod.
  - Kurts_RA8876_p_FB_and_clip_tests --> Frame buffer and clip tests.
  - RA8876p_readPixels ---------> Tests color bar write and read accuracy with rotation.
  - TestDMA -------------------------> Another DMA test for displaying images.
  - TestDMA_FB --------------------> More ASYNC and DMA testing using frame buffer callbacks.
  - writeRotatedRect_ra8876 ----> Displays images and color bars with rotation.
- treedee -------------------------> Demonstrates a spinning 3D wire cube.
- UserDefinedFonts -----------> Demonstrates loading  user define fonts into pattern ram.   Fonts can also be loaded from an SD card or USB drive.

***
# CREDITS
Major work on this library was done by the following PJRC forum members:

@mjs513, @KurtE, @MorganS, @rezo and @K7MDL (Keith).

They added functions to be compatible with other display libraries, performed a major rework and helped in debugging the code. The 8080 parallel FlexIO driver is based on work done by @rezo.

***

# REFERENCES
ER-TFTM-101-1 10.1" TFT from BuyDisplay:
- https://www.buydisplay.com/serial-spi-i2c-10-1-inch-tft-lcd-module-dislay-w-ra8876-optl-touch-panel
- https://www.buydisplay.com/download/ic/RA8876.pdf

PJRC Forum Threads:
- https://forum.pjrc.com/threads/58565-RA8876LiteTeensy-For-Teensy-T36-and-T40
- https://forum.pjrc.com/index.php?threads/ra8876-parallel-display-library-testing.75345/
- https://forum.pjrc.com/index.php?threads/recommendations-for-10-tft-display-with-touchscreen-for-teensy-4-1.75666/
***
# This is WIP.   USE AT YOUR OWN RISK.  There are no guarantees when using this library. More to come.
