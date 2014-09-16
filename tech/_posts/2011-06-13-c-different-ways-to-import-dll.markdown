---
author: gvivekcbe
comments: true
date: 2011-06-13 06:24:12+00:00
layout: post
slug: c-different-ways-to-import-dll
title: 'C++: DLL'
wordpress_id: 59
---

Some notes that i taken regarding DLL. Iam writing them here without formatting them.



DLL Export -> Will create App.lib which is linked to the app that uses the DLLs

When you include the DLL header file, the app can statically link with the dll.lib file by using #pragma comment(lib, "Test.lib")

Delay loading still requires .lib file.

**Three options for including DLL in app:**



	
  1. Implicit linking - Requires .lib file to link. Requires .dll file at startup.

	
  2. Delay Loading - Needs tje .lib file but doesn't require dll file at startup.

	
  3. Explicit Linking - Doesnt need .lib file but needs extra code. In DLL, All functions are declared in DLL by this macro -> __declspec(dllexport). In the app, those functions are declared by __declspec(dllimport)



