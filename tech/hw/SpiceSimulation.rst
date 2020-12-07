#######################
Quick Spice Simulation
#######################
Author: HanishKVC
Version: 20201106IST1722

Some Basics
############

Remember to use the components from the spice library (which have spice parameters associated with them) and not the components from the normal library.

Remember to attach the 0 ground to a appropriate net in your schematic.

Remember the orientation of the component placed in the schematic, as it determines the sign of the current and or voltage related to it and for sources
wrt other components connected to it. Change the orientation to get a sane flow wrt the signs.

Remember to have some minimal resistance like 1u ohms or so atleast in any given path around/through (i.e one which contains) a voltage source, rather than a dead short.

Remember to not have any nets which are floating wrt ground i.e net0. All nets should connect to net0 through some dc path. So if required add a large
resistance like 1G or so between a floating net and net0.


Components
-----------

R123 resistors
Cabc capacitors
L123 inductors
Mabc L1 L2 K (k = mutual inductance)
D123 diode
Qabc bjt transistor
M123 mosfet
T transmission line

Use Meg for Mega and not M or m.


Analysis
##########

Transient analysis
--------------------

Setup the Voltage source as required. If it is a AC source then ensure that the minimum timestep of transient analysis is atleast 10 times smaller,
so that atleast 10 samples are there for each cycle of the AC source.

For transient analysis with a AC source, one needs to setup the ac waveform characteristic like pulse or sin or pwl or so...

AC analysis
--------------

For AC or DC analysis, one needs to specify the AC and DC magnitude and phase(if reqd for AC) wrt the voltage source used.
The Pulse|Sin|PWL|... defined wrt the Voltage sources spice parameters for transient analysis wont help and wont be used.


Programs
##########

Kicad Notes
-------------

Local net names have a '/' prefixed to their name in the netlist generated.

Place the control command related to analysis that needs to be carried out into a text box in the schematic.


Notes
#######

General
========

A larger capacitor allows a lesser/slow ac signal ie a more dc like signal to pass through.


Misc
=======

? Filters And Impedence ?
---------------------------

Adding a series resistance of around 10 or better still 50 to 75 ohms immidiately after the ac voltage/signal source
for a UHF band pass LC filter as used in funcube/... (butterworth, shunt first, 2nd order, 410 to 465MHz) shows its
ac characteristic in a proper way.



