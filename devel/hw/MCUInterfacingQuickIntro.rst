#########################################
Interfacing to MCU - Quick Generic Intro
#########################################

Hardware Level
#################

For interfacing any Component to a MCU/SOC one has to look from the following angles, among others

Voltage/Current
=================

What is the voltage levels of the component's io pins and inturn the operating voltage levels of the MCU's IO pins.

How much current does the component's io pin sink-into or source-from the MCU's io pin AND inturn how much can the MCU support/withstand

   * one at a individual pin level

   * and another at the level of a group of pins within a IO Port.

      If multiple components are connected across different pins, dont forget any overall limits.

NOTE: If the MCU cant meet the needs from either voltage or current perspective, then one will sense/drive it in a decoupled manner
using transistors, opamps, relays (inc ssr), bridges, etc in between.

NOTE: Remember to look at the operating electrical charactersistics and not just the absolute maximum rating.


Analog/Digital
================

Does the component interface to the MCU over a digital or analog interface.

Analog
--------

Analog could mean varying voltage / current.

So inturn interface through a ADC or DAC or PWM or Gpio (PWM or simple bi-level interpretation or so) and where required Transistors/Opamps/transducers/...

Digital
---------

Digital could mean

Simple BiLevel (Hi/Lo)

   Use GPIO.

Low speed Serial like SPI, I2C, UART, ...

   Using GPIO or function specific io block

High speed Serial like LVDS, USB, MIPI, PCIE, SATA, HDMI, ...

   Normally function specific io blocks, unless you are looking at low speed USB or so.
   In some cases the physical interface could be same, while mainly protocol handshake logic would vary.
   The frequency involved will also need to be cross checked, among others.

Parallel like Display/Video Parallel, General Purpose Memory IO, PATA, ...

   Normally function specific io blocks. Some SOCs (MCUs less likely) could have General purpose Memory IO which is flexible.
   However now a days due to the difficulties involved with high speed parallel busses, its no longer parallel buses, but more of
   multiple/parallel channels of serial io.

Handshake IO like Interrupts, Wait/Request/Ack, Enables, Reset, ...

   Interrupts

      Level(Low, High), Edge (Low-High, High-Low) triggered.

      Normal or WakeUp interrupt

         Wakeup interrupt related components would ideally require to be on a seperate always-on low power path.


GPIO
------

When using GPIOs do keep these in mind

   * Understand the reset state of the GPIO used and put suitable PullUp or PullDown if required, so that things are in a sane state.

   * Do check if the GPIO is push-pull kind or open-collector(/drain) kind.

      And accordingly use a suitable internal or external pull-up if open-collector type. Which also helps interface with components across a wider voltage range.

   * Note the LogicLowHIGHVoltage and LogicHighLOWVoltage between the interfacing entities (i.e MCU and Component), especially if across different families.

   * Check the pin/signal is active high or active low

NOTE: For complex chips interfacing into a SOC, even the power and clock sequencing could be critical.


FPGA
=====

If initially testing out on a FPGA, look at the pin mapping between the soft MCU and the FPGA pins.

Ensure Sourcing and Sinking limits are satisfied wrt IO banks/ports.

Look into the Pinout, Pin planning and IOBank/Port specifications of the FPGA used.


Towards a product
===================

For any exposed to outside (directly or indirectly) IO pins, look at these as required

* ESD protection

* voltage clamping and current limiting, if required.

* EMI filtering, if required, using beeds, rc/lc filters, chokes, etc

* Isolation, if required.

Decoupling and Filtering of Supply paths.

Grounding strategy.

...


Software
##########

General
========

Take note of any watch dog timers and inturn make use of them and or disable them.

Depending on the IO interface over which the peripheral connects, one will have to

a) Identify the memory map of the registers associated with functionalities mentioned below and inturn use them as required.

   * GPIO / PinMux

   * IO Subsystem used (like I2C/SPI/UART/...)

   * Clock and Power Controls

   * Interrupt controller and Timer, if needed.

b) Configure the pins in the required state/mode.

   * GPIO 

      Input or Output or

      Internal PullUp or PullDown if any.

   * or Function specific

c) provide interrupt handler OR poll (not recommended for linux and other multitasking environments) the component involved.

d) Configure for Non-Cached IO, except for large device data buffers

   For SOCs with MMU one will have to also worry about mapping the device memory into the virtual address space.

   Keep in mind write/io barriers, invalidate/flushing, etc

e) If required, Enable clock and power to the corresponding module in the MCU/SOC. Including setting up of the appropriate clock paths.

f) use DMA where required, including scatter-gather if supported.

Remember to switch to low power modes when done. While ensuring that resources which need to be active in low power mode can remain so.


Linux
======

For a generic device one will usually implement a char device driver based kernel module.

Validate user space data, before use in kernel.


Shakti-SOC and Related
########################

Some responses which I gave on the forum, which maybe useful in general

20201014 Peripheral support, boot
==================================

Some suggestions/thoughts in general

a) Serial Transcievers - As long as the UART logic used is not restricted to legacy clock speeds (as stupidly done on many super io chips),
one can always connect transceivers for CAN or 485 or ... and use them at higher speeds than legacy uart speeds also. i.e better to have
a flexible clock path/multipliers based uart and leave bus specific niceties to an external transciever.

b) IO Voltage - With low power in mind, better to stick to 1.8 v IO which can tolerate 3v. And let people use level conversion (transistors/...) for 5V.

And or have Open Drain/Collector IO mode (rather than push-pull) for some of the GPIO pin banks so that one can interface to 1.8 or 3.3 or 5v as required
by using external pull-ups.

c) Beyond UART, I2C and SPI, it would be good if either SDIO or USB is supported, as one will be able to interface to most other io indirectly through them.

At the same time with SPI support one can always interface to SDIO devices potentially using the mandatory SPI mode of SDIO.
So interfacing to other standards/peripherals through SDIO is still possible but albeit at a lower speed compared to full SDIO transfer modes.

d) I would assume builtin RF support is going to be potentially further down the line, however in the short term one would need to use uart/spi/sdio(in spi mode)
to interface to wifi/bt/... offloader chips.

e) support for validated (if not secured/verified) boot, at a minimum using a locked/fused in hash, if not signature based ones, would be a useful 1st step.


20201030 Interfacing ESP32, SDCard, Python, ...
=================================================

Part 1
-------

Assuming the initial C Class Shakti physical SOC that will be released by IIT++ later wont support Ethernet (Do confirm with IIT once)
(NOTE: I am not talking about the FPGA version, bcas you can always instantiate other io controller logics in a FPGA be it SDIO or
Ethernet or ... and interface it to the internal IO bus of Shakti)

The simplest answer to both your questions is SPI i.e you should be able to interconnect ESP32 as a SPI slave to Shakti,
similarly you should be to talk to a SD card in SPI mode from Shakti.

If both ESP32 and Shakti allow clocking UART to non legacy speeds, even it may help transfer between them.

You will have to look at the maximum clock speeds from both Shakti and ESP32 side wrt SPI and UART and then decide,
chances are SPI clocks may be bit more flexible compared to UART.

At the same time do remember that, If you want even faster io speeds, if you can

a) spare a bunch of GPIO lines on both chips (if they are in the same bank and share a register, even better),

b) spare the CPU (Shakti and ESP32) i.e the CPU is not loaded for sufficient time in-between other activities,

then technically you maybe able to rig up your own software controlled Parallel or Multi Channel/Line Serial interface provided
the GPIOs can be switched (needs to be checked once) fast enough.

It may be best to control the SDCard from ESP32 and use the serial/parallel interface between Shakti and ESP32 to access the files.
How much ram you can spare on either chip and the cpu loading at either of them will also help you decide, whether it makes sense or not finally.

NOTE: As I havent checked Shakti currently, the below is theoretical, but should be definitely possible

If linux is up and running on Shakti along with a sufficiently normal c library, you should be always able to cross compile any other c code
(be it the regular python flavor or one of the embedded python flavors) to run on it, assuming a linux distro is already not available for riscv.
However if a linux distro is available you should be able to repurpose binaries from that linux distro provided library versions match sufficiently.

Also if you have no storage device connected to Shakti other than spi boot Flash, then the simplest for you will be to have a ramdisk with
a minimal filesystem including your version of python in it, bundled along with the linux kernel in the flash.

I am sure the Shakti team will give more appropriate answers, based on the knowledge they have about the final physical SOC which they are planning,
if that is what you are interested in. However the SPI thing which I mentioned should work irrespective of anything else. Best wishes.

Part 2
-------

If the physical Shakti SoC will follow the spec mentioned at https://gitlab.com/shaktiproject/sp2020 (IIT Shakti team can clarify on this)

then it doesnt seem to indicate ethernet, if so, then the SPI or UART path what I mentioned in the previous message is the way to go for ESP32 - Shakti handshakes,
if you want things to work on both FPGA and potential physical SoC versions of Shakti.

If you will be using the Shakti SoC with DDR memory access, then given the limited RAM on ESP32 side, you may want to access the SDCard (in SPI mode) from Shakti SoC side,
unless the ESP32 is going to be relatively lot more free than Shakti in your setup.

Part 3
--------

Also before I forget, if you decide to code up ur own software controlled parallel (/multi line serial) bus using gpio's
(like I had suggested as a possibility in a previous message), remember that

a) you cant expect the data to be available at the other end immidiately after you have written it. You would have noticied
in timing diagrams of many serial or parallel busses that the clock transition or the read or write enable transition which
triggers the other end to recognise the fresh data occurs bit delayed compared to the command/data line transition, so that
there is enough time for the data/cmd lines to settle. You require to ensure the same in your bus.

b) Also chances are the needed settling time would change when going from the FPGA based Soft SOC to the Physical SOC.

side note: using gpios from across different banks/ports rather than all from the same bank/port, may actually help you get
slightly better throughput sometimes, but then again you shouldnt have to rely on such intricacies normally, bcas rather
it would mean that you are using the wrong chip for your given situation, if you have to rely on it to get ur logic to work
with acceptable performance.

Best wishes.




Catalog
#########

Author: HanishKVC

Version: 20201002IST1249

Link: Will live somewhere at https://github.com/hanishkvc/

Remembering: Gandhi

Also thank you to Indian Govt, IIT and CDAC for the Swadeshi Chips initiative.

