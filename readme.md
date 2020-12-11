# TF: Textfile & String Library for AutoHotkey [lib] - v3.8

__A "Swiss Army Knife" library for Text (files)__

## Introduction

As the name suggest this is an AutoHotkey (AHK) Library with a number of functions to "manipulate" **text**,
both **files** such as \*.txt, \*.ahk, \*.html, \*.css etc AND **Strings** (or variables). For example
you can delete specific lines, replace words or specific lines, number lines, remove or insert columns
of text, etc. See the list of functions below for a complete overview.

It is **not** useful for binary files or data such as MS Office files, PDFs, EXEcutables, images etc.
(Tip: search the AHK forum for "binread" to find some pointers on how to read, write and "edit" binary files.)

## Functions

1.  [TF](#TF)
2.  [TF_Save](#TF_Save)
3.  [TF_CountLines](#TF_CountLines)
4.  [TF_Count](#TF_Count) *1
5.  [TF_ReadLines](#TF_ReadLines)
6.  [TF_Tail](#TF_Tail)
7.  [TF_Replace](#TF_Replace)
8.  [TF_ReplaceInLines](#TF_ReplaceInLines)
9.  [TF_RegExReplace](#TF_RegExReplace)
10. [TF_RegExReplaceInLines](#TF_RegExReplaceInLines)
11. [TF_RemoveLines](#TF_RemoveLines)
12. [TF_RemoveBlankLines](#TF_RemoveBlankLines)
13. [TF_RemoveDuplicateLines](#TF_RemoveDuplicateLines)
14. [TF_InsertLine](#TF_InsertLine)
15. [TF_ReplaceLine](#TF_ReplaceLine)
16. [TF_InsertPrefix](#TF_InsertPrefix)
17. [TF_InsertSuffix](#TF_InsertSuffix)
18. [TF_TrimLeft](#TF_TrimLeft)
19. [TF_TrimRight](#TF_TrimRight)
20. [TF_AlignLeft](#TF_AlignLeft)
21. [TF_AlignCenter](#TF_AlignCenter)
22. [TF_AlignRight](#TF_AlignRight)
23. [TF_LineNumber](#TF_LineNumber)
24. [TF_ConCat](#TF_ConCat)
25. [TF_ColGet](#TF_ColGet)
26. [TF_ColPut](#TF_ColPut)
27. [TF_ColCut](#TF_ColCut)
28. [TF_ReverseLines](#TF_ReverseLines)
29. [TF_Find](#TF_Find) (can be used as basic grep)
30. [TF_SplitFileByLines](#TF_SplitFileByLines)
31. [TF_SplitFileByText](#TF_SplitFileByText)
32. [TF_Merge](#TF_Merge) *2
33. [TF_Prepend](#TF_Prepend) *2
34. [TF_Append](#TF_Append) *2
35. [TF_Wrap](#TF_Wrap)
36. [TF_WhiteSpace](#TF_WhiteSpace)
37. [TF_Substract](#TF_Substract)
38. [TF_RangeReplace](#TF_RangeReplace)
39. [TF_MakeFile](#TF_MakeFile)
40. [TF_Tab2Spaces](#TF_Tab2Spaces)
41. [TF_Spaces2Tab](#TF_Spaces2Tab)
42. [TF_Sort](#TF_Sort)
43. [TF_Join](#TF_Join)

**Notes**

1. TF_Count does not support files and only works with variables.
2. TF_Merge, TF_Prepend, TF_Append currently do not support variables and only work with FILES.
 
**Concept**

It is important to understand a few basic concepts before you start working with the TF library:

- If you pass on a file to a TF function the output will generally be a file (there are a few exceptions). By default it writes the output to a COPY of the input file, leaving the original input file intact.
- You **can** overwrite the input file by using a ! as prefix. See "Quick intro to Parameters" and "Textfile and the ! Prefix" below for more details.
- If you pass on a variable to a TF function the returned output will also be a variable.
- Most TF Functions allow you to work on specific lines or sections of lines.
- TF "knows" if something is a file or a variable, even if the variable passed on to TF is meant to represent a file. (This is done in the helper function TF_GetData for those interested).

To understand how to work with **files and variables** please read the "Textfile and the ! Prefix"
and "Files & Variables" sections below.

**File encoding, codepage**

If you experience TF is changing the file encoding (codepage) try to set the correct file encoding
at the top of your script (or at least before you call a TF function) using the following AutoHotkey command:
http://ahkscript.org/docs/commands/FileEncoding.htm

Please note, there is no way to determine the file encoding 100% accurately, even if a file
contains BOM. See the discussion about this topic and possibly useful additional functions
at the AutoHotkey forum http://www.autohotkey.com/board/topic/95986-filegetencoding-filegetformat/
(The result of these functions are simply a best guess assuming UTF-8 more common when BOM - https://en.wikipedia.org/wiki/Byte_Order_Mark - 
is missing).

## How to "install" / use

You can either place TF.ahk in your LIB directory (see [http://ahkscript.org/docs/Functions.htm#lib](http://ahkscript.org/docs/Functions.htm#lib))
or use #include (see [http://ahkscript.org/docs/commands/_Include.htm](http://ahkscript.org/docs/commands/_Include.htm))

You can find examples of most functions in the "example script" here
[http://www.autohotkey.com/forum/viewtopic.php?p=280363#280363](http://www.autohotkey.com/forum/viewtopic.php?p=280363#280363)

**Note:** Because most of these functions operate on a line by line basis they WILL be
slower compared to a function or script which could operate on an entire file or
variable at once. Keep this in mind if you need to process many files/variables or very
large files/variables in case speed is an issue.

## Quick intro to Parameters

Almost all functions accept the following basic parameters:

<table border="1" width="100%" cellspacing="0" cellpadding="3" bordercolor="#C0C0C0">
      <tr valign="top">
		<td><b>Parameter</b></td>
		<td><b>Meaning</b></td>
	  </tr>	
	  <tr valign="top">
	    <td>Text</td>
	    <td>The Filename (may include (absolute) path) to read from and save to (for all functions that write an output file).<br />
         <b>Note:</b> by default a filename_copy will be created, use the ! prefix if you want to OverWrite the TextFile (e.g. the source file)<br />
	     As of v3 "Text" can also be a variable or indeed text directly passed on to the function.<br />
         See <a href='#textfile-and-the--prefix'>Textfile and the ! Prefix</a>.
	    </td>
	  </tr>
      <tr valign="top">
        <td>! Prefix</td>
        <td>If Text starts with ! (eg: "!c:\sample.txt") it will overwrite the text file, otherwise it will save the new file to a copy of the text file eg: Filename_copy.txt (All functions, apart from reading functions because there is no output file) <br />
        <b>Tip:</b> you can use concatenation to add the !, e.g. "!" . "Filename.txt", see the examples in the AHK thread.<br />
        Note: If Text is a variable, it can start with a ! as TF will detect automatically that it is not a file and will therefore not create a file but return the altered variable instead.<br />
        See <a href='#textfile-and-the--prefix'>Textfile and the ! Prefix</a>.
	    </td>
	  </tr>
      <tr valign="top">
        <td>Lines</td>
        <td>Number of lines to read</td>
      </tr>
      <tr valign="top">
        <td>StartLine</td>
        <td>Start of Range (Almost all).<br />
	    See <a href='#startline-endline-syntax'>(StartLine, Endline) Syntax</a>.
	    </td>
	  </tr>
      <tr valign="top">
        <td>EndLine</td>
        <td>End of Range (Almost all).<br />
  	    See <a href='#startline-endline-syntax'>(StartLine, Endline) Syntax</a>.
	    </td>
	  </tr>
      <tr valign="top">
        <td>SearchText</td>
        <td>Text to Find (Find / Replace functions)</td>
      </tr>
      <tr valign="top">
        <td>ReplaceText</td>
        <td>Text to Replace (Find / Replace functions)</td>
      </tr>
</table>

**Notes**

1. Many functions also have specific parameters, see the description of each function below.
2. **Backup files:** If a subdirectory "backup" is present in the directory of TextFile 
a backup is made before overwriting the original file (both for file.txt and file_copy.txt)
with the BAK extension
3. You can find examples of most functions in the "example script" here [http://www.autohotkey.com/forum/viewtopic.php?p=280363#280363](http://www.autohotkey.com/forum/viewtopic.php?p=280363#280363)

### (StartLine, Endline) Syntax

You can pass on multiple lines (sections) by using the StartLine parameter.

Examples:

- (5) ; start from line 5 to the end (StartLine)
- (5, 15) ; Section: lines 5 to 15 (StartLine, EndLine)
- ("5,13,45,67", 135) ; Specific lines: 5,13,45,67, ignore 135 ("StartLine", Endline - **EndLine value is ignored**)
- ("5-13,45-51", 135) ; Multiple sections of lines: 5 to 13, 45 to 51 ignore 135 ("StartLine", Endline - **EndLine value is ignored**)

Incremental (EndLine will **not** be ignored) in the following cases:

- ("2+3", 150) ; start with line 2, increment 3 up-to line 150 so 2,5,8,11,14 etc
- ("5+15") ; start with line 5, increment 15 until end of file so 5,20,35 etc

Negative Startline

- A Negative startline value makes a TF function operate on the last X lines.
- TF_ColGet and TF_ColPut accept negative StartColumn parameter. This can be used to "Get" or "Put" text in the x-th "column" from the right

### Textfile and the ! Prefix

Valid examples for using a Textfile:

```autohotkey
TF_("file.txt", .... ; Process file.txt and write output to file_copy.txt

F=file.txt ; ; Note how F is a variable, but AHK/TF will see it is meant to represent file.txt and write output to file_copy.txt
TF_(F, .... ; Process file.txt and write output to file_copy.txt
TF_("!file.txt", .... ; Process file.txt and overwrite file.txt

F=file.txt
TF_("!" . F, .... ; Process file.txt and overwrite file.txt

F=!file.txt
TF_(F, .... ; Process file.txt and overwrite file.txt

In a Loop, FilePattern:
TF_(A_LoopFileName, .... ; Process file and write output to file_copy
TF_("!" . A_LoopFileName, .... ; Process file and overwrite file

Loop, *.txt
    {
     TF_(A_LoopFileName, .... ; Process file and write output to file_copy
    }
```

If you want to use multiple TF functions on a single file it is advised to use the ! Prefix

```autohotkey
F=!file.txt 
TF_RemoveBlankLines(F)        ; remove all empty lines from file.txt and overwrite original file.txt
TF_LineNumber(F)              ; add linenumbers to all lines and overwrite original file.txt
TF_Replace(F, "this", "that") ; Replace the word "this" with "that" and overwrite original file.txt
; So the original file.txt has undergone three changes
```

**Files & Variables (v3+)**

You can use TF functions directly on files as well as variables. 
Below you will find some easy examples.

**Introduced in v3: TF()**

In order to save you the trouble of using a fileread to read a file into
a variable and proceed to use various TF functions there is the TF()
Function.


```autohotkey
TF("pathtofile", CreateGlobalVar="T")
```

By default it reads the contents of the file in a global variable named **t** and 
returns it for further processing. By default it creates variable **t** if a variable
name is **omitted** so if you do not like to use **t** you can use anything you like.
It is NOT mandatory to use TF(), you are welcome to use a fileread as well, TF() is 
merely added for convenience as we will see in the examples below. TF() doesn't suddenly
make the TF library "not standard compliant" as some seem to think as it only creates a 
global variable if you use the TF() function.

**Repeat:** You only have to use TF() if you want to read a file into a variable 
and use it on multiple TF_\* Functions but it is NOT required.

See also [TF Lib Errors](#tf-lib-errors) below.

**TF() Examples:**


```autohotkey
TF("file.txt") ; will create global var t
TF("file.txt", "MyVar") ; will create global var MyVar
```
   
**Examples on how to use files & variables with TF:**


```autohotkey
#Include tf.ahk
TestFile= ; create variable
(join`r`n
1 Hi this
2 a test variable
3 to demonstrate
4 how to 
5 use this
6 new version
7 of TF.AHK
)
FileDelete, TestFile.txt
FileAppend, %TestFile%, TestFile.txt ; create file
F=TestFile.txt ; just a shorthand code for TextFile.txt, so when
; we are using 'F' below we are still passing on a TextFile, not a variable!

; pass on file, read lines 5 to end of file:
MsgBox % "From File 1:`n" TF_ReadLines("TestFile.txt",5)
MsgBox % "From File 2:`n" TF_ReadLines(F,5)              ; same

; pass on variable, read lines 1 to 5
MsgBox % "From Variable 1:`n" TF_ReadLines(TestFile,"1-5")  
MsgBox % "From Variable 2:`n" TF_ReadLines("Hi`nthis`nis`na`ntest`nvariable`nfor`ntesting",1,3) ; pass on string

; Examples using TF(): (it will save you a FileRead if you want to work with Variables)

TF("TestFile.txt") ; read file, create global var t
t:=TF_ReadLines(t,5) ; pass on global var t created by TF(), read lines 5 to end of file, assign result to t 
MsgBox % "TF(), example 1:`n" t

TF("TestFile.txt", "MyVar") ; read file, create global var MyVar
MyVar:=TF_ReadLines(MyVar,5) ; pass on global var MyVar created by TF(), read lines 5 to end of file, assign result to MyVar
MsgBox % "TF(), example 2:`n" MyVar

; Note how we can use TF() here
t:=TF_ReadLines(TF("TestFile.txt"),5) ; pass on global var t created by TF(), read lines 5 to end of file, assign result to t 
MsgBox % "TF(), example 3:`n" t

MyVar:=TF_ReadLines(TF("TestFile.txt","MyVar"),5) ; pass on global var t created by TF(), read lines 5 to end of file, assign result to t 
MsgBox % "TF(), example 4:`n" MyVar

t:=TF_ReadLines(TF(F),5) ; pass on global var t created by TF(), read lines 5 to end of file, assign result to t
t:=TF_ReverseLines(t,5) ; pass on global var t created by TF(), reverse lines, assign result to t
MsgBox % "TF(), example 5:`n" t

; Work directly with the clipboard or another other variable
Clipboard=Line 1`nLine 2`nLine 3`nLine 4`nLine 5`nLine 6`nLine 7`nLine 8
Clipboard:=TF_RemoveLines(Clipboard, 3, 6) ; remove lines 3 to 6
MsgBox % "Clipboard, example 6:`n" Clipboard
```
    
**Note** TF_Merge, TF_Prepend, TF_Append currently do not support variables and only work with FILES.

**Common mistake(s):**


```autohotkey
MyVar:=TF_ReadLines("TestFile.txt",5) ; this is wrong. It is AN INCORRECT EXAMPLE!
```

The example above is incorrect: You pass on a file so the output is
testfile_copy.txt. In this case nothing meaningful is assigned to the
variable MyVar. Correct would be:


```autohotkey
MyVar:=TF_ReadLines(MyVar,5) ; this is OK
MyVar:=TF_ReadLines(TF("TestFile.txt","MyVar"),5) ; this is OK
```

--------------------------------------------------------------------------

# Functions and Examples

**Note:** Some of these functions are very similar, but they are included in the library for ease of use ...

You can find examples of most functions in the "example script" here [http://www.autohotkey.com/forum/viewtopic.php?p=280363#280363](http://www.autohotkey.com/forum/viewtopic.php?p=280363#280363)

<a name="TF"></a>

**TF(TextFile, CreateGlobalVar="T")**

- Purpose: Read contents of file to create global variable, **T** by default.
- Parameters: TextFile, CreateGlobalVar (=preferred name of global variable)
- Credits: See TF thread & discussion http://www.autohotkey.com/forum/viewtopic.php?p=313120#313120

Note: see background info and examples above at [Files and Variables](#FilesAndVariables).

<a name="TF_CountLines"></a>

**TF_CountLines(Text)**

- Purpose: Returns the number of lines in a file or variable
- Parameters: Text
- Credits: SKAN


```autohotkey
MsgBox % TF_CountLines("File.txt") ; show the number of lines of file in a MsgBox
Lines:=TF_CountLines("File.txt") ; store the number of lines of file in a variable
```

<a name="TF_Count"></a>    

**TF_Count(String, Char)**

- Purpose: Count the number of occurrence of Char using StringReplace
- Parameters: String, Char

Notes: do **not** use it to count lines (using `n) because it will return an incorrect value,
use TF_CountLines for counting lines. TF_Count does not support files and only works with variables.


```autohotkey
MsgBox % TF_Count("Hello this is an example", "i") ; count how many i's there are in the string
```

<a name="TF_ReadLines"></a>

**TF_ReadLines(Text, StartLine = 1, EndLine = 0, RemoveTrailing = 0)**

- Purpose: Return the specified lines, can be used to read sections of a file or variable.
- Parameters: Text, StartLine, EndLine

Note: by default it will add a newline character to the last line, so if you want to 
append new data you don't have to start it with a newline char. If you don't want
that use RemoveTrailing = 1.


```autohotkey
MsgBox % TF_ReadLines("File.txt",5) ; Read lines 5 to end of file, show result in a MsgBox
Lines:=TF_ReadLines("File.txt",5) ; Read lines 5 to end of file, store result in variable
MsgBox % TF_ReadLines("File.txt",5,0,1) ; 0 for end line indicates until end of file, remove trailing empty line.
```

<a name="TF_Tail"></a>

**TF_Tail(Text, Lines = 1, RemoveTrailing = 0, ReturnEmpty = 1)**

- Purpose: Read last X lines from file or variable
- Parameters: Text, Lines, RemoveTrailing (0 or 1), ReturnEmpty (0 or 1)
- Credits: incl. ideas from Tuncay

Notes:

1.  Lines can be a negative number: -1 will get the second to last line.
    If you use a negative number it will ALWAYS retrieve a SINGLE line
2.  By default TF_Tail will return empty lines, if you don't want that
    use ReturnEmpty = 0
3.  By default TF_Tail will add a newline character to the last line, so
    if you want to append new data to the returned data you don't have
    to start it with a newline char. If you don't want that use
    RemoveTrailing = 1


```autohotkey
MsgBox % TF_Tail("File.txt", 3) ; get the last three lines
MsgBox % TF_Tail("File.txt", -2) ; get second to last line, negative values only return one line
MsgBox % TF_Tail("File.txt", 5, 0, 0) ; return the last five lines, with trailing new line and excluding empty lines
```

<a name="TF_Replace"></a>

**TF_Replace(Text, SearchText, Replacement="")**

- Purpose: Find and Replace text in entire file (using StringReplace)
- Parameters: Text, SearchText, Replacement


```autohotkey
TF_Replace("File.txt","key","lock")            ; pass on a file, replace the word "key" with "lock" in file_copy.txt
```

<a name="TF_ReplaceInLines"></a>

**TF_ReplaceInLines(Text, StartLine = 1, EndLine = 0, SearchText = "", ReplaceText = "")**

- Purpose: Find and Replace text on specific lines (using StringReplace)
- Parameters: Text, StartLine, EndLine, SearchText, ReplaceText


```autohotkey
TF_ReplaceInLines("!File.txt","1,3,9","","key","lock") ;  update source file, replace "key" with "lock" in lines 1, 3 and 9
```

<a name="TF_RegExReplace"></a>

**TF_RegExReplace(Text, NeedleRegEx = "", Replacement = "")**

- Purpose: Find and Replace text in entire file (using RegExReplace)
- Parameters: Text, NeedleRegEx, Replacement


```autohotkey
TF_RegExReplace("File.txt","im)^(.*)$","[$1]") ; pass on a file, wrap all lines in []
```

<a name="TF_RegExReplaceInLines"></a>

**TF_RegExReplaceInLines(Text, StartLine = 1, EndLine = 0, NeedleRegEx= "", Replacement = "")**

- Purpose: Find and Replace text in specific lines (using RegExReplace)
- Parameters: Text, StartLine, EndLine, NeedleRegEx, Replacement


```autohotkey
TF_RegExReplaceInLines("File.txt",3,8," [a-z]{3} "," lock ")  ; replace any three letter word with "lock" in lines 3 to 8
```

<a name="TF_RemoveLines"></a>

**TF_RemoveLines(Text, StartLine = 1, EndLine = 0)**

- Purpose: Remove specified lines from file
- Parameters: Text, StartLine, EndLine


```autohotkey
TF_RemoveLines("File.txt", 5, 10) ; remove lines 5 to 10 from file
```

Note: If you pass on a negative value for StartLine, example
TF_RemoveLines(Text, -5, .... it will remove these lines from the end
of Text, so -5 will remove the last five lines. The EndLine parameter is
ignored.


<a name="TF_RemoveBlankLines"></a>

**TF_RemoveBlankLines(Text, StartLine = 1, EndLine = 0)**

- Purpose: Remove blank lines from file (in specified section)
- Parameters: Text, StartLine, EndLine


Note: also removes lines with only tabs & spaces e.g. "white space"


```autohotkey
TF_RemoveBlankLines("File.txt") ; remove blanklines in entire file
```


<a name="TF_RemoveDuplicateLines"></a>

**TF_RemoveDuplicateLines(Text, StartLine = 1, Endline = 0, Consecutive= 0, CaseSensitive = false)**

- Purpose: Remove duplicate lines, optionally you can specify if they need to be Consecutive and / or Case sensitive
- Parameters: Text, StartLine, EndLine, Consecutive (0 or 1), CaseSensitive (true or false)


```autohotkey
TF_RemoveDuplicateLines("File.txt","","",1,false) ; remove only Consecutive duplicate lines
```

<a name="TF_InsertLine"></a>

**TF_InsertLine(Text, StartLine = 1, Endline = 0, InsertText = "")**

- Purpose: Insert text in specified lines
- Parameters: Text, StartLine, Endline, InsertText


```autohotkey
TF_InsertLine("File.txt","2,4,9",5,"---")     ; insert --- in lines 2 4 and 9. 5 is endline will be ignored
```

<a name="TF_ReplaceLine"></a>

**TF_ReplaceLine(Text, StartLine = 1, Endline = 0, ReplaceText = "")**

- Purpose: Replace specified lines with new text
- Parameters: Text, StartLine, Endline, ReplaceText


```autohotkey
TF_ReplaceLine("File.txt","1+3",8,"---")      ; replace lines 1 4 and 7. 8 is end line so no more lines are processed after 
```

<a name="TF_InsertPrefix"></a>

**TF_InsertPrefix(Text, StartLine = 1, EndLine = 0, Text = "")**

- Purpose: Insert a text at the BEGINNING of each of the specified lines
- Parameters: Text, StartLine, Endline, Text


```autohotkey
TF_InsertPrefix("File.txt",5,8, "Hello ")          ; Prefix Hello in lines 5 to 8
```

<a name="TF_InsertSuffix"></a>

**TF_InsertSuffix(Text, StartLine = 1, EndLine = 0 , Text = "")**

- Purpose: Append a text at the END of each of the specified lines
- Parameters: Text, StartLine, Endline, Text


```autohotkey
TF_InsertSuffix("File.txt","2-4,6-9","", " Hello")  ; Suffix Hello in lines 2 to 4 and 6 to 9
```

<a name="TF_TrimLeft"></a>

**TF_TrimLeft(Text, StartLine = 1, EndLine = 0, Count = 1)**

- Purpose: Trim number of specified columns from specified lines
- Parameters: Text, StartLine, Endline, Text, Count


```autohotkey
TF_TrimLeft("File.txt","","",25)                    ; Trim Left 25 Characters of all lines
```

<a name="TF_TrimRight"></a>

**TF_TrimRight(Text, StartLine = 1, EndLine = 0, Count = 1)**

- Purpose: Trim number of specified columns from specified lines
- Parameters: Text, StartLine, Endline, Text, Count


```autohotkey
TF_TrimRight("File.txt","4,6,8","",45)              ; Trim Right 45 Characters in lines 4, 6 and 8
```

<a name="TF_AlignLeft"></a>

**TF_AlignLeft(Text, StartLine = 1, EndLine = 0, Columns = 80, Padding = 0)**

- Purpose: Align lines according to number of specified columns
- Parameters: Text, StartLine, Endline, Columns, Padding
 - incl. ideas from SKAN

Note: Using the Align functions will not take into account any leading or trailing
spaces a line had BEFORE processing. So all white space before and after the text
is removed before the text is aligned.

~~~~
Padding = 0 Remove trailing white space
Padding = 1 Keep trailing white space
~~~~


```autohotkey
TF_AlignLeft("File.txt","","",90, 1)    ; AlignLeft all lines, keep trailing white space
```

<a name="TF_AlignCenter"></a>

**TF_AlignCenter(Text, StartLine = 1, EndLine = 0, Columns = 80, Padding = 0)**

- Purpose: Align lines according to number of specified columns
- Parameters: Text, Columns, StartLine, Endline, Text
- uses some of SKAN functions

Note: Using the Align functions will not take into account any leading or trailing
spaces a line had BEFORE processing. So all white space before and after the text
is removed before the text is aligned.

~~~~
Skip = 0 process empty lines, fill with spaces
Skip = 1 skip empty lines, do not fill with spaces
~~~~


```autohotkey
TF_AlignCenter("File.txt","","",150, 1) ; AlignCenter, all lines skip empty lines, do not fill with spaces
```

<a name="TF_AlignRight"></a>

**TF_AlignRight(Text, StartLine = 1, EndLine = 0, Columns = 80, Skip = 0)**

- Purpose: Align lines according to number of specified columns
- Parameters: Text, Columns, StartLine, Endline, Skip (0 or 1)
 - uses some of SKAN functions

Note: Using the Align functions will not take into account any leading
or trailing spaces a line had BEFORE processing. So all white space
before and after the text is removed before the text is aligned.

~~~~
Skip = 0 process empty lines, fill with spaces
Skip = 1 skip empty lines, do not fill with spaces
~~~~


```autohotkey
TF_AlignRight("File.txt","","", 190, 0) ; AlignRight, all lines, do not skip empty lines fill them with spaces
```

<a name="TF_LineNumber"></a>

**TF_LineNumber(Text, Leading = 0, Restart = 0, Char = 0)**

- Purpose: Insert line numbers before each line
- Parameters: Text, Leading, Restart, Char
- Credits: incl. ideas from ribbet.1

~~~~
Leading = 0 No padding with leading zeros
Leading >= 1 Include padding with leading zeros (001 v 1)
Restart = restart counter after X lines (starting again at 1)
Char = character to use for leading/padding. Default 0, but can be any character. Tip: use a space.
~~~~


```autohotkey
TF_LineNumber("File.txt",1,15,A_Space)  ; Add linenumbers, padding with spaces, linenumbers are aligned right because of space
```

<a name="TF_ConCat"></a>

**TF_ConCat(FirstTextFile, SecondTextFile, OutputFile, Blanks = 0, FirstPadMargin = 0, SecondPadMargin = 0)**

- Purpose: Join Text files side by side (line1-from-file1 line1-from-file2 newline line2-from-file1 line2-from-file2 newline etc)
- Parameters: FirstTextFile, SecondTextFile, OutputFile, Blanks, FirstPadMargin, SecondPadMargin

Based on: CONCATenate text files, ftp://garbo.uwasa.fi/pc/ts/tsfltc22.zip - Backup: http://www.retroarchive.org/garbo/pc/ts/

With TF_ConCat you can join Text files side by side. Blanks is number of blanks between lines.
You can pad blanks the right margin of either of the text files, for this use FirstPadMargin and SecondPadMargin.

<a name="TF_ColGet"></a>

**TF_ColGet(Text, StartLine = 1, EndLine = 0, StartColumn = 1, EndColumn = 1, Skip = 0)**

- Purpose: Get specified columns from text file
- Parameters: Text, StartLine, EndLine, StartColumn, EndColumn, Skip (0 or 1)

~~~~
Note: A TAB character is 1 character so for files with TABS the column might not be where you expect it to be
skip = 0, DO NOT skip lines shorter then startcolumn position
skip = 1, skip lines shorter then startcolumn position
TF_ColGet and TF_ColPut accept negative StartColumn parameter. This can be used to "Get" or "Put" text in the x-th "column" from the right
~~~~


```autohotkey
TF_ColGet("File.txt","","",2,13)    ; get columns 2 to 13 from all lines, so it removes all other columns from the file or variable
```

<a name="TF_ColPut"></a>

**TF_ColPut(Text, Startline = 1, EndLine = 0, StartColumn = 1, Text = "", Skip = 0)**

- Purpose: Insert text at specified columns in text file
- Parameters: Text, StartLine, EndLine, StartColumn, EndColumn, Skip (0 or 1)

Based on: COLPUT.EXE & CUT.EXE, ftp://garbo.uwasa.fi/pc/ts/tsfltc22.zip - Backup: http://www.retroarchive.org/garbo/pc/ts/

~~~~
Note: A TAB character is 1 character so for files with TABS the column might not be where you expect it to be
skip = 0, DO NOT skip lines shorter then startcolumn position
skip = 1, skip lines shorter then startcolumn position
TF_ColGet and TF_ColPut accept negative StartColumn parameter. This can be used to "Get" or "Put" text in the x-th "column" from the right
~~~~


```autohotkey
TF_ColPut("File.txt","","",5, "|", 0) ; insert | in column 5 of all lines including empty lines (= | will be at column 1)
```

<a name="TF_ColCut"></a>

**TF_ColCut(Text, StartLine = 1, EndLine = 0, StartColumn = 1, EndColumn = 1)**

- Purpose: Remove specified columns from text file
- Parameters: Text, StartLine, EndLine, StartColumn, EndColumn

Based on: COLPUT.EXE & CUT.EXE, ftp://garbo.uwasa.fi/pc/ts/tsfltc22.zip


```autohotkey
TF_ColCut("File.txt", "2+2", "", 4, 38)  ; remove columns 4 to 38 in lines 2 4 6 8 etc
```

<a name="TF_ReverseLines"></a>

**TF_ReverseLines(Text, StartLine = 1, EndLine = 0)**

- Purpose: Reverse the order of specified lines
- Parameters: Text, StartLine, EndLine

Note: Startline parameter can not use specific lines, sections or incremental values here


```autohotkey
TF_ReverseLines("File.txt",2,9) ; reverse lines 2 to 9
```

<a name="TF_Find"></a>

**TF_Find(Text, StartLine = 1, EndLine = 0, SearchText = "", ReturnFirst = 1, ReturnText = 0)**

- Purpose: Find text using RegExMatch, return line(s), text or lines and text
- Parameters: Text, StartLine, EndLine, SearchText, ReturnFirst, ReturnText

TF_Find(Lines) uses Regular Expressions - This means that if you use certain characters which
have a special meaning in a RegEx: **\\.\*?+[{|()\^\$** they must be preceded by a backslash
to be seen as literal. For example, **\\.** is a literal period and **\\\\** is a literal backslash.
Escaping can be avoided by using **\\Q**...**\\E**. For example: **\\QLiteral Text\\E**.
See [http://ahkscript.org/docs/commands/RegExMatch.htm](http://ahkscript.org/docs/commands/RegExMatch.htm)
for further information.

~~~~
ReturnFirst = 0 return multiple lines 
ReturnFirst = 1 return first line only

ReturnText = 0 return line numbers only
ReturnText = 1 return entire line (text). This simulates a basic grep feature
ReturnText = 2 return line numbers + entire line (text). This simulates a basic grep feature 
~~~~


```autohotkey
MsgBox % TF_Find("File.txt", "", "", "keys") ; return first line number with keys in it
MsgBox % TF_Find("File.txt", "", "", " c[a-z]+s ", 0, 1) ; find all lines with words that start with a c an end with an s
```

<a name="TF_SplitFileByLines"></a>

**TF_SplitFileByLines(Text, SplitAt, Prefix = "file", Extension = "txt", InFile = 1)**

- Purpose: Split a text file in to several others based on number of lines
- Parameters: Text, SplitAt (number), Prefix, Extension, InFile (0, 1 or 2)

~~~~
SplitAt = Number of lines (three methods, see below)
Prefix . Extension = filenameAUTOINCREMENT.Extension (Example: part_ . txt)
InFile = 0 skip line e.g. do not include the actual line in any of the output files
InFile = 1 include line IN current file
InFile = 2 include line IN next file
~~~~

Note: If you pass on a variable to TF_SplitFileByLines, the array size will be returned
as Prefix0 and the array elements as Prefix1, Prefix2 etc similar to the AHK StringSplit
command. The Extension parameter is ignored when using variables. Some characters are
illegal such as - + @ % & \* _ \\ / [ ] etc to use in Prefix, stick to a-z A-Z.

Options for "SplitAt" parameter:

~~~~
a) One numerical value, example TF_SplitFileByLines(Text, "25", .... 
   will split text every 25 lines until the end 
b) Split at rotating line lengths using a dash "-" as separator, example TF_SplitFileByLines(Text, "5-10-15", .... 
   will split text at 5 10 15 lines, until the end so 5 lines, 10 lines, 15 lines, 5 lines, 10 lines etc
c) Split at specific lines using a comma "," as separator, example TF_SplitFileByLines(Text, "5,81,135", .... 
   will split text at lines 5,81,135 until end of file (e.g. last file will be from line 135 until the end)
~~~~


```autohotkey
TF_SplitFileByLines("File.txt", 2, "part", "zec", 1) ; split source file every 2 lines, include 2nd line INFILE
; illustrate use of variables and returned arrays:
TF_SplitFileByLines(Variable, 2, "part", "zec", 1)
MsgBox % "Array size: " . Part0 . "`n1st array element: "  Part1
```

<a name="TF_SplitFileByText"></a>

**TF_SplitFileByText(Text, SplitAt, Prefix = "file", Extension = "txt", InFile = 1)**

- Purpose: Split a text file in to several others based on text
- Parameters: Text, SplitAt (text, can be RegEx), Prefix, Extension, InFile (0, 1 or 2)

~~~~
SplitAt = Text, can be RegEx
Prefix . Extension = filenameAUTOINCREMENT.Extension (Example: part_ . txt)
InFile = 0 skip line e.g. do not include the actual line in any of the output files
InFile = 1 include line IN current file
InFile = 2 include line IN next file
~~~~

Note: If you pass on a variable to TF_SplitFileByLines, the array size will be returned
as Prefix0 and the array elements as Prefix1, Prefix2 etc similar to the AHK StringSplit
command. The Extension parameter is ignored when using variables. Some characters are 
illegal such as - + @ % & \* _ \\ / [ ] etc to use in Prefix, stick to a-z A-Z.


```autohotkey
TF_SplitFileByText("File.txt", "button", "part", "zec", 1)   ; split source file on every line with the word button, include that line INFILE
; illustrate use of variables and returned arrays:
TF_SplitFileByText(Variable, "keyboard", "part", "zec", 1)
MsgBox % "Array size: " . Part0 . "`n1st array element: "  Part1
```

**TF_Merge(FileList, Separator = "\`n", FileName = "merged.txt")**

- Purpose: Merge several files into one
- Parameters: FileList, Separator (what to put between two files, newline by default), FileName (name of output file, Prefix with a ! to overwrite target file)

Example FileList:

~~~~
FileList=
(
file1.txt
file2.txt
file3.txt
)
~~~~

Note: TF_Merge, TF_Prepend, TF_Append currently do not support variables and only work with FILES.


```autohotkey
; using Loop (files & folders) to create one quickly if you want to merge all TXT files for example:
Loop, c:\*.txt
  FileList .= A_LoopFileFullPath "`n"
TF_Merge(FileList) ; will create merged.txt, you can use ! to overwrite an existing file if you want

; using FileSelectFile to select files to merged: (Thanks for asking Vitor, http://www.autohotkey.com/forum/viewtopic.php?p=335329#335329)
FileDelete merged.txt ; not required
FileList=
FileSelectFile, FileList, M 1,,, *.txt ; M allows you to select multiple files while holding down the left ctrl button
If (ErrorLevel = 1) or (FileList = "")
   ExitApp ; no files selected
Path:=TF_ReadLines(FileList,1,1,1) ; the first line holds the directory of the selected files, so read path
FileList:=TF_RemoveLines(FileList,1,1) ; remove path from filelist
FileList:=TF_InsertPrefix(FileList, "", "", Path . "\") ; make sure all files have full paths to file so the are read correctly
TF_Merge(FileList) ; will create a file in the current script dir called merged.txt you can also specify another filename, take into account the filedelete merged.txt above

; You could skip the Path:= step above by calling TF_ReadLines directly in TF_InsertPrefix, but you would have to delete the first line AFTER it like so:
;FileList:=TF_InsertPrefix(FileList, "", "", TF_ReadLines(FileList,1,1,1) . "\")
;FileList:=TF_RemoveLines(FileList,1,1)
```

<a name="TF_Prepend"></a>

**TF_Prepend(File1, File2)**

- Purpose: Prepend file1 to file2 (file2 is changed, uses TF_Merge)
- Parameters: File1, File2

Note: TF_Merge, TF_Prepend, TF_Append currently do not support variables and only work with FILES.

<a name="TF_Append"></a>

**TF_Append(File1, File2)**

- Purpose: Append file1 to file2 (file2 is changed, uses TF_Merge)
- Parameters: File1, File2

Note: TF_Merge, TF_Prepend, TF_Append currently do not support variables and only work with FILES.

<a name="TF_Wrap"></a>

**TF_Wrap(Text, Columns = 80, AllowBreak = 0, StartLine = 1, EndLine = 0)**

- Purpose: Wrap (specified) lines
- Parameters: Text, Columns, AllowBreak (0 or 1), StartLine, EndLine

~~~~
AllowBreak = 0 will not "break" words, so it will take into account whole words and not chop them off. 
AllowBreak = 1 will break words
~~~~


```autohotkey
TF_Wrap("File.txt",60)            ; wrap at col 60
```    

<a name="TF_WhiteSpace"></a>

**TF_WhiteSpace(Text, RemoveLeading = 1, RemoveTrailing = 1, StartLine = 1, EndLine = 0)**

- Purpose: Remove leading and/or trailing whitespace
- Parameters: Text, RemoveLeading (0 or 1), RemoveTrailing (0 or 1), StartLine, EndLine

~~~~
RemoveLeading = 0  Do not remove leading white space of lines
RemoveLeading = 1  Remove leading white space of lines
RemoveTrailing = 0 Do not remove trailing white space of lines
RemoveTrailing = 1 Remove trailing white space of lines       
~~~~


```autohotkey
TF_WhiteSpace("File.txt", 1, 0, "5-10") ; remove leading and keep trailing whitespace in lines 5 to 10
```    

<a name="TF_Substract"></a>

**TF_Substract(File1, File2, PartialMatch = 0)**

- Purpose: Delete lines from file1 in file2 (using StringReplace)
- Parameters: File1, File2, PartialMatch (0 or 1)

~~~~
File2: you can use !file2 to overwrite file2 otherwise output is file2_copy.txt
PartialMatch = 0 lines from file1 must appear as is, case insensitive 
PartialMatch = 1 allow for paRTIal match of line
PartialMatch = 2 allow for paRTIal match of line, but remove entire line (uses RegEx, new as of v3.4)
~~~~

<a name="TF_RangeReplace"></a>

**TF_RangeReplace(Text, SearchTextBegin, SearchTextEnd, ReplaceText = "", CaseSensitive = "False", KeepBegin = 0, KeepEnd = 0)**

- Purpose: A Range Replacement allows you to perform a replacement on text whose beginning and ending remains the same, but whose middle contents might change.
- Parameters: SearchTextBegin, SearchTextEnd, ReplaceText, CaseSensitive (True or False), KeepBegin (0 or 1), KeepEnd (0 or 1)

Note: Similar to "BK Replace EM" Range Replacement. It is basically an easier shortcut for a more complex RegExp.

~~~~
KeepBegin = 0 Remove SearchTextBegin from file
KeepBegin = 1 Do not remove SearchTextBegin from file (saves you the trouble of putting it back in via ReplaceText)
KeepEnd = 0 Remove SearchTextEnd from file
KeepEnd = 1 Do not remove SearchTextEnd from file (saves you the trouble of putting it back in via ReplaceText)

SearchTextBegin = "" e.g. empty means from START of File *1
SearchTextEnd = "" e.g. empty means until END of File *1
~~~~

\*1 if you pass on empty strings to SearchTextBegin and SearchTextEnd the entire file will 
replaced with the replacement text.
SearchTextBegin/End **can** be on the same line. Remember this LIB mainly operates on
a line by line basis. This Function only operates on the FIRST SearchTextBegin to 
the FIRST SearchTextEnd it finds.


```autohotkey
Range=[insert this`ntext for the`nrange replace Text`ntest function]
TF_RangeReplace("File.txt", "Create hotkeys for keyboard", "into an EXE file", Range)
```

<a name="TF_MakeFile"></a>

**TF_MakeFile(Text, Lines = 1, Columns = 1, Fill = " ")**

- Purpose: Create file of X lines and Y columns, fill with space or other character(s)
- Parameters: TextFile (new file to be created), Lines, Columns, Fill

Sometimes you need an "empty" file before you can start adding line numbers or putting data into a file.

<a name="TF_Tab2Spaces"></a>

**TF_Tab2Spaces(Text, TabStop = 4, StartLine = 1, EndLine = 0)**

- Purpose: Convert tabs to spaces, shorthand for TF_ReplaceInLines
- Parameters: Text, TabStop, StartLine, EndLine
- Credits: incl. ideas from infogulch

~~~~
TabStop = number of spaces to replace a TAB with, so 4 means a TAB will be replaced by 4 spaces
~~~~

<a name="TF_Spaces2Tab"></a>

**TF_Spaces2Tab(Text, TabStop = 4, StartLine = 1, EndLine = 0)**

- Purpose: Convert tabs to spaces, shorthand for TF_ReplaceInLines
- Parameters: Text, TabStop, StartLine, EndLine
- Credits: incl. ideas from infogulch

~~~~
TabStop = number of spaces to replace with a TAB, so 4 means 4 spaces will be replaced by a TAB
~~~~

<a name="TF_Sort"></a>

**TF_Sort(Text, SortOptions = "", StartLine = 1, EndLine = 0)**

- Purpose: Sort (section of) TextFile
- Parameters: Text, SortOptions, StartLine, EndLine

~~~~
SortOptions: use the SORT options http://ahkscript.org/docs/commands/Sort.htm
~~~~

Note: StartLine can not have multiple sections, increments or multiple lines in this case.
When dealing with variables/strings instead of text files the native AHK Sort command is
of course more useful.

<a name="TF_Join"></a>

**TF_Join(Text, StartLine = 1, EndLine = 0, SmartJoin = 0, Char = 0)**

- Purpose: Join lines (section of)
- Parameters: Text, StartLine, EndLine, SmartJoin, Char

~~~~
SmartJoin: Detect if CHAR(s) is/are already present at the end of the line before joining the next, this to prevent unnecessary double spaces for example.
Char: character(s) to use between new lines, defaults to a space. To use nothing use ""
~~~~

Note: StartLine can not use increments. It can have multiple sections.

<a name="TF_Save"></a>

**TF_Save(Text, FileName, OverWrite = 1)**

- Purpose: To save a variable to a file
- Parameters: Text, FileName, OverWrite (O or 1)

~~~~
OverWrite = 0 create filename_copy.ext if filename.ext exists
OverWrite = 1 will overwrite filename.ext if filename.ext exists (default)
~~~~

# TF Lib Errors

1. Error when using the ! prefix.  
   The correct syntax is "!file.txt". If you do this !"file.txt" (the ! is not within the quotes it will not be able to read a file)
2. If you pass on a single zero as text ```TF_TrimLeft("0",1,1,3)``` and there is no file with that name ```"0"``` it will shown an error. More as one zero as "text" is OK.

# History

**History v3.8, 11 December 2020**

- Prevent TF_Sort from removing last character from the last line https://github.com/hi5/TF/issues/11

**History v3.7, 16 April 2017**

- Added: additional check for passing on just zero/zeros as text to TF functions in TF_GetData() - see v3.6 below
- Fix: changed readme.md to fix rendering issue of MD files on GH - https://github.com/hi5/TF/issues/5

**History v3.6, 25 December 2014**

- Added: Added additional minor error check in TF_GetData - if "Text" is "false", there is nothing to process so Exit

**History v3.5, 2 August 2014**

- Changed A_ScriptDir to A_WorkingDir in TF_ReturnOutPut - https://github.com/hi5/TF/issues/1
- Fixed: TF_ColGet negative startcolumn and sections now work correctly

**History v3.4, 30 October 2010 - UNRELEASED at the time**

- New function: TF_Join: joining lines.
- Added: A_ThisFunc to helper function TF_MatchList, this may help with error messages such as: Invalid StartLine parameter (non numerical character) - Function used: A_ThisFunc
- Added/Change: New option for StartLine, Negative startline operate on last X lines was already the case for TF_removelines, applies to all TF functions
- Added: TF_Substract: Partialmatch = 2 now deletes entire line on partial match, see TF DOC for more info.
- Added: TF_ColGet and TF_ColPut now accept negative StartColumn parameter. Can be used to Get or Put text in the xth "column" from the right, see description above - TF_ColCut does **not** support this as you can use TF_TrimRight.
- Added: TF_ColGet, TF_ColPut and TF_ColCut can now process multiple columns in one go. Format: CSV or incremental (DO **not** use sections for TF_ColPut due to unexpected end results)
- Minor improvements to the documentation
- removed TF_FindLines from lib (was already deprecated)

**History v3.3, 16 April 2010**

- Fixed: If you used variables with any of the replacement functions or tried to remove empty lines from a VARIABLE in the following format: variable:=TF_..(variable,"search","replace") and the searchtext was NOT present in the variable it returned an empty variable (e.g. deleted the contents of "variable"). It affected TF_ReplaceInLines, TF_Replace, TF_RegExReplaceInLines, TF_RegExReplace, TF_RemoveBlankLines, TF_RangeReplace\. The built-in check only worked correctly for FILES and with the introduction of variables in TF 3 this didn't surface during testing.
- Fixed: documentation error for TF_Merge (separator and filename where swapped) and added examples on how to use TF_Merge in a Loop and with FileSelectFile

**History v3.2, 20 February 2010**

- Fixed: TF_AlignRight: due to a bug it didn't work as it should have, it prepended the number of spaces rather than aligning the text at the specified width.
- Changed: TF_GetData (helper function) should now avoid unnecessary IfNotExist for files, for scripts with many loops in combination with variables it should improve the speed slightly
- Changed: TF_SplitFileByLines: New options for SplitAt, now three methods available, see notes at function description
- Changed: TF_RemoveLines: New option for StartLine, if negative value is used it will remove the last X lines from file, see notes at function description

**History v3.1, 09 December 2009**

- Changed: Rewrite of TF_Find. Can now return multiple lines (like TF_Findlines used to), not only linenumbers but the entire line (text) of found lines so it can be used as a basic grep. Now uses RegExp, see compatibility notes.
- Deprecated: TF_FindLines (see change TF_Find). Kept in for backwards compatibility
- Updated and expanded documentation with examples and a hopefully "better" introduction highlighting some basic concepts

**History v3.0, 27 November 2009**

Complete overhaul of library, now accepts files & variables for input and output:

- Changed: New parameter for TF_Readlines & TF_Tail: trailing new line now optional
- Added: TF_Save, shorthand for filedelete+fileappend
- Added: TF_GetData, helper function to determine if a file or a variable is passed on to function
- Added: TF() To read a text file in global var, t by default - Credits various ...
- Added: TF_ReturnOutput has replaced: Overwrite, MakeCopy and the newly developed ReturnStr
- Changed: Complete rewrite of TF_Tail, new options - borrowed bits from Tuncay (Thanks!)
- Changed: MakeMatchList: Removed TF_Countlines (one less fileread), Pass on "String" and not a TextFile
- Fix: TF_ConCat & TF_MakeFile didn't write output file - same bug as splitby* earlier.

**History v2.5 fix, 01 November 2009**

- Unreleased, but available on request :-) Note that in 2.4a TF_MakeFile and TF_ConCat do not produce output files, easy to fix if you are determined to use an older version of TF.

**History v2.4a fix, 10 August 2009:**

- The fix for TF_SplitFileBy* functions of 2.3b wasn't complete, now  it should work correctly (it didn't write the last file)

**History v2.4 update, 06 August 2009:**

- Added: TF_Sort

**History v2.3b update, 03 August 2009:**

- Fixed: No output problem for TF_SplitFileBy* functions, "bug" introduced by 2.3a "Built in Check if TextFile actually exists"
- TF_RemoveBlankLines check if file has empty empty lines to start with, if not return and do nothing (does not create file_copy identical to file)
- TF_RangeReplace same fix as 2.3a

**History v2.3a update/bugfix (29 July 2009, HugoV, ribbet.1, Murp|e)**

Fixed/Changed:

- New features in TF_LineNumber: Restart + Choice of leading/padding character. Thanks for the idea ribbet.1, [http://www.autohotkey.com/forum/viewtopic.php?p=284687#284687](http://www.autohotkey.com/forum/viewtopic.php?p=284687#284687)
- Built in Check if TextFile actually exists, to prevent creation of empty file(s). Thanks for the idea Murp|e, [http://www.autohotkey.com/forum/viewtopic.php?p=284649#284649](http://www.autohotkey.com/forum/viewtopic.php?p=284687#284687)
- TF_Replace: if SearchText wasn't present in TextFile the function never returned (stuck in endless loop)
- TF_ReplaceInLines: if SearchText wasn't present in TextFile simple return an do nothing (faster, does not create file_copy)
- TF_RegExReplaceInLines: if NeedleRegEx wasn't present in TextFile simple return an do nothing (faster, does not create file_copy)

**History v2.3 (28 July 2009)**

- Added: TF_Tab2Spaces - Note: Thanks to infogulch
- Added: TF_Spaces2Tab - ditto
- Added: TF_RangeReplace
- Added: TF_MakeFile

Also thanks to Murp|e for pointing out some errors in the documentation (TF_COL\* functions).

**History v2.2 (10 July 2009)**

- Added: TF_Substract
- Added: TF_WhiteSpace
- Added: TF_Wrap

**History v2.1**

- Added: TF_Prepend
- Added: TF_Append

**History v2.0 (Overhaul by HugoV using TXT lib as starting point)**

- Renamed: TF_TotalLines to TF_CountLines (seemed more logical)
- Removed: TF_GetCSV, TF_SetCSV to keep the focus on TXT files, CSV requires a different library IMHO (HugoV)
- Introduced: _MakeMatchList_ and adjusted TF_ReadLines, TF_RemoveLines, TF_InsertPrefix, TF_InsertSuffix, TF_TrimLeft, TF_TrimRight, TF_ColGet, TF_ColPut, TF_ColCut accordingly (HugoV)
     - Make a MatchList which is used in various functions (listed above)
     - Using a MatchList gives greater flexibility so you can process multiple sections of lines in one go avoiding repetitive fileread/append actions
     - You can pass on multiple lines (sections) If you quote the StartLine parameter. Examples of StartLine, Endline:
     5 ; start from line 5 to the end (StartLine)
     5, 15 ; lines 5 to 15 (StartLine, EndLine)
     "5,13,45,67", 135) ; lines 5,13,45,67, ignore 135 ("StartLine", Endline - EndLine value is ignored)
     "5-13,45-51", 135) ; lines 5 to 13, 45 to 51 ignore 135 ("StartLine", Endline - EndLine value is ignored)
     - Incremental processing of lines, example startline, endline - endline will not be ignored
     "2+3", 150 ; start with line 2, increment 3 up-to line 150 so 2,5,8,11,14 etc
     "5+15" ; start with line 5, increment 15 until end of file so  5,20,35 etc
- Introduced: _OverWrite_ & _MakeCopy_ (Heresy / HugoV)
     - No longer necessary to pass on full path, e.g. "file.txt" works OK now (HugoV)
     - Reduces size of library by removing repeating code sections for saving Output (Heresy)
     - Backup files: If a subdirectory "backup" is present in the directory of TextFile a backup is made before overwriting the original file (both for file.txt and file_copy.txt) with the BAK extension (HugoV)
     - Includes fix to remove trailing \`n added by most functions (HugoV)
- Introduced: SetWidth/space by SKAN for TF_Align* Functions http://www.autohotkey.com/forum/viewtopic.php?p=45880#45880
- Added: TF_ReplaceInLines. Similar to TF_Replace
- Added: TF_RegExReplaceInLines. Similar to TF_RegExReplace
- Added: TF_SplitFileByLines (HugoV)
- Added: TF_SplitFileByText (HugoV)
- Added: TF_Merge (HugoV)
- Added: TF_Find (HugoV, based on example by olegbl)
- Added: TF_FindLines (HugoV, based on example by olegbl)
- Change: TF_TrimLeft. 1 - it now processes all lines of the TextFile. 2 - you can now omit EndLine e.g. will process until end of file (HugoV)
- Change: TF_TrimRight. 1 - it now processes all lines of the TextFile. 2 - you can now omit EndLine e.g. will process until end of file (HugoV)
- Change: TF_AlignCenter, TF_AlignLeft, TF_AlignRight. Now use SetWidth/space by SKAN, more reliable also adds spaces to RIGHT side of line
- Change: TF_AlignLeft. 1 - you can now use StartLine, EndLine parameters. 2 - StartLine and/or EndLine can be omitted (HugoV)
- Change: TF_AlignRight. 1 - you can now use StartLine, EndLine parameters. 2 - StartLine and/or EndLine can be omitted (HugoV)
- Change: TF_ReadLines you can now omit EndLine e.g. will read until end of file (HugoV)
- Change: TF_RemoveBlankLines you can now use StartLine, EndLine parameters. EndLine can be omitted (HugoV)
- Change: TF_InsertLine modified to accommodate line sections and incremental lines.
- Change: TF_ReplaceLine modified to accommodate line sections and incremental lines.
- Change: TF_RegExReplace no longer returns "Counts" (HugoV)
- Change: TF_RemoveDuplicateLines 1 - no longer returns "Removed". 2 - can use start/end line, 3 - no longer uses sort but keeps original order intact, 4 - to methods to check consecutive lines or unique files in file (HugoV)
- Change: TF_ColGet, TF_ColPut, TF_ColCut. 1 - order of parameters changed, 2 - now accept Startline, EndLine parameters. (HugoV)
- Fixed: TF_RemoveDuplicateLines always produced empty Output file (HugoV)

**History prior 2.0 - TXT library (Heresy, HugoV)**

- 2008-06-18 : added TF_AlignLeft(), TF_AlignCenter(),  TF_AlignRight() (Heresy)
- 2008-06-18 : added TF_Tail(), TF_ReverseLines() (HugoV)
- 2008-06-17 : added TF_GetCSV(), TF_SetCSV() (Heresy)
- 2008-06-17 : added TF_LineNumber(), TF_Concat(), TF_ColGet(), TF_ColPut(), TF_ColCut() (HugoV)
- 2008-06-16 : added TF_RegExReplace(), TF_RemoveLines(), TF_TrimLeft(), TF_TrimRight() (Heresy)
- 2008-06-15 : added TF_TotalLines(), TF_ReadLines(), TF_RemoveDuplicateLines() (Heresy)

## Credits

This library is based on the *Library for Text file manipulation* started by [Heresy](http://www.autohotkey.com/forum/profile.php?mode=viewprofile&u=8193). I have contributed a number of functions to that library, but that version also has some "bugs" which are hopefully resolved in this TF library.

Thanks to:
Heresy, SKAN! (countlines, setwidth, spaces), Olegbl, (suggestion for find*), infogulch (suggestion for tab \<-\> spaces),
Murp|e (suggestions for documentation, check if file exists), ribbet.1 (New features in TF_LineNumber), Tuncay (help with
TF() and borrowed ideas for TAIL).
