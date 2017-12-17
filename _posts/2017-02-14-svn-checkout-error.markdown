---
title:  "SVN Checkout Error"
date:   2017-12-14 11:30:23
categories: [Development]
tags: [svn]
---
While working on **Ubuntu Mate + Eclipse Mars + Subversive SVN Team Provider**, I need to commit a few modified files using SVN client in Eclipse. I am using **SVN Kit v1.8.12.10533** as SVN client to manage my code in eclipse. Though all functionality except "Commit" is working perfectly, to commit changes, I use SVN command line. (To know more on SVN command line, you can refer my other post on SVN [here](http://jumbo.iamjrp.com/post/148217764265/subversion-on-command-line))

Ok, so error, it throws while committing ...

    svn: E200007: Commit failed (details follow):
    svn: E200007: Commit failed (details follow):
    svn: E200007: CHECKOUT can only be performed on a version resource [at this time].
    svn: E175002: CHECKOUT request failed on 'file-path'
    svn: E200007: Commit failed (details follow):
    **svn: E200007: CHECKOUT can only be performed on a version resource** [at this time].
    svn: E175002: CHECKOUT request failed on 'file-path'

Workaround is :

Perform a cleanup prior to committing or using an external tool (SVN command-line client or TortoiseSVN) instead of Subversive.

Open terminal
go to project directory
Then ...

    svn cleanup .

Now commit the files in eclipse. It will work perfectly.
Enjoy :)
