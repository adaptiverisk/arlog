---
layout: post
title: "Time Stamp Your Library"
date: "2016-08-20 00:54"
categories: "xll c++"
published: true
---

When you distribute your program, library, or xll addin it is usefull to
have a function that can tell you when your project was compiled. This way you
can ensure that the new program, library, or xll addin is correctly installed on
a user's system. In this post I will describe how to set up such function in
your Visual Studio C++ project.

* Create `timestamp.h` with the following content:
{% highlight c++ linenos=table %}
#include <string>

std::string TestTimeStamp();
{% endhighlight %}

* Create `timestamp.cpp` with the following content:
{% highlight c++ linenos=table %}
#include "timestamp.h"

std::string TestTimeStamp()
{
  std::string date(__DATE__);
  std::string time(__TIME__);
  std::string res;
  res = date + " | " + time;
  return res;
}
{% endhighlight %}

* Add custom build step to make sure that `timestamp.cpp` is compiled every
time.

  - Right click on the project in the `Solution Explorer` and select `Properties`
  - Make sure that `Configuration` is set to `All Configurations`
  - Go to `Configuration Properties` > `Custom Build Step` > `General` and
    fill out options as follows:
    
      - Command Line: `del $(ProjectDir)$(Configuration)\timestamp.obj`
      - Description: `Refresh timestamp`
      - Outputs: `fake.cpp`
      - Execute Before: `PreBuildEvent`
      
  ![options](/images/timestamp_opts.png)
  
* Now every time you compile the project the timestamp string returned by
`TestTimeStamp()` function is updated.

  
  
