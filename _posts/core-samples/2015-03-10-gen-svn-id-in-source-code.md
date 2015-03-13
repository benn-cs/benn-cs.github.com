---
layout: post
category : svn c
tagline: 
tags : [svn,c]
---
{% include JB/setup %}

#gen svn id in source code
```c

 #!/bin/sh
export LANG=en_US.utf8

svn_home_str=`svn info | grep "Working Copy Root Path" | awk '{print $5}'`
svn_last_rev_str=`svn info | grep "Last Changed Rev" | awk '{print $4}'`

if [ 't'$svn_home_str'a' == 'ta' ]
then
  svn_home_str="."
fi

cd $svn_home_str
svn up > /dev/null

echo -e "/*	Copyright 2015 MixData Inc.
 *
 *	@author Ben|C6H6
 */

#ifndef __DSP_BUILD_VERSION_INFO_H__
#define __DSP_BUILD_VERSION_INFO_H__


#define DSP_BUILD_VERSION "${svn_last_rev_str}"

#endif
" > $svn_home_str/src/dsp_build_version.h

```
	
