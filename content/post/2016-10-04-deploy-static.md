---
tags: ["c++", "xll"]
categories: ["xll"]
date: 2016-10-04T00:00:00Z
published: true
title: Deployment Blues
url: /xll/c++/2016/10/04/deploy-static.html
---

# Introduction

It happens often: you compile your xll addin and give it to yor client just to 
hear them complaining that they cannot load the xll into Excel. The error that
they see is: *"The file you are trying to open, 'MyAddin.xll', is in a different 
format than specified by the file extension. Verify that the file is not 
corrupted and is in from a trusted source before opening the file. Do you want 
to open the file now?"*

There are two reasons for this error:

* The client uses 32bit Excel while the addin is a 64bit xll or vise versa. The
fix for this case is obvious - make sure that you send matching xll to the
client.

* Your addin depends on some dll's that are not available on the client's
computer. To fix this problem you need to add all necessary dll's into
the same folder as the addin. Although there are other locations where you can 
put dll's to fix th problem, from our experience puting them into the same 
folder as xll is the most robust and straightforward way.

How do you know which dll's are missing? You need to open xll in the 
[Dependency Walker](http://www.dependencywalker.com/) on your client's computer. 
The Dependency Walker will show which dll's could not be located.

# C++ Runtime Library

One of the more common cases is missing C++ runtime library. When you
set up an addin project in Visual Studio it by default sets option for created
code to use runtime dlls. The compiled addin xll will run on development
computer without any issues, since runtime dll's are properly installed. But
these dll's are often not installed on client computers and Excel will produce
error while loading the addin.

You can resolve this issue using one of the three methods:

1. Use the above approach by adding runtime dll's to the same folder where xll
is located on the client's computer.

2. Installing runtime dll's using installer provided by Microsoft.

3. Build you xll by using static linking to runtime library.

The third method is our prefered way to solve this issue. We'll discuss how it
works in the next section and then we'll discuss what are its benefits and
downsides.

# Static Linking

To ensure that your xll is statically linked to runtime library you need to
set Runtime Library option to `Multi-threaded (/MT)` (by default it is set to
`Multi-threaded DLL` \\
![Runtime Library Option](/images/runtime.png)

Benefit - you can just deploy single xll file and not worry if the client
has appropriate runtime dll installed.

Downside - any library that your project links to should be compiled with same 
value for Runtime Library option. Sometimes this is outside of your control.
