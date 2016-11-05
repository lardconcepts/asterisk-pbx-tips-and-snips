If breaking out of a loop, and where you do not want to return to a parent loop, do NOT forget to issue the following:

    same => n,ExitWhile()
    same => n,ExitWhile()
    same => n,EndWhile()
    same => n,EndWhile()
    
And I've thrown them all in, and twice, for good measure. Otherwise you may find yourself in while-loop hell next time you go round the loop. 
Doesn't matter if all that makes sense right now, just do it!

Function REMAINDER does not give REMAINDER!

You'll want to use modulo (%) instead. This example dialplan should demonstrate

```
[remaindertest]
 exten => s,1,Verbose(Context: ${CONTEXT} Exten:${EXTEN}) 
    same => n,Set(seconds=57)
    same => n,While($[${seconds} <= 400]);
    same => n,Set(minutes=$[FLOOR(${seconds} / 60)])
    same => n,Set(myRemainderSec=$[REMAINDER(${seconds},60)])
    ;same => n,Set(sec=$[ABS(${sec})])
    ;same => n,Set(sec=$[${MATH(${sec}+0,i)}])
    
    same => n,SET(myModSec=${MATH(${seconds}%60,int)})  
    same => n,Verbose(1,Seconds:${seconds} = Minutes:${minutes} Remainder Seconds:${myRemainderSec} modulo seconds:${myModSec})
    same => n,Set(seconds=$[${seconds}+3])
    same => n,EndWhile()
    same => n,Hangup()   
```

##FILE will not create a new file when appending, *unless* the line ending type is given

This doesn't work:

    same => n,SET(FILE(/tmp/${EPOCH},,,al)=${EPOCH})
    
This will work:

    same => n,SET(FILE(/tmp/${EPOCH},,,al,u)=${EPOCH})
    
FILE will read garbage into a variable. It's a [known bug](https://issues.asterisk.org/jira/browse/ASTERISK-26481)

The workaround is 

     same => n,Set(featurefile=/home/test/feature-1.txt)
     same => n,Set(unfilteredfeat2=${FILE(${featurefile},0,1,l,u)})
     same => n,Set(feature2=${SHIFT(unfilteredfeat2)})


