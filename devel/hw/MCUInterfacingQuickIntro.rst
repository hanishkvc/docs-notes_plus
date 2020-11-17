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
~~~~~~~~

Analog could mean varying voltage / current.

So inturn interface through a ADC or DAC or PWM or Gpio (PWM or simple bi-level interpretation or so) and where required Transistors/Opamps/transducers/...

Digital
~~~~~~~~~

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
~~~~~~

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
~~~~~~~

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
~~~~~~~

If the physical Shakti SoC will follow the spec mentioned at https://gitlab.com/shaktiproject/sp2020 (IIT Shakti team can clarify on this)

then it doesnt seem to indicate ethernet, if so, then the SPI or UART path what I mentioned in the previous message is the way to go for ESP32 - Shakti handshakes,
if you want things to work on both FPGA and potential physical SoC versions of Shakti.

If you will be using the Shakti SoC with DDR memory access, then given the limited RAM on ESP32 side, you may want to access the SDCard (in SPI mode) from Shakti SoC side,
unless the ESP32 is going to be relatively lot more free than Shakti in your setup.

Part 3
~~~~~~~~

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


20201031 Python-C, FPGA-SoC-PC ...
====================================

Part 1
~~~~~~~~

Depending on how real or rather wall clock time sensitive is the image and data processing you will be doing, you may want to do it in C,
if you have that possibility, as the speed difference between C and Python code may be pretty significant for even the python interpretation
around/on-top-off the core image or data processing calls. You may not notice this overhead on a laptop or so which is running in GHz range,
but if the embedded target is going to run at 200-300MHz (Shakti team can confirm on this), chances are it will have a noticable impact,
if you need sufficiently fast responses in your system.

But then again, if there is linux kernel and a sufficiently standard c library / runtime running on top of it, you also have the option of
converting the python code to c using cython module mechanism, but you might still have some minimal triggering vestiges in python, which will
require the python interpreter, unless someone is willing to dig even deeper wrt those python vestiges to eliminate them beyond what cython provides by default.

If binutils, glibc and gcc is available for Shakti, then I would say 99% (unless I am forgetting something fundamental currently) you can be
sure to be able to build and run standard python on Shakti, there will be other libraries you may have to build, but it should be fine.
Note that this should be fine, even if these trinities are cross compile version, rather in that case even your code will have to be cross compiled and nothing else.

Assuming above is true, then whether you are using the Shakti's with DDR or not is what will be critical. If it is the DDR based ones, then the
python based path could be still fine for you. However if it is the Non DDR version, then memory constraint may not allow using standard python
and you will have to work with c or embedded python (but this wont be easy unless your image/data processing libraries are pure python).
But then again given that you have mentioned about camera and image processing etc, the Non DDR version may not be the right SOC choice for you,
unless we are talking about really very small image resolutions.

NOTE:

Also if your idea was to capture the images using that camera module with esp32 in it and then process it on Shakti, then depending on the image resolution
and speed required in your end application, soft shakti + fpga may be the path you may have to pursue in which case some of the image processing can be
off-loaded to a custom logic on fpga, to get the required speed.

Do profile your end use case assuming single threaded (with NO mmx or sse extensions, this is important especially for image or vector/SIMD optimisable end use cases)
processing occuring at 180-300MHz provided the end physical SOC will be in the 300-500MHz or so range (the reason why I have reduced the speed to be only around 60%
is because of the differences that will be there wrt caching/out-of-order execution[not there]/branch prediction/hyper-threading [not-there] between Shakti and your
PC's processor; Shakti team may be able to give the info wrt relative performance between these SOCs and some specific PC processor, then based on the benchmarks used
the teams can make their own assumptions about possible performance in the end SoC for their application), and then decide whether to go with

a) python or c code running on either FPGA based or Physical Shakti SOC or

b) rather if fpga custom logic is required, in which case physical Shakti SOC is no longer a straight forward answer, and you need to use soft Shakti on fpga,

Hope I have been sufficiently coherent above, Best wishes to all.


Part 2
~~~~~~~~

Just to complete out my previous post, based on a quick glance, there is the newlib based and glibc based gcc tool chains for RiscV.

a) Newlib based tool chain is best for the E Class Shakti. For someone interested in python, chances are, getting the normal python
running will involve lot of effort (and isnt worth the effort for the Non DDR version), one of the embedded pythons will be the
right option here either way, if one wants python.

b) WHile the C Class Shakti should have the glibc based gcc tool chain. And getting normal python running here should be relatively straight forward.


If moving logic from pc to shakti, one needs to keep in mind

N1) the lack of SIMD, FPU, Out-Of-Order execution as well as

N2) lower clock speeds, single hardware thread and

N3) differences that will be there wrt Cache, branch prediction, ...

and profile accordingly and identify the application mips requirements and then decide on python or c or a combination with fpga
(the advantage one gets with the soft-ip core version of Shakti).

Hope these help, as a initial rough guide, as one explores further.


20201031 Extending Shakti Verilog-BSV
========================================

Currently I havent looked beyond the user space in RiscV, nor much into Shakti, but still if I am to hazard a guess I would assume that
the hypervisior mode will have to mess with the flow of atleast some aspects of exception handling, memory and io management and potentially some instructions also
so if you are looking at modifying the existing RiscV core, then you may be forced to do it in Bluespec, potentially as Shakti's RiscV core is in it.

Now you may be able to do it on top of the generated verilog code also, but then it may turn out to be bit too much of a moving target with updates to either
Shakti's RiscV core or Bluespec's verilog generator. Nor will others be able to update the Shakti logic easily in a way that it doesnt upset your changes on top
done at a different stage of the generation flow.

However as I have noted at the beginning, this is my initial guess.


20201115 Linux, SDK, PlatformIO, Vivado, MediaServer, Storage
===============================================================

What is what
~~~~~~~~~~~~~~~

When one has a idea, it requires to be translated into a format which can be understood and acted on by the available
underlying mechanism right.

Now the underlying mechanism could be predefined arithematic or logical processing elements provided by a CPU (either
hardwired or soft core) or basic digital building blocks or their equivalents like that provided by a FPGA.

Now in either of these cases, one requires tools to convert the idea specified in a relatively high level simple format
into the underlying mechanism. So the two tools you have mentioned in your list help with these. Compared to a normal pc
or mobile environment, slightly differently in a way here one eats ones own dog food here.

When there are too many things to do, which inturn require access to multiple (potentially shared) resources, having some logic
which manages these in a possibly optimal way based on demand and availability will be helpful right. Now I am sure you know
which in your list provides this.

Sometimes it helps to speed up things, if the intricacies of certain things/subsystems are already handled for you, so that
you can concentrate on your end application which builds on top of these. The drivers/libraries help with these.

Media Server
~~~~~~~~~~~~~~~

Think of the different use cases you will be satisfying and the underlying mechanisms/operations involved to achieve the same and
the time available at hand to achieve the same at runtime. So also how cpu or io intensive the underlying operations are going to be.

You may require to worry about storage, transport/network and if you are going to be bit sophisticated then even transcoding. And
chances are you require to worry about getting these to work sufficiently parallely at runtime.

Identify has to how cpu or io intensive these operations are. Inturn when the same resource is required to be used like say CPU or
a specific IO path are they fast enough to allow interleaved use without impacting the end use case.

If you have parallel resources / paths, which can be used, then interleaving can be avoided or reduced.

Look at the bitrates of the media you will be serving and speed of the io path (or rather paths as the case may be here) and check
if it can be satisfied or not. Also chances are double or multi/ring buffering may help you all many a times, when things are tight.

Now if you are supporting transcoding or so, then chances are you may require the vector/simd support to do things in realtime
with 300MHz or so speeds.

FPGA / End SOC / ...
~~~~~~~~~~~~~~~~~~~~~~~

If you are going to be limiting yourselves to the FPGA, and want to go beyond what is provided, then you have lot of flexibility, as
you can always add more suitable io controllers to the internal bus of the softcore.

Equally if you want to try and support both the FPGA as well as the End SOC later, then things are going to be bit more tight.
Profile the io paths and if required SIMD (dont remember if the Softcores currently supports this, check the documentation and IIT
team). And then decide whether you can do things directly from this core and or you require to offload some of the processing to
additional external logics and or if some simple filesystem with contiguous allocations can aid or so ...

Identifying, mapping and inturn profiling the basic operations, involved in your end use cases, on this platform would be the first
step to get a feel of how simple or tight the things are going to be and then based on it decide on interleaving or using parallel
resources or offloading and or ...

NOTE: I have kept the response purposefully bit vague, while hopefully still providing some hints/guidance at a high level, so that
you all can explore and understand the things/impacts practically on your own.


20201115 ePaper Device with Battery, UI framework
===================================================

If you are looking at using paper display (I assume epaper) and battery, then

a) chances are power is critical to you, in which case check with IIT team wrt the power consumption of the end soc chips which
they are planning.

b) Equally these epaper displays have low refresh rate and so can also be managed by low speed io paths, allowing them to be
interfaced with these SOCs (A normal Display cant be interfaced directly to these SOCs unless you are taking the FPGA path and
maybe a ParallelRGB or LVDS based display controller). So also not sure if the regular Android or Ubuntu GUI is the right display
manager/subsystem for your end application. A framebuffer based or wayland based simple UI may be the path to take wrt GUI.

You have to think of the memory which will be required by your end use cases, so chances are the SOC with few 100s of KBs of
ram access is not suitable for your application nor for linux. Which leaves the SOCs with 10s or 100s of MBs of DDR access the
right SOC choice for you.

Whether you use 32bit or 64bit version doesnt matter much, but for the differences in the pipeline and the end speeds and how
much of it you require for your end application. If you are looking at something like a ebook reader or so, then the simpler pipeline
based 32bit SOC may actually be good enough, if you put specific effort to develop few optimised applications, but either way
definetly you cant be thinking along fancy UI or so.

Hope this helps.


20201116 Shakti SoftCore, Other FPGAs
========================================

NOTE: Havent actively worked on FPGAs for almost 2 decades now (cost-power- vs end_utility has been a issue, unluckily for me
;-), but still as I noticed few questions related to this and maybe no response (or I missed it, in which case sorry), and as even I
may want to look at this in future, so just had a quick glance at the git source of these soft cores on the web just now and based
on it what I feel is

You need to look at

core_config.inc - which defines the fpga used and the top level fpga source

Makefile - which includes the config file and rules to build the files for programming the fpga (which inturn links into tcl folder)

fpga_top.v and constraints.xdc - which define the pins needed and inturn how it is mapped to a current fpga device used.

So if you want to move it to a new FPGA + board, then look at the constraints and map it to equivalent/suitable pins for this new
board, and inturn update the constraints file accordingly. Also update the FPGA define. (As the interfaces used/exposed are not
that fancy, so hopefully may not be that much of a issue).

You will also have to update the references and details wrt the fpga, flash, memory, adc, ... parts in the files in the tcl subfolder.
i.e the tcl/mig/... files.

Also you have to cross check that timing closure is achieved as required, and logics are matched to suitable resources in the
FPGA, in a efficient way, where possible/needed. Maybe the IIT team can give details about which are the logics/paths that need
to be cross verified to be doubly sure things will be fine, wrt their implementation. If one is moving to a tighter and or less
flexible/powerful FPGA, is when some of these may become a issue. Also currently not sure how using BSV to generate verilog
impacts things wrt specifying and tracking these.

NOTE: The above is a note based on a quick glance at the files, without looking into the tools involved nor the files in detail, on top
with a rusty memory wrt the domain now, so take these with a pinch of salt. However hopefully this should still give a rough idea
as to what and all to look at. Maybe some one from the IIT team or someone who is using fpga's more actively currently can fill in
more details and potentially the appropriate helper tools to use to generate/modify some of these files and so and more.


20201117 Ram, Storage, SPI (2, QuadSPI) - My funny Alice In Wonderland
========================================================================

Part 1
~~~~~~~~

Peripheral connectivity
----------------------------------

Look at the interfaces provided by the SoC as well as required by the peripheral, that will help you conclude whether they both can
talk the same language. Or which is the right peripheral to select.

Looking at these SoCs (if you are targeting the end standalone SoCs and not just the FPGA softcore ones) you dont have the
traditional parallel IO nor high speed differential serial IO busses so Legacy PATA or current SATA or NVMe based storage cant be
directly connected to these SoCs.

However it does have SPI and you will be able to find either SPI based NAND chips or you can interface SD card over the SPI bus
(albiet in 1 wire mode rather than the 4 wire mode supported by native SD phy layer). And if you are taking the SPI NAND chip path,
then currently the SPI interface from these SoCs seem to only support the normal SPI and not the Dual or Quad SPI, so you will be
limited wrt the throughput accordingly, if it continues to remain the case.

As for RAM, if you are using anything other than Pinaka, you already have external DDR memory access available i.e in Parashu and
Vajra. In case of Pinaka you could interface SPI SRAM technically if required.

DO NOTE that SPI bus uses a SEPARATE chip select line, so one can always MULTIPLEX few SPI devices on the same SPI bus, if
required, by using some additional chip select logic to select between the different devices connected to the same bus.

Query to IIT Team
----------------------------

The documentation seems to indicate that the SoCs support two SPI busses, even the spi bsv file seems to indicate the same, but
looking at the fpga_top as well as the constraints.xdc, it seems like currently only the SPI bus connected to the on board Flash chip is
enabled and not the 2nd SPI bus. Is there any specific reason for the same.

This would also mean that if one wants to experiment with SPI devices, then on the current FPGA based SoftCores it may be difficult
(with the default configuration released by IIT team) because the only enabled SPI bus is connected to the Flash and so one will have
to lift the SlaveSelect/ChipSelect pin (and add the required additional chip select logic) while connecting wires to other pins of the
flash and then do what ever SPI interfacing that they may want to do.

Equally the DQ2 and DQ3 signals dont seem to be enabled, again any reason for the same.

OR one will be required to change the configuration i.e fpga_top, constraints file (and any other, if requried)

Am I reading things correctly and is it going to be so complex to interface a SPI device, OR have I goofed up somewhere.

Also do note that I am infering this by looking at the files which are available on the git repository on the web. If these files are
dynamically generated as part of the build process and inturn if they enable both SPI busses as expected, then may be you may want
to update the new files into git. Otherwise by looking at what is currently uploaded to gitlab git repository, I feel there is a issue with
experimenting with SPI devices if required.

Also do note that I dont have access to Arty A7 board, so I have just looked at the schematic and images available on the web wrt this
board.

Please do correct me, if my reading wrt the SPI interfacing situation is wrong. I feel it will be useful to many who may be looking into
these, one way or the other


Part 2
~~~~~~~

I think I have realised my goof up, I had forgotten about the pin muxing, so it seems to be exposed throu pinmux
as mentioned in Soc.bsv.

However please do respond about my query wrt DQ2 and DQ3 being disabled, which would reduce the speed achievable
over SPI a lot, for devices which support QuadSPI.

NOTE: Downloading the source from git and grepping helped. Even thou the README had the pinmux detail, which I had
read sometime back, but had forgotten about it, and these queries by many wrt interfacing, made me want to check
if there was something else I had missed out and created that castle in the air about missing 2nd SPI ;-)




Catalog
#########

Author: HanishKVC

Version: 20201002IST1249

Link: Will live somewhere at https://github.com/hanishkvc/

Remembering: Gandhi

Also thank you to Indian Govt, IIT and CDAC for the Swadeshi Chips initiative.

