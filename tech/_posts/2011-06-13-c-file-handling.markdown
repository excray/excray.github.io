---
author: gvivekcbe
comments: true
date: 2011-06-13 06:18:16+00:00
layout: post
slug: c-file-handling
title: C++ file handling
wordpress_id: 53
---

Reading whole file.

[sourcecode language="cpp"]

try

{

stringstream script;

ifstream scriptFile( "ex.txt", ios::in );
scriptFile.exceptions(ifstream::failbit | ifstream::badbit); //This has to be if ios_base::failure has to be thrown
script << scriptFile.rdbuf();
if ( script.rdstate != 0)
throw;
}

catch(ios_base::failure fe)

{

}

//converting hex integer to string representation
stringstream convert;
convert << hex << line;
[/sourcecode]
