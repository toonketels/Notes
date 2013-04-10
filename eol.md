End Of Line
-----------


## Issue

Sometimes, git tells files are modified and their not. Git diffs indicate the entire file
has changed... This probably indicate end of line character issues.


## Background

Different operating systems use different chars to indicate end of line:
Windows     CRLF     Carriage Return Line Feed
Mac/Linux   LF       Line Feed

When files are copied from one system to another (of via web), newline characters are copied
with it.

Most programs to view and edit text can handle this. They may convert it automatically?

Git can be set to save the line endings in a specific way in the repo, but when checking out files
they will be converted automatically to the OS's flavor. This means

1. Save in one format
2. Checkout, modify in each  OS's format

Which means
* Checking out a file will convert the newline char if necessary
* Commiting a file will convert the newline char if necessary

And
* We need to set some standard global/per repo what should be in the repo.

## Resouces

* (wikipedia newline article)[http://en.wikipedia.org/wiki/Newline]
* 