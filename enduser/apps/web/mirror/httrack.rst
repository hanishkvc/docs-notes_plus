==================================
httrack and developer.android.com
==================================
version: v20200728IST2134
author: HanishKVC


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

