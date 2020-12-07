==================
Python c modules
==================
HanishKVC, 2020

References
===========

https://docs.python.org/3/c-api/[intro.html | dict.html]
https://docs.python.org/3/extending/[index.html | extending.html | building.html]
https://docs.python.org/3.5/library/ctypes.html


Notes
=======

function doc strings
----------------------

Func's PyDoc_STRVAR should contain '--\n\n' after the 1st line, to make the 1st
line appear as the signature of the function when help(funcname) is called.

Be responsible with the py objects
-----------------------------------
   
New references need to be decremented or passed on to someone else
who will then need to worry about them.
    
Stolen references we can stop worrying, unless we want to use them
after its been stolen from us.
    
Borrowed references we can use without worrying provided we dont
allow anything else to come in-between. However if we call some
other logic, which may manipulate them (without informing us), and
inturn if we still want to use them after the other logic returns,
then we need to lock those borrowed references for our purpose by
incrementing their ref count before calling the other logic, and
inturn decref them after we are done with them later.

