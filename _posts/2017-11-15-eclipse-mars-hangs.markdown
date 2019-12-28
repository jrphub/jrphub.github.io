---
title:  "21 - Eclipse Mars.2 Release (4.5.2) hangs in Ubuntu 16.04 LTS"
date:   2017-12-15 11:30:23
categories: [Development]
tags: [eclipse]
---
While creating a project, eclipse hangs. But the project directory get created. After killing the eclipse and restart the eclipse, the prject is seen in workspace, but any operation on eclipse hangs. 

This is known issue explained 

[here]: https://bugs.eclipse.org/bugs/show_bug.cgi?id=495946	"bug-eclipse"

Solution:

1. Install Eclipse Neon (4.6) 

   or

2. Install Eclipse Luna (4.4.2)

   or

3. Follow below steps

   1. export SWT_GTK3=0

   2. open eclipse.ini file and before -vmargs, remove the existing launcher and add below 2 lines

      ​	`--launcher.GTK_version`

      ​	`2`

   3. start eclipse
