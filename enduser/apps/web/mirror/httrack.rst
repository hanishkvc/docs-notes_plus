=========
httrack
=========
version: v20200728IST2134
author: HanishKVC

Usage
======

The below is based on some experiments and some minimal reading of help pages and forums.
Havent gone thro the docs properly yet.

General usage
---------------

httrack site-and-or-its-sub-space-to-mirror [additional-sites-to-mirror] [filters] [options]

options
~~~~~~~~~

Some of the additional options that could be specified

* -iC1 [--continue]

  continue a interrupted mirror

* -iC2 [--update]

  update a existing mirror

  --updatehack

    Try avoid updating, where possible.

  Updating may not skip existing files, i.e it may not avoid redownloading,
  even the corresponding pages are fully downloaded, if the server doesnt
  send back proper info to the query. This can occur very often especially
  with server side dynamically generated pages.

* %q0

  dont include query string. Need to experiment wrt this and understand its
  implications better, like will it update links with query string in them
  to point to the saved base page in the mirror without the query string,
  related impact/info in them.

    If the query string doesnt change the content in any major way, then
    if it points links with query string in them to the saved base page,
    it will be very useful. However if the query string changes the content
    of the page, then this will be a disaster. So need to check the outcome
    of this option to understand when and where to use it and or not use it.

    example site/some_path?try_cache=false, ignoring query string shouldnt
    affect in any real way.

    example site/some_path?version=xyz, ignoring query string here potentially
    shouldnt affect in any real way, unless one always wants the latest and or
    specific versions of that page/link.

    example site/get_data?key=abc, ignoring query string in this situation
    could lead to links pointing to wrong data. Rather in this case, the
    httrack's default behaviour, which is to account for query strings and
    not ignore them (my current understanding) is the right behaviour.

* -X0 [--purge-old=0]

  dont purge currently non-existant files on server, from the local mirror.
  Note: by default non-existant files are purged from local mirror.

* --near

  download images,things linked from the webpages being mirrored.
  But dont get the linked webpages and beyond.

Filters
~~~~~~~~~

+filter_pattern to get urls/links which match the pattern

  +somesite/*images*

    get contents of paths/files on somesite which contain the literal string
    images in them.

-filter_pattern url/links which match this pattern wont be got

  -somesite*?*

    dont get links with get vars/arguments in them.


Update/Continue
-----------------

If you want to add new urls to a existing mirror and continue, then add them
to the meta data files in the mirror's hts dirs and hts files. And then use
--continue option. If you use --update option then it will try to update and
or in worst case (if server is nutty) redownload the already mirrored urls.

TOCHECK: If --continue will update links in already mirrored pages, if the
new urls provided to continue operation fetch the corresponding pages into
the mirror.

Websites
==========

developer.android.com
-----------------------

* Google Use somthing other than dot '.' as part of file names
  when the '.' doesnt denote a file extension
  i.e say R_drawables instead of R.drawables for url file names

  Otherwise tHis leads to these files being named in a opaque
  manner by default like say R-1.html, R-2.html and so on by
  web mirroring tools.

  By the way Google, if you please stop being unfriendly to people
  with limited net access and start releasing sdk documentation
  offline bundle again with your sdk, like before, then we
  dont even require to get into any of this in the first place.

* Avoid directly embeding devsite-book-nav data in the files.
  As this data is potentially same for a group of files, see
  if you can load it at runtime from a linked file.

  This is adding potentially around 1+MB in a huge majority of
  the pages. This involves thousands of pages, so it is a huge
  overhead.

* In some places there is space in the url path like for
  developer.android.com/ guide/topics/ui/actionbar.html,
  refered from kotlin related pages.

* need to avoid urls with ?skip_cache. Otherwise it leads to
  duplicated files and hierarchies.

* need to avoid urls with #, as these refer to internal bookmarks.
  Otherwise it leads to duplicated files.

After wasting lot of bandwidth and space, found that there is two
projects dash and zeal which provide the dataset in a offline manner
and they seem to have already taken care of nuking devsite-book-nav
and most of the things which I have mentioned above, so they contain
a clean offline representation to a great extent.


