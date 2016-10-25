If breaking out of a loop, and where you do not want to return to a parent loop, do NOT forget to issue the following:

    same => n,ExitWhile()
    same => n,ExitWhile()
    same => n,EndWhile()
    same => n,EndWhile()
    
And I've thrown them all in, and twice, for good measure. Otherwise you may find yourself in while-loop hell next time you go round the loop. 
Doesn't matter if all that makes sense right now, just do it!
