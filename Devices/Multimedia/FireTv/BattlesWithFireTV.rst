===========================
Battles with my Fire TV
===========================
Author: HanishKVC
version: 20200727IST2025


Core
======

General
---------

Allow going back without deleting in a edit box, by using the back button (there is always prev/fast_back
button to go back with deleting).

	Currently if one wants to change some spelling somewhere in the middle, one has to delete everything
	till that middle position.

Have option to show time somewhere in the screen by default, rathing than having to long press home button.

	With potentially not having a battery backed rtc, I can see the reason why its not default,
	but give option to users who want it and inturn put some message in place of time,
	when it is not sure of current time, because of lack of internet access from last power-on.

		To a great extent it is a internet streaming device, so one can assume that most of the time
		it is being used, it will have network access.

Option to control background audio playback centrally.

	Say a tune-in channel which is playing in the background.

Speech
--------

Generic speech input for edit boxes (at a minimum, latter expanding to other widgets)

	Useful for Searching from within a App, inputing text, selecting from a list etc.

	Had assumed this basic form of speech navigation/input given that Amazon seemed to be pushing alexa skills etc.
	But dissapointed that even basic speech based interaction control, which is decoupled from app specific
	skills and thus can provide more easier speech support enablment to apps, without much or any effort
	from app developers is missing.

		Or is it that amazon will be abondoning fire tv for echo (speaker and show) only world.

		I can see the reason why pure device only speech recognition would have issues with such generic flexibility
		and why alexa skills could be more feasible. But the reality today is that it depends on internet for
		even skills, so does it really matter.

Global search (text/audio) not propagating into apps in a smooth manner.

	Some apps seem to support this, but most dont seem to. May be work with app developers to encourage them to support
	the needed intent or ...


Video
-------

Have option to try and Enforce video quality selection in data usage/monitoring page, by throttling network speed to apps, if required.
because most apps dont seem to provide a option to set the video quality they are ok with.

	Also maybe also provide option for having different category of video qualities.

		User may want a slightly higher quality for Video apps like (Netflix, Prime, ...)
		and relatively lower quality for say news apps.

		Current solution seems to fit everything into a single bucket.

Data Monitoring
----------------

TODO: Not sure if Data usage is being tracked properly. Need to monitor this bit more.


Apps
======

Silk Browser
-------------

Use the option button to toggle between full screen and screen with address+tools bar on top.

May be use FF and FR buttons to skip between links on the page (i.e like tab and shift+tab nav on a web page).


Bloomberg
-----------

Irrespective of which video one selects in the horizontal list, many a times it always plays starting from 1st video in this list.

	Only some times, it played the in-between video correctly.

Force refresh to top of the app, irrespective of where one is in the app page.

	This occurs once every few minutes.

HuffPost
----------

Shows a empty main page with a search icon on top.

WashPost
---------

I think is using non-optimised (thumbnail) images for the video matrix, so given the small memory in the device,
it gets cleared once one scrolls even 2 or so horizontal lists down.

	Also it takes some time to fill up initially even with decent internet access speed.

JioCinema
----------

Please stop trying to be the big state brother/sister on the top, who monitors everything else occuring in the users system.

	Why do you need boot done notification, list of other processes with thumbnail of their last ui state, ...

	Or what ever else be the reason for the same like better targetting of things or ..., seriously please

Tune In
--------

No options like

	to bookmark/star/faviorate the channels one likes and organise them

	to select stream bitrate/quality

Why fine grained location info?


Google Youtube
----------------

You know right, you can program to provide locally saved mapping wrt interests/bookmark, history, etc. i.e without signing in.

	Sometimes people may not want to upload their sign-in details in too many devices, for n number of reasons, you know.
	Its not like people are abondoning you or not wanting to share things with you always, you know (again).


NDTV
------

Allow video quality/bitrate control.

Show date of the video snippets.

India Today
-------------

Allow video quality/bitrate control.

Video playback buffers and adaptive bitrate switching seems to be insufficient.

Aljazera
----------

Doesnt seem to honor the video quality selection set in data usage/monitor section of FireTV->setting|preference.
So ends up eating lot of internet quota one may have. So please do fix this.

HotStar
---------

The video quality setting gets reset to auto, and one is required to explicitly set it to what one wants again and again.


