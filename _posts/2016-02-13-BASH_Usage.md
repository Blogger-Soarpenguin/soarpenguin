***

###Variable Types
    Although Bash is not a typed language, it does have a few different types of variables. These types define the kind of content they are allowed to have. Type information is stored internally by Bash.

    1. Array: declare -a variable: The variable is an array of strings.
    2. Associative array: declare -A variable: The variable is an associative array of strings (bash 4.0 or higher).
    3. Integer: declare -i variable: The variable holds an integer. Assigning values to this variable automatically triggers Arithmetic Evaluation.
    4. Read Only: declare -r variable: The variable can no longer be modified or unset.
    5. Export: declare -x variable: The variable is marked for export which means it will be inherited by any child process.


###Here's a summary of most of the PE tricks that are available:

    Syntax     Description
    1. ${parameter:-word} 
       Use Default Value. If 'parameter' is unset or null, 'word' (which may be an expansion) is substituted. Otherwise, the value of 'parameter' is substituted.

    2. ${parameter:=word}
       Assign Default Value. If 'parameter' is unset or null, 'word' (which may be an expansion) is assigned to 'parameter'. The value of 'parameter' is then substituted.

    3. ${parameter:+word}
       Use Alternate Value. If 'parameter' is null or unset, nothing is substituted, otherwise 'word' (which may be an expansion) is substituted.

    4. ${parameter:offset:length}
       Substring Expansion. Expands to up to 'length' characters of 'parameter' starting at the character specified by 'offset' (0-indexed). If ':length' is omitted, go all the way to the end. If 'offset' is negative (use parentheses!), count backward from the end of 'parameter' instead of forward from the beginning. If 'parameter' is @ or an indexed array name subscripted by @ or *, the result is 'length' positional parameters or members of the array, respectively, starting from 'offset'.

    5. ${#parameter}
       The length in characters of the value of 'parameter' is substituted.

    6. ${parameter#pattern}
       The 'pattern' is matched against the beginning of 'parameter'. The result is the expanded value of 'parameter' with the shortest match deleted.

    7. ${parameter##pattern}
       As above, but the longest match is deleted.

    8. ${parameter%pattern}
       The 'pattern' is matched against the end of 'parameter'. The result is the expanded value of 'parameter' with the shortest match deleted.

    9. ${parameter%%pattern}
       As above, but the longest match is deleted.

    10. ${parameter/pat/string}
       Results in the expanded value of 'parameter' with the first (unanchored) match of 'pat' replaced by 'string'.

    11. ${parameter//pat/string}
       As above, but every match of 'pat' is replaced.


###Tests supported by [ (also known as test):

     -e FILE: True if file exists.
     -f FILE: True if file is a regular file.
     -d FILE: True if file is a directory.
     -h FILE: True if file is a symbolic link.
     -p PIPE: True if pipe exists.
     -r FILE: True if file is readable by you.
     -s FILE: True if file exists and is not empty.
     -t FD : True if FD is opened on a terminal.
     -w FILE: True if the file is writable by you.
     -x FILE: True if the file is executable by you.
     -O FILE: True if the file is effectively owned by you.
     -G FILE: True if the file is effectively owned by your group.
     FILE -nt FILE: True if the first file is newer than the second.
     FILE -ot FILE: True if the first file is older than the second.
     -z STRING: True if the string is empty (it's length is zero).
     -n STRING: True if the string is not empty (it's length is not zero).

String operators:

    1. STRING = STRING: True if the first string is identical to the second.  
    2. STRING != STRING: True if the first string is not identical to the second.  
    3. STRING < STRING: True if the first string sorts before the second.  
    4. STRING > STRING: True if the first string sorts after the second.  
    5. EXPR -a EXPR: True if both expressions are true (logical AND).  
    6. EXPR -o EXPR: True if either expression is true (logical OR).  
    7. ! EXPR: Inverts the result of the expression (logical NOT).  

Numeric operators:

    1. INT -eq INT: True if both integers are identical.  
    2. INT -ne INT: True if the integers are not identical.  
    3. INT -lt INT: True if the first integer is less than the second.  
    4. INT -gt INT: True if the first integer is greater than the second.  
    5. INT -le INT: True if the first integer is less than or equal to the second.  
    6. INT -ge INT: True if the first integer is greater than or equal to the second.  

Additional tests supported only by [[:  

    1. STRING = (or ==) PATTERN: Not string comparison like with [ (or test), but pattern matching is performed. True if the string matches the glob pattern.  
    2. STRING =~ REGEX: True if the string matches the regex pattern.  
    3. ( EXPR ): Parentheses can be used to change the evaluation precedence.  
    4. EXPR && EXPR: Much like the '-a' operator of test, but does not evaluate the second expression if the first already turns out to be false.  
    5. EXPR || EXPR: Much like the '-o' operator of test, but does not evaluate the second expression if the first already turns out to be true.  


###Conditional Loops (while, until and for)

    The two basic kinds of loops are called while and for. The while loop has a variant called until which simply reverses its check; and the for loop can appear in two different forms. Here's a summary:

    1. while command: Repeat so long as command is executed successfully (exit code is 0).
    2. until command: Repeat so long as command is executed unsuccessfully (exit code is not 0).
    3. for variable in words: Repeat the loop for each word, setting variable to each word in turn.
    4. for (( expression; expression; expression )): Starts by evaluating the first arithmetic expression; repeats the loop so long as the second arithmetic expression is successful; and at the end of each loop evaluates the third arithmetic expression.