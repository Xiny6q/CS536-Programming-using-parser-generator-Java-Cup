Download link :https://programming.engineering/product/cs536-programming-using-parser-generator-java-cup/

# CS536-Programming-using-parser-generator-Java-Cup
CS536 Programming using parser-generator Java Cup
For this assignment you will use the parser-generator Java Cup to write a parser for the Wumbo language. The parser will find syntax errors and, for syntactically correct programs, it will build an abstract-syntax tree (AST) representation of the program. You will also write methods to unparse the AST built by your parser and an input file to test your parser. A main program, P3.java, that calls the parser and then the unparser is provided for you to use. You will be graded on the correctness of your parser and your unparse methods and on how thoroughly your input file tests the parser. In particular, you should write an input file that causes the action associated with every grammar rule in your Java CUP specification to be executed at least once.

Specifications

Getting started



Operator Precedences and Associativities



Building an AST



Unparsing



Modifying ast.java



Testing



Suggestions for How to Work on This Assignment



Getting Started

Skeleton files on which you should build are in the following zip file:

p3.zip contains all files below.

Wumbo.jlex: A JLex specification for the Wumbo language (a solution to program 2 plus additional REPEAT token). Please use this version instead of your version implemented for P2.



Wumbo.cup: A Java CUP specification for a very small subset of the Wumbo language (you will need to add to this file).



Wumbo.grammar: A CFG for the Wumbo language. Use this to guide the enhancements you make to



Wumbo.cup.

ast.java: Contains class definitions for the AST structure that the parser will build (you will need to add unparsing code to this file, but you should not add any new classes, fields, or methods).



P3.java: The main program that calls the parser, then, for a successful parse, calls the unparser (no changes needed).



Use make test to run P3 using test.wumbo as the input, and sending the unparsed output to file test.out. Alternatively run it as follows:

java P3 test.wumbo test.out

Makefile: A Makefile for program 3 (no changes needed).



test.wumbo: Input for the current version of the parser (you will need to change this file).



6:56

ErrMsg.java: Same as for program 2 (no changes needed).



Here is a link to the Java CUP reference manual. There is also a link in the “Tools” section of the “Resources” menu on the course website.

Operator Precedences and Associativities

The Wumbo grammar in the file Wumbo.grammar is ambiguous; it does not uniquely define the precedences and associativities of the arithmetic, relational, equality, and logical operators. You will need to add appropriate precedence and associativity declarations to your Java CUP specification.

Assignment is right associative.



The dot operator is left associative.



The relational and equality operators (<, >, <=, >=, ==, and !=) are non-associative (i.e., expressions like a < b < c are not allowed and should cause a syntax error).



All of the other binary operators are left associative.



The unary minus and not (!) operators have the highest precedence, then multiplication and division, then addition and subtraction, then the relational and equality operators, then the logical and operator (&&), then the logical or operator (||), and finally the assignment operator (=).



Note that the same token (MINUS) is used for both the unary and binary minus operator, and that they have different precedences; however, the Wumbo grammar has been written so that the unary minus operator has the correct (highest) precedence; therefore, you can declare MINUS to have the precedence appropriate for the binary minus operator.

Java Cup will print a message telling you how many conflicts it found in your grammar. If the number is not zero, it means that your grammar is still ambiguous and the parser is unlikely to work correctly. Do not ignore this! Go back and fix your specification so that your grammar is not ambiguous.

Building an Abstract-Syntax Tree

To make your parser build an abstract-syntax tree, you must add new productions, declarations, and actions to Wumbo.cup. You will need to decide, for each nonterminal that you add, what type its associated value should have. Then you must add the appropriate nonterminal declaration to the specification. For most nonterminals, the value will either be some kind of tree node (a subclass of ASTnode) or a LinkedList of some kind of node (use the information in ast.java to guide your decision). Note that you cannot use parameterized types for the types of nonterminals; so if the translation of a nonterminal is a LinkedList of some kind of node, you will have to declare its type as just plain LinkedList.

You must also add actions to each new grammar production that you add to Wumbo.cup . Make sure that each action ends by assigning an appropriate value to RESULT. Note that the parser will return a Symbol whose value field contains the value assigned to RESULT in the production for the root nonterminal (nonterminal program).

Unparsing

To test your parser, you must write the unparse methods for the subclasses of ASTnode (in the file

6:56

ast.java). When the unparse method of the root node of the program’s abstract-syntax tree is called, it should print a nicely formatted version of the program (this is called unparsing the abstract-syntax tree). The output produced by calling unparse should be the same as the input to the parser except that:

There will be no comments in the output.

The output will be “pretty printed” (newlines and indentation will be used to make the program readable); and

Expressions will be fully parenthesized to reflect the order of evaluation.

For example, if the input program includes:

if (b == -1) { x = 4+3*5-y; while (c) { y = y*2+x; } } else { x = 0; }

the output of unparse should be something like the following:

if ((b == (-1))) {

x = ((4 + (3 * 5)) – y);

while (c) {

y = ((y * 2) + x);

}

}

else {

x = 0;

}

To make grading easier, put open curly braces on the same line as the preceding code and put closing curly braces on a line with no other code (as in the example above). Put the first statement in the body of an if or while on the line following the open curly brace. Whitespace within a line is up to you (as long as it looks reasonable).

Note: Trying to unparse a tree will help you determine whether you have built the tree correctly in the first place. Besides looking at the output of your unparser, you should try using it as the input to your parser; if it doesn’t parse, you’ve made a mistake either in how you built your abstract-syntax tree or in how you’ve written your unparser.

It is a good idea to work incrementally (see Suggestions for How to Work on This Assignment below for more detailed suggestions):

Add a few grammar productions to Wumbo.cup.



Write the corresponding unparse operations.



Write a test program that uses the new language constructs.



Create a parser (using make) and run it on your test program.



Modifying ast.java (IMPORTANT)

We will test your program by using our unparse methods on your abstract-syntax trees and by using your unparse methods on our abstract-syntax trees. To make this work, you will need to:

Modify ast.java only by filling in the bodies of the unparse methods (and you must fill in all of the method bodies).

Make sure that no field is null (i.e., when you call the constructor of a class with a LinkedList

argument, that argument should never be null). The only two exceptions to these are

ReturnStmtNode and CallExpNode; it is OK to make the ExpNode field of a ReturnStmtNode null

6:56

(when no value is returned), likewise for the ExpListNode field of a CallExpNode (when the call has no arguments). Thus, you shouldn’t be checking to see if a field is null or not, other than in these two nodes.

Follow the convention that the mySize field of a VarDeclNode has the value VarDeclNode.NOT_STRUCT if the type of the declared variable is a non-struct type.

Testing

Part of your task will be to write an input file called test.wumbo that thoroughly tests your parser and your unparser. You should be sure to include code that corresponds to every grammar rule in the file

Wumbo.grammar.

Note that since you are to provide only one input file, test.wumbo should contain no syntax errors (you should also test your parser on some bad inputs, but don’t hand those in).

You will probably find it helpful to use comments in test.wumbo to explain what aspects of the parser are being tested, but your testing grade will depend only on how thoroughly the file tests the parser.

Suggestions for How to Work on This Assignment

This assignment involves three main tasks:

Writing the parser specification (Wumbo.cup).

Writing the unparse methods for the AST nodes (in ast.java).

Writing an input file (test.wumbo) to test your implementation.

If you work with a partner, it is a good idea to share responsibility for all tasks to ensure that both partners understand all aspects of the assignment.

We suggest that you proceed as follows, testing your parser after each change (if you are working alone, we still suggest that you follow the basic steps outlined below, just do them all yourself):

Working together, start by making a very small change to Wumbo.cup. For example, add the rules and actions for:



type ::= BOOL

type ::= VOID

Also update the appropriate unparse method in ast.java. Make sure that you can create and run the parser after making this small change. (To create the parser, just type make in the directory where you are working.)

Next, add the rules needed to allow struct declarations.



Next, add the rules needed to allow programs to include functions with no formal parameters and with empty statement lists only, and update the corresponding unparse methods.



Still working together, add the rules (and unparse methods) for the simplest kind of expressions — just plain identifiers.



Now divide up the statement nonterminals into two parts, one part for each person.



6:56

Each person should extend their own copy of Wumbo.cup by adding rules for their half of the statements, and should extend their own copy of ast.java to define the unparse methods needed for those statements.



Write test inputs for your statements and your partner’s statements.



After each person makes sure that their parser and unparser work on their own statements, combine the two by cutting and pasting one person’s grammar rules into the other person’s Wumbo.cup (and similarly for ast.java).



Now divide up the expression nonterminals into two parts and implement those using a similar approach. Note that you will also need to give the operators the right precedences and associativities during this step (see above).



Divide up any remaining productions that need to be added, and add them.



Talk about what needs to be tested and decide together what your final version of test.wumbo should include.



When working on your own, do not try to implement all of your nonterminals at once. Instead, add one new rule at a time to the Java CUP specification, make the corresponding changes to the unparse methods in ast.java, and test your work by augmenting your test.wumbo or by writing a Wumbo program that includes the new construct you added, and make sure that it is parsed and unparsed correctly.



If you worked alone on the previous program and are now working with a partner, see programming assignment 2 for more suggestions on how to work in pairs.

Handing in

Please read the following handing in instructions carefully. You will need to submit all the files in the entire working folder as a compressed zip file as given below.

lastname.firstname.lastname.firstname.P3.zip

+—+ deps/

+—+ ast.java

+—+ Wumbo.cup

+—+ Wumbo.grammar

+—+ Wumbo.jlex

+—+ ErrMsg.java

+—+ Makefile

+—+ P3.java

+—+ test.wumbo

+—+ lastname.firstname.lastname.firstname.P3.pdf

Please ensure that you do not include any extra sub-directories.

If you are working in a pair, have only one member submit the program. Include both persons’ name as given above. Also, mention the teammate’s name as a comment while submitting the assignment on canvas.


6:56


Joining a Canvas group with your partner (also required for those who work alone)

To faciliate grading, before you submit the compressed file, please join a Canvas group with your partner (guide) so you can both receive grades for the same submission. Since you’re working on P3, please join an empty group whose name is in the form of “P3-Pair #” (type “P3” in the search bar). Note that we have created the groups for you so you only need to join an empty one with your partner. If you work alone, please join an empty group as well.

Grading criteria

General information on program grading criteria can be found on the Assignments page.

For more advice on Java programming style, see Code Conventions for the Java Programming Language.

See also the style and commenting standards used in CS 302 and CS 367.

http://pages.cs.wisc.edu/~loris/cs536/asn/p3/p3.html Page 6 of 6
