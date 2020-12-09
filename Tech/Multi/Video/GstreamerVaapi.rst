#################################
GStreamer, VAAPI, Fedora, Ubuntu
#################################
HanishKVC, v20201206IST1240

Setup your system
###################

Check VAAPI is available in your system

	vainfo

If required install vaapi drivers for your system

	dnf install libva-intel-driver

	You will have to enable RPMFusion for this.

	apt install i965-va-driver

	Ubuntu seems to require i965 to support the vaapi video codecs on my laptop, even thou it is a Core 8th Gen chipset.
	
	Ubuntu has both i965 and iHD drivers installed and iHD is what is picked up by default. So need to use LIBVA_DRIVER_NAME=i965
	
	Fedora seems to have only i965 driver and not the iHD driver, so this issue is not there.

		export LIBVA_DRIVERS_PATH=/usr/lib/x86_64-linux-gnu/dri/

			The path containing iHD (iHD_drv_video) and or i965 (i965_drv_video), if required.

		export LIBVA_DRIVER_NAME=iHD or
	
		export LIBVA_DRIVER_NAME=i965

Install vaapi plugin for gstreamer

	dnf install gstreamer1-vaapi


Use Gstreamer, Vaapi, ...
##########################

Get Details about a gstreamer object | plugin | element

	gst-inspect-1.0 vaapi

	gst-inspect-1.0 vaapisink

	LIBVA_DRIVER_NAME=i965 GST_DEBUG=*vaapi*:6 gst-inspect-1.0 vaapi

	gst-inspect-1.0 --plugin

		gives the list of plugins

	gst-inspect-1.0 --all

		gives all the plugins and their details

Test vaapisink, Select the display (i.e X11 or DRM or Wayland or ...) to use if required

	gst-launch-1.0 videotestsrc ! vaapisink

	gst-launch-1.0 videotestsrc ! vaapisink display=3

	By default vaapisink used drm display output, which didnt show anything on screen, so had to use display property of vaapisink to make it use wayland display

Play a h264 file

	gst-launch-1.0 filesrc location=a_h264_file.mp4 ! qtdemux ! h264parse ! vaapidecodebin ! vaapisink

	gst-launch-1.0 filesrc location=a_h264_file.mp4 ! qtdemux ! vaapidecodebin ! vaapisink

	gst-launch-1.0 filesrc location=a_h264_file.mp4 ! qtdemux ! vaapidecodebin ! waylandsink

	gst-launch-1.0 playbin uri=file:///path/from/systemrootdir/test.mp4

	Directly specifying vaapih264dec didnt work, had to use vaapidecodebin rather. With it no need to explicitly specify h264parse also.


Debug
========

If gstreamer logic is not behaving properly, then one can set GST_DEBUG to a non zero value to get useful debug log messages. Higher the number more verbose the logs.

	export GST_DEBUG=4

		man -K GST_DEBUG, can help find the man page having info about GST_DEBUG. Value of 1 is Errors and 4 is Info.

	export GST_DEBUG=*vaapi*:6 gst-inspect-1.0 vaapi

		One can even filter the log messages that should be displayed.


pipeline elements interconnection graph
------------------------------------------

Set the GST_DEBUG_DUMP_DOT_DIR env variable, to let gstreamer generate dot files, which contain the graph of the gst elements/plugins and their properties, at different phases of using gstreamer.

	GST_DEBUG_DUMP_DOT_DIR=/tmp/gst999

	gst-launch-1.0 playbin uri=file:///`pwd`/test.mp4 

	dot -Tpdf /tmp/gst999/0.00.06.011171764-gst-launch.PLAYING_PAUSED.dot > /tmp/t.pdf

		Use graphviz to view the dot file graphically, after conversion to a suitable format.


Misc
######

Locally installed documentation for clutter related stuff can be found at

	file:///usr/share/gtk-doc/html/

export GST_VAAPI_ALL_DRIVERS=1
rm ~/.cache/gstreamer-1.0 -rf


