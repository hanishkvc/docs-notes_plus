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

    Look at zealdocs and or dash (ios) to get more recent sdk/developer
    documentation.

To add a external library for use in a application. Go to

    File->Project Structure->Dependencies->Modules(apps)->DeclaredDeps

    Next click the + symbol and in the search plus add dialog which comes
    remember to type the keyword or name with stars on both sides, if you
    dont get the library you are looking for directly by entering the name
    of the library. Because there may be multiple versions of the same
    library provided by different sites and many of them may be very old
    and or not updated in ages. So to get the actual/valid/supported/latest
    version of the library, you may have to go through all matching libs
    for the generic search with * or both sides of the generic search term
    i.e like say *commons-compress* and then go trough the results carefully
    and then select the right one. Without star on both sides, you may not
    find the latest version in some cases like the commons-compress from
    apache.



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

Initial
~~~~~~~~~

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


NOTE: On Android 10 API29, the recyclerview seems to handle key up/down on its own.
However enter key gets passed to my handler. (Update: Rather as enter is converted
to mouse clicks automatically, that was what was trigggering the required logic
and not the keydown or keylistener handler).

After some more experimenting
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Newer androids make recyclerView items focusable automatically, while older androids
require the developer to set the focusable manually in the layout xml. So best to
explicitly set the recyclerView item layout element to be focusable in the layout
xml file.

NOTE: Currently I have checked that Android API level 25 and below require manual
intervention wrt making it focusable, while API28 and above handle this implicitly.

If items of recyclerView is focusable, then android/recyclerview/ activity/... code
handles key event based navigation of recycler view items automatically. However older
androids dont highlight the item in focus, while newer androids do highlight the item
in focus. SO best to explicitly set a drawable selector for the recyclerView which
specifies how the item background should be handled wrt different states like focused,
activated, pressed, default etc.

THe auto key event handling logic also maps enter/dpad_center press event into normal
or longpress clicks, so handling the mouse/touch events is good enough to allow dpad
(i.e tvremote) based navigation also.

THe below two situations among others will require the user to override the auto
key handling logic, to achieve a custom/better flow

S1) But then as keyboard firmware converts a very long key press into one long
keypress and multiple short keypresses, so one cant handle keyboard based long press
properly by default, unless additional code is implemented to filter such situations.

S2) Similarly the auto key handling requires one to navigate through each and every
focusable element on the screen in sequence. If one wants to use one specific key
like say left key to jump to some special gui widget/element on the top or bottom or
so in a screen gui which is having only a vertical recyclerview list, other than the
special element, it is not possible by default.

    However if that special element only triggers flow similar to what android back
    should ideally trigger, then android back and its logic itself can achieve what
    is required.

Setting a onKeyListener for recyclerView doesnt trap/bypass the underlying auto key
handling that is occuring. Need to check where I require to trap this.

Need to check the documentation to see how android gui events are handled, do the
events get processed from

    top to bottom, such that the top level logic checks if a lower logic implements
    a handler, and if so call it and then if it tells its not fully handled, then do
    its own logic

    AND OR bottom to top, where the bottom/inner most logic does its work and then if
    requried and or available call the higher/topper handler/logic.

Trap auto key handling

    Similar to the mouse events, If one provides OnKeyListener for recyclerView's
    ViewHolder View, then the auto key handling can be trapped. And as usual, if you
    handle the key event fully in this handler, then return true, else return false
    so that additional logic provided by android/widget can do its job.

    However if one wants to handle the key events at a higher level in the GUI heirarchy
    like at the full recyclerView level and not at individual item level, then setting
    onKeyListener at the recyclerView level doesnt do the job, if focus is enabled for
    the items, else it will work partly. By partly what I mean is that if one wants
    focus to flow into the individual recyclerView items, then it wont work, else, i.e
    if one doesnt want to pass focus to individual items, then recyclerView level
    OnKeyListener can handle key events.





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


View
-----

Why not provide a simple getBackgroundColor for views, so that one can try and
stop oneself from messing with any theme and colors to some extent. Good you
provide more powerful/complex mechanisms, but why not also a simple stupid
and straight method which will do for many cases.


Intents
---------

file:/// scheme have been curtailed with FileExposedBeyondApp exception. One will
have to use either

    FileProvider with grant read permission flag for intent.

    Bypass StictMode.VmPolicy with a dummy

    Share using intent with send action and grant read permission flag.


