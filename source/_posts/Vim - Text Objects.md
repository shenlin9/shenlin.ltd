---
title: Vim - Text Objects
categories:
  - Vim
tags:
  - Vim
---

Vim - Text Objects

<!--more-->

## Text Objects

							*word*
A word consists of a sequence of letters, digits and underscores, or a
sequence of other non-blank characters, separated with white space (spaces,
tabs, <EOL>).  This can be changed with the 'iskeyword' option.  An empty line
is also considered to be a word.

							*WORD*
A WORD consists of a sequence of non-blank characters, separated with white
space.  An empty line is also considered to be a WORD.

A sequence of folded lines is counted for one word of a single character.
"w" and "W", "e" and "E" move to the start/end of the first word or WORD after
a range of folded lines.  "b" and "B" move to the start of the first word or
WORD before the fold.

Special case: "cw" and "cW" are treated like "ce" and "cE" if the cursor is
on a non-blank.  This is because "cw" is interpreted as change-word, and a
word does not include the following white space.  {Vi: "cw" when on a blank
followed by other blanks changes only the first blank; this is probably a
bug, because "dw" deletes all the blanks}

Another special case: When using the "w" motion in combination with an
operator and the last word moved over is at the end of a line, the end of
that word becomes the end of the operated text, not the first word in the
next line.

							*sentence*
A sentence is defined as ending at a '.', '!' or '?' followed by either the
end of a line, or by a space or tab.  Any number of closing ')', ']', '"'
and ''' characters may appear after the '.', '!' or '?' before the spaces,
tabs or end of line.  A paragraph and section boundary is also a sentence
boundary.
If the 'J' flag is present in 'cpoptions', at least two spaces have to
follow the punctuation mark; <Tab>s are not recognized as white space.
The definition of a sentence cannot be changed.

							*paragraph*
A paragraph begins after each empty line, and also at each of a set of
paragraph macros, specified by the pairs of characters in the 'paragraphs'
option.  The default is "IPLPPPQPP TPHPLIPpLpItpplpipbp", which corresponds to
the macros ".IP", ".LP", etc.  (These are nroff macros, so the dot must be in
the first column).  A section boundary is also a paragraph boundary.
Note that a blank line (only containing white space) is NOT a paragraph
boundary.
Also note that this does not include a '{' or '}' in the first column.  When
the '{' flag is in 'cpoptions' then '{' in the first column is used as a
paragraph boundary |posix|.

							*section*
A section begins after a form-feed (<C-L>) in the first column and at each of
a set of section macros, specified by the pairs of characters in the
'sections' option.  The default is "SHNHH HUnhsh", which defines a section to
start at the nroff macros ".SH", ".NH", ".H", ".HU", ".nh" and ".sh".

The "]" and "[" commands stop at the '{' or '}' in the first column.  This is
useful to find the start or end of a function in a C program.  Note that the
first character of the command determines the search direction and the
second character the type of brace found.

If your '{' or '}' are not in the first column, and you would like to use "[["
and "]]" anyway, try these mappings: >
   :map [[ ?{<CR>w99[{
   :map ][ /}<CR>b99]}
   :map ]] j0[[%/{<CR>
   :map [] k$][%?}<CR>
[type these literally, see |<>|]




" this si a  test
