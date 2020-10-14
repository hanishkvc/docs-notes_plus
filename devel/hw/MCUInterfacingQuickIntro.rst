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




Catalog
#########

Author: HanishKVC

Version: 20201002IST1249

Link: Will live somewhere at https://github.com/hanishkvc/

Remembering: Gandhi

Also thank you to Indian Govt, IIT and CDAC for the Swadeshi Chips initiative.

