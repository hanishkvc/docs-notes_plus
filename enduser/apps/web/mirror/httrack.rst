=========
httrack
=========
version: v20200728IST2134
author: HanishKVC

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


