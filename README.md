# &#x1F50A; fdk-acc-esp32-arduino

<img align="right" alt="GIF" src="https://media1.giphy.com/media/13HgwGsXF0aiGY/giphy.gif" width="250" height="auto" />
<br />
This is a fork of the https://github.com/pschatzmann/arduino-fdk-aac library
<br /><br />
The changes are in memory allocation, the idea is to use external SPI RAM instead of the strained built-in DRAM.
<br />
<br />

#### &#x26A0; DISCLAIMER (important for comercial use)

**© License**

It's complicated. Fraunhofer FDK AAC has a **non-free license &#x1F61E;**. Talk to your lawyer before using it in any project. 
More info at section [The Fraunhofer FDK AAC license problem](#the-fraunhofer-fdk-aac-license-problem)


<span style="color:#F2645C; font-size:0.9em; font-weight:bold">&#x1F3AF; Recommended free codec:</span>

Consider using the robust, modern and free **Opus Codec** https://opus-codec.org 👌

##### Arduino Opus library with special optimization for ESP32 Xtensa dual-core (or single-core) 32-bit LX6 microprocessor:
[&#x1F680; https://github.com/mkopa/opus-esp32-arduino](https://github.com/mkopa/opus-esp32-arduino)

<a href="https://github.com/mkopa" style="color: #3F704D; font-size: 0.9em; font-weight: bold;">April 2022 Marcin Kopa</a>

---

### &#x1F4BB; Installation

```
lib_deps =
    https://github.com/mkopa/fdk-acc-esp32-arduino
lib_ldf_mode = deep+
build_flags = 
    -DBOARD_HAS_PSRAM               # Enable SPI PSRAM
    -mfix-esp32-psram-cache-issue   # For older IDF compatibility
    -Wno-unused-variable
    -Wno-unused-but-set-variable
    -Wno-unused-function
    -Wno-format-extra-args
# board_build.partitions = no_ota.csv
```
If you have a problem when compiling or running the program, you probably need to configure the partition tables.

If you're in a hurry, and you haven't configured partition tables before, just clear it:
```
board_build.partitions = no_ota.csv
```

<a href="https://github.com/mkopa" style="color: #3F704D; font-size: 0.9em; font-weight: bold;">April 2022 Marcin Kopa</a>

---

### &#x1F4DC; A brief description of the *arduino-fdk-acc* library's changes

#### DRAM
For most purposes, the standard libc `malloc()` and `free()` functions can be used for heap allocation without any special consideration.

However, in order to fully make use of all of the memory types and their characteristics, ESP-IDF also has a capabilities-based heap memory allocator. If you want to have memory with certain properties (for example, DMA-Capable Memory or executable-memory), you can create an OR-mask of the required capabilities and pass that to `heap_caps_malloc()`.

DRAM uses capability `MALLOC_CAP_8BIT` (accessible in single byte reads and writes). When calling `malloc()`, the ESP-IDF `malloc()` implementation internally calls `heap_caps_malloc(size, MALLOC_CAP_8BIT)` in order to allocate DRAM that is byte-addressable. To test the free DRAM heap size at runtime, call *cpp:func:heap_caps_get_free_size(MALLOC_CAP_8BIT)*.

Because malloc uses the capabilities-based allocation system, memory allocated using `heap_caps_malloc()` can be freed by calling the standard `free()` function.

##### Note
Due to a technical limitation, the maximum statically allocated DRAM usage is 160KB. The remaining 160KB (for a total of 320KB of DRAM) can only be allocated at runtime as heap.

#### External SPI RAM

When external RAM is enabled, external SPI RAM under 4MiB in size can be allocated using standard `malloc` calls, or via `heap_caps_malloc(MALLOC_CAP_SPIRAM)`, depending on configuration.

<a href="https://github.com/mkopa" style="color: #3F704D; font-size:0.9em; font-weight:bold">April 2022 Marcin Kopa</a>

---
### The Fraunhofer FDK AAC license problem
This is a free license, incompatible with any version of the GNU General Public License (GNU GPL).

However, it comes with an advice of caution. While the Fraunhofer provides a copyright license, it explicitly declines to grant any patent license. In fact, it directs users to contact them to obtain a patent license. Users should act with caution in determining whether they feel comfortable using works under this license.

###### Other changes
Free Software Foundation has also added a new section to their article on License Compatibility and Relicensing, addressing combinations of code. This section was announced in 2017 and helps users in simplifying the picture when dealing with a project that combines code under multiple compatible licenses. They have also added a new entry to their FAQs. It explains what the GNU GPL says about translating code into another programming language.

Read more about the news on [FSF Blog](https://fedoraproject.org/wiki/Licensing/FDK-AAC)

<a href="https://github.com/mkopa" style="color: #3F704D; font-size:0.9em; font-weight:bold">April 2022 Marcin Kopa</a>

---

## Supported Bitrates

The following table provides an overview of recommended encoder configuration
parameters which we determined by virtue of numerous listening tests.

```
-----------------------------------------------------------------------------------
Audio Object Type  |  Bit Rate Range  |            Supported  | Preferred  | No. of
                   |                  |       Sampling Rates  | Sampl.Rate | Chan.
                   |          [bit/s] |                [kHz]  |      [kHz] | 
-------------------+------------------+-----------------------+------------+-------
AAC LC + SBR + PS  |   8000 -  11999  |         22.05, 24.00  |     24.00  | 2
AAC LC + SBR + PS  |  12000 -  17999  |                32.00  |     32.00  | 2
AAC LC + SBR + PS  |  18000 -  39999  |  32.00, 44.10, 48.00  |     44.10  | 2
AAC LC + SBR + PS  |  40000 -  64000  |  32.00, 44.10, 48.00  |     48.00  | 2
-------------------+------------------+-----------------------+------------+-------
AAC LC + SBR       |   8000 -  11999  |         22.05, 24.00  |     24.00  | 1
AAC LC + SBR       |  12000 -  17999  |                32.00  |     32.00  | 1
AAC LC + SBR       |  18000 -  39999  |  32.00, 44.10, 48.00  |     44.10  | 1
AAC LC + SBR       |  40000 -  64000  |  32.00, 44.10, 48.00  |     48.00  | 1
-------------------+------------------+-----------------------+------------+-------
AAC LC + SBR       |  16000 -  27999  |  32.00, 44.10, 48.00  |     32.00  | 2
AAC LC + SBR       |  28000 -  63999  |  32.00, 44.10, 48.00  |     44.10  | 2
AAC LC + SBR       |  64000 - 128000  |  32.00, 44.10, 48.00  |     48.00  | 2
-------------------+------------------+-----------------------+------------+-------
AAC LC + SBR       |  64000 -  69999  |  32.00, 44.10, 48.00  |     32.00  | 5, 5.1
AAC LC + SBR       |  70000 - 239999  |  32.00, 44.10, 48.00  |     44.10  | 5, 5.1
AAC LC + SBR       | 240000 - 319999  |  32.00, 44.10, 48.00  |     48.00  | 5, 5.1
-------------------+------------------+-----------------------+------------+-------
AAC LC             |   8000 -  15999  | 11.025, 12.00, 16.00  |     12.00  | 1
AAC LC             |  16000 -  23999  |                16.00  |     16.00  | 1
AAC LC             |  24000 -  31999  |  16.00, 22.05, 24.00  |     24.00  | 1
AAC LC             |  32000 -  55999  |                32.00  |     32.00  | 1
AAC LC             |  56000 - 160000  |  32.00, 44.10, 48.00  |     44.10  | 1
AAC LC             | 160001 - 288000  |                48.00  |     48.00  | 1
-------------------+------------------+-----------------------+------------+-------
AAC LC             |  16000 -  23999  | 11.025, 12.00, 16.00  |     12.00  | 2
AAC LC             |  24000 -  31999  |                16.00  |     16.00  | 2
AAC LC             |  32000 -  39999  |  16.00, 22.05, 24.00  |     22.05  | 2
AAC LC             |  40000 -  95999  |                32.00  |     32.00  | 2
AAC LC             |  96000 - 111999  |  32.00, 44.10, 48.00  |     32.00  | 2
AAC LC             | 112000 - 320001  |  32.00, 44.10, 48.00  |     44.10  | 2
AAC LC             | 320002 - 576000  |                48.00  |     48.00  | 2
-------------------+------------------+-----------------------+------------+-------
AAC LC             | 160000 - 239999  |                32.00  |     32.00  | 5, 5.1 
AAC LC             | 240000 - 279999  |  32.00, 44.10, 48.00  |     32.00  | 5, 5.1 
AAC LC             | 280000 - 800000  |  32.00, 44.10, 48.00  |     44.10  | 5, 5.1
-----------------------------------------------------------------------------------
```

<a href="https://github.com/mkopa" style="color: #3F704D; font-size:0.9em; font-weight:bold">April 2022 Marcin Kopa</a>

---

# Arduino AAC Encoding and Decoding Library

I was looking for a way to encode sound PCM data to the AAC or MPEG data format on some Arduino Devices. That's when I found  the [Fraunhofer FDK AAC library](https://en.wikipedia.org/wiki/Fraunhofer_FDK_AAC). 

The Android-targeted implementation of the Fraunhofer AAC can be used for encoding and decoding, uses fixed-point math and is optimized for encoding on embedded devices/mobile phones. The library is currently limited to 16-bit PCM input. So this seems to be the perfect match to be used in Arduino based Microcontrollers.

I have forked the [fdk-aac](https://github.com/mstorsjo/fdk-aac/tree/v2.0.1) project,  converted it to an Arduino library and provided a simple Arduino friendly API. The only caveat is, that I have removed all optimized processor specific code (e.g. for ARM, 386 and mips), so that it will compile with the same code on all environements.


## Decoding Example

```
#include "AACDecoderFDK.h"
#include "BabyElephantWalk60_aac.h"

using namespace aac_fdk;


void dataCallback(CStreamInfo &info, INT_PCM *pcm_data, size_t len) {
    int channels = info.numChannels;
    for (size_t i=0; i<len; i+=channels){
        for (int j=0;j<channels;j++){
            Serial.print(pcm_data[i+j]);
            Serial.print(" ");
        }
        Serial.println();
    }
}

AACDecoderFDK aac(dataCallback);

void setup() {
    Serial.begin(115200);
    aac.begin();
}

void loop() {
    Serial.println("writing...");
    aac.write(BabyElephantWalk60_aac, BabyElephantWalk60_aac_len);    

    // restart from the beginning
    delay(2000);
    aac.begin();
}

```

## Encoding Example

```
#include "AACEncoderFDK.h"
#include <stdlib.h>     /* srand, rand */

using namespace aac_fdk;

void dataCallback(uint8_t *aac_data, size_t len) {
    Serial.print("AAC generated with ");
    Serial.print(len);
    Serial.println(" bytes");
}

AACEncoderFDK aac(dataCallback);
AudioInfo info;
int16_t buffer[512];

void setup() {
    Serial.begin(115200);

    info.channels = 1;
    info.sample_rate = 10000;
    aac.begin(info);
}

void loop() {
    Serial.println("writing 512 samples of random data");
    for (int j=0;j<512;j++){
        buffer[j] = (rand() % 100) - 50;         
    }
    aac.write(buffer, 512);
}

```

## Logging

The log level can be defined with the help of the global LOGLEVEL_FDK variable. By default it is set to FDKWarning. You can change it by calling e.g.
```
LOGLEVEL_FDK = FDKInfo;
```

## Installation

For Arduino, you can download the library as zip and call include Library -> zip library. Or you can git clone this project into the Arduino libraries folder e.g. with

```
cd  ~/Documents/Arduino/libraries
git clone pschatzmann/arduino-fdk-aac.git
```

This project can also be built and executed on your desktop with cmake:

```
cd arduino-fdk-aac
mkdir build
cd build
cmake ..
make
```
  

## Class Documentation

- The [generated Class Documentation](https://pschatzmann.github.io/arduino-fdk-aac/html/annotated.html) can be found here.
- I also suggest that you have a look at [my related Blog](https://www.pschatzmann.ch/home/2021/08/13/audio-decoders-for-microcontrollers/)


## Compile Issues / Supported Architectures

On some processors we get compile errors because the arrays are too big.  
Please uncomment the #define PIT_MAX_MAX line in the AACConstantsOverride.h file. 

All examples have been written and tested on a ESP32.