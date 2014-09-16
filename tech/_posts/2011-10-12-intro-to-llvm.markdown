---
author: gvivekcbe
comments: true
date: 2011-10-12 01:22:56+00:00
layout: post
slug: intro-to-llvm
title: Intro to LLVM
wordpress_id: 178
tags:
- llvm
---

**Writing a pass that counts the number of dynamic instructions in LLVM:**

**Problem Statement:**

To write an analysis pass in LLVM, that counts the number of instructions in the whole program. Note the count has to denote the number of dynamic instructions(that appears in the bitcode) and not the static count(That appears in the source file)

**Solution:**

This requires some modifications to the hello world pass.

The pass is a FunctionPass.

While overriding runOnFunction(Function& F), a ProfileInfo object is required.

In the runOnFunction, create a ProfileInfo object by calling

[sourcecode language="CPP"]

ProfileInfo* PI = &getAnalysis<ProfileInfo>();

[/sourcecode]

If getAnalysis() has to return a ProfileInfo object, it requires a llvmprof.out file which is the file that stores the profiled information to be present in the current directory.

[sourcecode language="CPP"]

// We don't modify the program, so we preserve all analyses

virtual void getAnalysisUsage(AnalysisUsage &AU) const

{

AU.addRequired<ProfileInfo>(); //This step is required.

AU.setPreservesAll();

}

[/sourcecode]

Function::iterator points to a BasicBlock object. BasicBlock::iterator points to a Instruction object.

So iterate through each Instruction of all BasicBlocks.

Instruction::getOpcode() returns the opcode of the instruction.

[sourcecode language="CPP"]

If (instructionObj->getOpcode() == OPCODE_OF_INTEREST)>

{

count = count += PI->getExecutionCount(basicBlockObj); //This gives the dynamic count.



[/sourcecode]

**Execution Steps after creating the pass:**

1 rm llvmprof.out // remove the llvmprof.out file as it appends the profile information

2 rm *.bc

3 rm *.s

4 rm *.profile

5 llvm-gcc -emit-llvm simple.c -c -o simple.bc //simple.bc is the bitcode

6 opt -insert-edge-profiling simple.bc -o simple.profile.bc

7 llc simple.profile.bc -o simple.profile.s

8 g++ -o simple.profile ../../../../Programs/llvm-debug/Debug/lib/profile_rt.so simple.profile.s //maye have to use the cmd line option to disable //optimization

9 ./simple.profile //This generates the llvmprof.out file

10 opt -load ../../../hw1/Debug/lib/Scp.so -profile-loader -profile-info-file=llvmprof.out -BranchPass -Scp simple

**Example Program:**

[sourcecode language="CPP"]

int main()

{

int a = 3;

int b = 5;

int c = b + a;

return 0;

}

[/sourcecode]

Output:

The dynamic count is

FuncName DynOpCount %IALU %FALU %MEM %BRANCH %OTHER

2 main 34628 19.3601 0 60.9796 16.7726 2.88784
