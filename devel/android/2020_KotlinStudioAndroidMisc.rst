Android Studio
===============

File->Project-Structure provides the configuration related to project.

Analyze provides options like Check with Lint, etc

Help is still centered around only the tool and not the language and sdk
one is developing with.

    Also for some strange reason Google/Android has stopped providing
    offline documentation beyond SDK API24. In latest Android Studio/
    SDK Manager, even doc-24 is not available for download.

    For now one can get it using

    https://dl-ssl.google.com/android/repository/docs-24_r01.zip



Kotlin
=======

Compared to Google not having any control or co-ordination with Oracle over
the Java wreck that Oracle Java has become after Sun Java. It is better in
that sense.

Also they try to avoid boiler-plate where it is not needed. But they take it
beyond sane limits for my taste.

    One wants to avoid having to create the boiler plate around anonymous
    class instance and its overridden function for event call backs, it is
    fine to some extent.

    But putting the lambda interface->fun outside the parathesis for function
    arguments of the function which requires it, is taking it too far.

        Esp when the function which takes this interface->fun as argument
        as other arguments, which will as rightly should, sit within the
        func arg parathesis. So one has all but last arg in the parenthesis
        while the last lamda fun or interface->fun sits outside.

    Using with, let, apply, also for always fine cases with only 2 or 3
    calls into the corresponding object makes it look ugly, where as the
    traditional simple code flow where one specifies the object with the
    call looks neat and makes the flow stupidly clear and evident without
    needing to think.


Android
========

RecyclerView
--------------

NOTE: Based on a very quick glance through some parts of RecyclerView documentation.

RecyclerView and its Layout manager should at all times be knowing where a given
item at a given position in the backend data list resides in the display list (ie
its position, which could even be -1 or null or ... as it may not be currently
displayed or in queue for display). But it doesnt seem to provide a mapping between
these two positions currently, instead there is the findViewByPosition or so,
which seems to indicate internally it will walk through each item in display+cache
list. Seems bit odd.

Also for interacting with RecyclerView, the document seems to be proposing a complex
mechanism. Rather I feel the below flow provides a simpler logic for the simple cases
atleast.

For trapping mouse/touch events best to setup a onClickListner for the ViewHolder's
view. And looking at the data embedded in the view holder when the event is triggered
one can infer what it corresponds to in the backend datalist.

For trapping key events best to setup a onKeyListner for the RecyclerView itself.
And inturn maintain a position variable, which you manage to track the the currently
in-focus item of the backend datalist. Inturn ask RecyclerView to bring that element
position into view/focus by using the scrollToPosition call, as well as manage its
highlighting when it is in-focus. This inturn needs to use the findViewByPosition
to find the view associated with that position in the dislayed list.

    However there seems to be a race sometimes, when the display needs to scroll.
    THis needs to be looked at still.

    After having thinking through once again, the idea that was there in background
    wrt possible race, as thought, scrollToPosition wasnt doing anything (ie it was
    not even preparing the updated list of viewholders), instead it potentially is
    pushing a message into the gui event-loop. So if we create a runnable which will
    fetch the required views using findViewByPosition and then (de)highlight things
    as required, and inturn post this also into the gui event-loop toBe called later
    then everything falls in place properly and one can cycle through the items of
    the recycler view using keyboard / dpad and interact with it.


Java Classes
--------------

Path,Files
~~~~~~~~~~~~

Dont use these currently if you want backward compatibility with older than API26.
Better to stick with the old and ever available File class, for now.


Storage
---------

Not sure why Google doesnt expose the path associated with a given storage volume,
got by querying storage manager. Currently one will have to use the hidden getPath++.

For now simpler to use getExternalFilesDirs and then clip out the android app specific
part of the path to get the base paths wrt the storage volumes.

Hope Google keeps the new MANAGE_EXTERNAL_STORAGE based access simple and straight
forward, after showing the good and valid huge alert to user about what permission
they are giving to the app.



