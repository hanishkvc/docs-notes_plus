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

