---
author: gvivekcbe
comments: true
date: 2011-06-13 09:28:45+00:00
layout: post
slug: some-cool-code-snippets
title: Some cool code snippets
wordpress_id: 65
---

Code written by others, that i think is elegant.

**Representing a register using struct/union:**

[sourcecode language="cpp"]
<pre>typedef struct
{
	unsigned long res1                 : 16; // [15:0]
	unsigned long res2                 : 16; // [31:16]

	unsigned long MASK ()
	{
		return 0x00000000;
	}
	unsigned long DEFAULT_VALUE ()
	{
		return 0x00000000;
	}
	unsigned long READ_ONLY ()
	{
		return 0x00000000;
	}
	unsigned long BYTE_SIZE ()
	{
		return 4;
	}
} DummyReg;

typedef union
{
	DummyReg sData;
	unsigned long ulData;
}DummyRegister, *pDummyRegister;
[/sourcecode]

**Makefile:To locate other .cpp files:**

vpath %.cpp <path>

**Catcher Pattern:**

The tool that i worked in had to read a file which has some serialized structs identified by an ID. For each ID, a function called a catcher had to be called to handle the struct. For all classes that wanted to register such functions, there was a base class.

**Interpreter pattern:**

One of my team mate wrote a compiler based on interpreter pattern.


