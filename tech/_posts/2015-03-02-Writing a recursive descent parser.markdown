---
author: gvivekcbe
comments: true
date: 2015-03-02 22:40:33+00:00
layout: post
title: Writing a recursive descent parser. 
---

***
# Recursive descent parsing algorithm.

## Backus normal form:

> It is a system  that describes grammar of natural language.
The power of BNF is in its recursive nature. 

### BNF for a while loop: 
	<while-loop> ::= "while" "(" <condition> ")" <statement>


## Recursive descent parser:

> This parser is most suited for written by hand. 

> An example parser for the BNF below:

	<expression>  ::=  <Integer>  |
                   "(" <expression> <operator> <expression> ")"
                   
	<operator>  ::=  "+" | "-" | "*" | "/"

## IO class:

### methods:

	peek, skipBlanks, getChar, getInt()


## Parser Class:

Each rule will have a method. | corresponds to an IF statement.Skip blank lines at beginning of each rule and whenever required. After every peek there would be a getchar

## methods:

	getOperator, expressionVal


## Writing a parser with operator precedence:

> This depends on how the BNF rules are written:

	((3*2) + 2 / 4 )

> The operation happens with /,* and then it adds the two results. 

	((3*2) + 2), 4 are two factors.

> 3*2 is a term which is a combination of two factors. And the final expression is a (+,-) combination of terms.

### In BNF:

	<expression> := [-] <term> | [+|- <term>].. 
	<term> := <factor> | [*|/ <factor>]..
	<factor> := <number> | "(" <expression> ")"

> [].. is repeated zero to many times. so that constitutes a while loop.
