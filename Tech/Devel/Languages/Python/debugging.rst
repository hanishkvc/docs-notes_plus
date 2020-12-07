==================
python3 pdb usage
==================

    ::

    import pdb

    import mymodule

    pdb.runcall(mymodule.func, arg1, ..., argn)

        break <funcname> - add a breakpoint

        clear - clear a breakpoint

        list <linenum> - list the contents of current file being debugged

        next/continue/step - run code

        display <var/entityname> - display the entity



