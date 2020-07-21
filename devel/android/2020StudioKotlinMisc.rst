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

