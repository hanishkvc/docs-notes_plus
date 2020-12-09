######################
Generic devel notes
######################


Debugging
===========

Crashes
----------

Check the basic setup is fine

	cat /proc/sys/kernel/core_pattern

	ulimit -c unlimited

	ulimit -a

On ubuntu check these also

	systemctl enable apport.service

	apport-cli


Documentation
===============

Man
----

To find some function or define within man page use

        Thorough version => man -K string_to_search

        Quick version => man -k string_to_search or apropos string_to_search

