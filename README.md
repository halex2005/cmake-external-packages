[![Build status](https://ci.appveyor.com/api/projects/status/bjllvvoapoh32vng?svg=true)](https://ci.appveyor.com/project/halex2005/cmake-external-packages)

cmake-external-packages
=======================

cmake-external-packages can help you to start using on Windows some of libraries which are not support cmake configuration.

This repository does not contain any binary files, it contains only cmake plain text configurations for packages.

Why you'll need it?
-------------------

Some libraries have very good support for linux, but bad support on windows.
They can provide Visual Studio (VS) project files, but for only one VS version.
When you use cmake, it is inconvinient to consume this kind of libraries, because of you need to link with right compiler settings (or you'll got `unresolved reference` linker errors).
If you have cmake targets for these libraries, you'll compile and link without errors.

Why not to use cmake's [ExternalProject](https://cmake.org/cmake/help/v3.0/module/ExternalProject.html)?
----------------------------------------

Unfortunately, cmake's ExternalProject_Add command works in build phase, not in configure/generate phase.
How it looks? Suppose you wrote

    ExternalProject_Add(GIT_REPOSITORY git@repository.here)

Then you'll run generate phase by `cmake path/to/source` command.
Nothing will happens here.
Git repository you specify will be cloned only when you run build process for your project.

How to use?
-----------

Use [git-subtree](http://blogs.atlassian.com/2013/05/alternatives-to-git-submodule-git-subtree/), [git-submodule](http://blogs.atlassian.com/2011/12/git-submodules/) or just copy this repository contents to your repository subfolder.

Declare `EXTERNAL_PACKAGES_INCLUDE_DIR` option in your top-level CMakeLists.txt and add include directories:

```cmake
set (EXTERNAL_PACKAGES_INCLUDE_DIR ${CMAKE_CURRENT_LIST_DIR}/third-party/include
    CACHE STRING "Directory for third-party include files, where include folders will be copied")
include_directories(${EXTERNAL_PACKAGES_INCLUDE_DIR})
```

Add packages you want to use with `add_subdiretory`. For example, to use protobuf:

```cmake
add_subdirectory(path/to/cmake-external-packages/protobuf-v2)
```

Thats all. Included package will download source code zip file, extract it and generate cmake targets to build and consume.

Now you can link your code with protobuf like:

```cmake
add_executable(my_exe ${my_exe_sources})
target_link_libraries(my_exe libprotobuf)
```

Which packages are supported?
-----------------------------

- [cppunit](cppunit/CMakeLists.txt) [1.12.1]
- [log4cpp](log4cpp/CMakeLists.txt) [1.1.1]
- [protobuf](protobuf-v2/CMakeLists.txt) [2.4.1, 2.5.0, 2.6.0, 2.6.1]
- [tinyxml](tinyxml/CMakeLists.txt) [any, tested with 2.6.2]

Contributions are welcome!

Licensing
---------

cmake-external-packages library is distributed under [MIT license](license.md)

Copyright (C) 2016 halex2005 
Report bugs and download new versions at https://github.com/halex2005/cmake-external-packages/issues

[![PayPal donate button](http://img.shields.io/paypal/donate.png?color=yellow)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=7RR8B7SRHFX5Q "Donate once-off to this project using Paypal")
[![Gratipay donate button](http://img.shields.io/gratipay/halex2005.svg)](https://gratipay.com/halex2005/ "Donate weekly to this project using Gratipay")