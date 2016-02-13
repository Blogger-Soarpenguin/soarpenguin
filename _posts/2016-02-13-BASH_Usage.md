***

###Here's a summary of most of the PE tricks that are available:

###Syntax     Description
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

