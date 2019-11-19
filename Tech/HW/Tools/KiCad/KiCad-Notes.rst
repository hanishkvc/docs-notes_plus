============================================
Quick KiCad Setup and Usage wrt SCH and PCB
============================================
HanishKVC, v20191117IST0108
201X, GPL

C01> Wrt Schematic
-------------------

* GRID: Use 50mil for Component Creation, placement and wiring.

  However I use 25mil or smaller if required during Schematic component creation for 
  graphics or text

  NOTE1: 10mmx2.5mm(5mm) area for LED symbol is fine

  NOTE2: One can use space bar to do relative measurements

* The Simple flow

  # Create component symbols for schematic
  
  # Include them in the schematic
  
  # Assign properties as required
  
  # Interconnect them as required
  
  # Annotate the schematic
  
  # Run ERC
  
  # Generate BOM (I use my own python script to convert from the kicad xml format to csv)
  
  # Generate Netlist

  # Remember to connect PWR_FLAG to power pins (Supply, Gnd), if they are defined as a power supply. 



C02> Wrt PCB
--------------

C02.01> Basic Setup for Component Footprint creation
----------------------------------------------------
    
* GRID: Use 2mil for component/footprint creation, placement and wiring/tracks if possible

* Plan how you want to place the pins/pads wrt the origin point
    
* For Through Hole Pads,

  + keep the drill size atleast 40% smaller than actual Pad size
  
  + Disable (SolderPaste Layer), Enable (F.SolderMask, B.SolderMask, F.Silkscreen)
  
  + Use All Coper Layers

* For SMD Pads,

  + Enable (F.Paste, F.Mask)
  
  + Use only F.Cu copper layer.

* Origin point and Component footprint

  + For through hole based components, use Pin 1 as the origin point in the footprint (just a convention)
  
  + For SMD components, having the origin point to be the middle of the footprint is a normal convention

* Normally the Local clearances settings are left at 0, because

  + PCBNew(PCBEditor)->Dimensions->PadsMaskClearance is setup globally in general to set 
    % the Solder Paste (Normally slightly less than the Pad size)
    % the Solder Mask (Normally slightly larger than the Pad size)

  + Use local clearances settings only in special cases where required.

* NOTE: Use space bar to set the origin point for delta distance calculation as and when required.

* Set Reference & Value properties of footprint to REF** (One in F.Silk and one in F.Fab)

* Create a 3d step/stp file, and assign to the component footprint, if you want to see the component in 3D mechanical view.


C02.02> Some common PCB Editor settings
----------------------------------------

C02.02.01> PCB Layer stackup can be configured under DesignRules->LayerSetup. One can set the
PCB thickness, Number of layers and the layer stackup detail (including meta data / user usable
layers).

C02.02.02> Component/Footprint Pad related settings to do with Solder Paste and Mask setup done 
globally using Dimensions->PadsMaskClearance

* Solder Mask

  + Default 0.2mm clearance for Solder Masks wrt Pads(SMD/TH)

  + Prefer 0.1mm Solder Mask Clearance

  NOTE: Either of these can create a situation, where the solder mask between very small pitch pads
  may get removed. This can create problem with QFN packages with their under the belly hidden SMD Pads.

* Solder Paste

  + Default 1:1 Solder Paste to Pad size mapping.

  + Prefer -0.05mm Solder Paste Clearance

    NOTE: However for very small pads, there may be no solder paste in worst case. So cross check

NOTE: May be rather I should leave it to Default setting of 0.2mm SolderMaskClearance adjustment
and for Cases where this can lead to the solder mask getting eliminated between SMD Pads, I should
setup local clearance settings for these pads. Similarly for Solder Paste by leaving it at the default
of 1:1 size mapping.

C02.02.03> Signal routing related settings to do with track width, spacing/clearance, via dia and
via drill dia are setup using DesignRules->DesignRules=> NetClassesEditor and GlobalDesignRules(Minimum)

Using NetClassesEditor one can setup rules for some common classes of signals like

* NetClass Default
  + 0.25mm Clearance
  + 0.25mm track width
  + 0.50mm Via dia
  + 0.25mm Via drill

* NetClass Power
  + 0.25mm Clearance
  + 0.50mm track width
  + 0.70mm Via dia
  + 0.40mm Via drill

* NetClass HighPower
  + 0.30mm Clearance
  + 0.80mm track width
  + 0.80mm Via dia
  + 0.40mm Via drill

Using GlobalDesignRules (Minimum) one can inturn set absolute minimum settings for thes parameters 

* 0.20mm track width
* 0.40mm Via dia
* 0.25mm Via drill


C02.03> PCB Editor
---------------------

* Display Modes

  + The Default Display mode allows certain features like Dragging instead of Moving, and few other features

  + The OpenGL mode potentailly allows shove and move routing

* One has to select specific modes like Place/Route mode and in turn specific sub mode like Track/Fill/...
  for corresponding operations to be available.

* Remember to select the appropriate routing option, before drawing a track or dragging a track/via

  + Mode (Highlight Collision, Shove, walk around)

  + Enable "Remove redundant tracks", "Automatic neckdown", "Smooth dragged segments" normally

* Create a KeepOut Area around the Layer Alignment Target/Marker so that it creates a distinct mark in the
  copper layers. Otherwise Copper Pour/Fill will eat into this Marker making it useless as it will not be
  visible.

* To reload all footprints in the layout from the library do Right button click on 
  footprint > "Properties" > "Change Footprint(s)" > ”Update all footprints on the board”
 
  i.e Change the footprint in the library. Edit Footprint parameters/properties -> Change Footprint(s).
  Select "Change same footprint" or "Change all"

* Keep Text and Reference rendering enabled and have Value rendering disabled
  In turn while Plotting also enable Reference to be plotted, but disable values from being plotted. 
  So that you see the same data while editing the PCB, as well as while plotting (i.e generating) the gerber files.

  NOTE: visible_elements property in kicad_pcb file corresponds to the setting has to what all is enabled 
  or disabled wrt rendering

* Use 't' to select and move footprints


C02.04> Stitching Copper Pour/Fill/Zones
------------------------------------------
Copper Pour/Fill/Zones of power related nets allows one to do a decent copper balance/thieving at one level. 
At same time it provides a nice reference plane for data signals and helps control EMI and achieve better
signal integrity at the same time.

Inturn if the same net is placed in more than one plane to allow better boxing or surrounding of signals,
then it is useful to stitch these different layers together where they have common overlapping net zones.

To create such zone stitching

* Add single pad test points in schematic which connect to the Signal which has Copper Pour/Fill/Zones in PCB

* Create a Footprint with a single pad which is a Through hole and inturn assign it to these test points

* Set the "Pad connection to Zones" for these footprints to Solid (instead of "Use Zone settings") in 
  "Footprint Properties"->"Local Settings" for these footprints in the PCB.

  NOTE: This is because normally we might have set the Zone's property to use thermal relief.

Use 'b' to refill these pours

C02.05> Generating Plots/Gerbers/Drill files
-----------------------------------------------

The Setup

* It is useful to have the following set to be at same place on the PCB.

  + Place->"Drill and Place Offset"
  
  + Place->"Grid Origin" as well as one of the 
  
  + Place->"Layer Alignment Target marker"

    The Align Layers Marker creates a silk screen paint/marker

    * SilkScreen printing normally has less precision than copper layers

    * Also it doesn't reflect that well for the cameres during automated assembly

    AND NOT a Copper based Fudicial (i.e a copper dot with empty space around it.)
    SO NOT MUCH USE in many cases wrt Pick And Place Automatic alignment.

* Have generated Plot(including drill) and Place Pos files by setting "Use Auxiliary Axis as origin" option.

* For Plotting related files enable

  + Sheet reference on all layers

  + Pads on silkscreen

  + Footprint references (Footprint values is disabled)

  + Tent Vias (dont check "Do not tent vias")

  + PCB edge should be in all layers (Dont check "Exclude PCB edge layer from other layers")

  + auxiliary axis as origin

  + Gerber Options (Extended attributes, Format 4.6 unit mm)

  NOTE: Default line width = 0.1mm

* For Drilling related files

  + Units of Inches

  + Zeros Format = Decimal format

  + Precision = 2:4

  + Drill file options (Merge PTH and NPTH into single file)

  + Drill Origin = Auxiliary axis

* For Fabrication outputs -> Component Position Files

  + Units = mm

  + One file for board

  + With INSERT attribute set


* Generate a unified drill file with info wrt both PTH (Electrical) and NPTH (Mech holes). Many PCB Fab houses
  prefer it this way. However if some prefer seperate files, then uncheck this option.

* Configured generation of a single Component POS file with info for both TOP and BOTTOM side
  Also configured enable Insert attribute for all SMD footprints, so that all SMD footprints
  get mentioned/put in the POS file(Used by Pick and Place Machine).

  NOTE: Cross verify that the automated assembly house verifies that pin 1 of each component is 
  properly aligned and doesn't blindly use the XY co-ords and ROTation info provided to them.

From PCB generate the Gerber and Drill files

* Using PLOT->Gerber files (Edge.Cuts, Cmts.User, F.Cu, B.Cu, F.Mask, B.Mask, F.Paste, B.Paste, F.Silks, B.Silks)

  + Edge.Cuts is used to define the board outline

  + Cmts.User is used to specify the board dimensions as well as comments/Notes for Fab/Assembly in general

* Using PLOT->DXF files (Edge.Cuts, F.Cu, B.Cu)
  
* Using Fabrication Outputs->Footprints Position file

* Also generate the Fabrication Outputs->Footprints report (For your own cross check)



