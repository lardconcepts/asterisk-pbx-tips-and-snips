### Endwhile must be "seen" once before you can break out of a loop!

From [an issue tracker comment](https://issues.asterisk.org/jira/browse/ASTERISK-25525?focusedCommentId=228165&page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel#comment-228165) (not the docs)

> Usage rules:
* While marks the start of a while loop in the dialplan as well as the condition to keep executing the loop. For the While to terminate the loop, the loop's EndWhile must have been executed before. This implies that the while condition must have been true at least once.
* EndWhile marks the end of the while loop.
* ExitWhile breaks out of the while loop and jumps to the loop's EndWhile location. For ExitWhile to know where to go, the loop's EndWhile must have been executed before.
* ContinueWhile jumps to the current while loop's While location.

If breaking out of the middle of a loop, you MUST ensure the loop is properly exited or everything will turn to custard.

The following will work, because I've started at a number LOWER than what I want to start at, then have a GotoIf to skip to the end, so that it has "seen" that loop once.

So for the following dialplan, if you press 1 as soon as you hear the numbers, it will work.

```
[while-loop-tester]
exten => s,1,Verbose(1,Context:${CONTEXT} Extension:${EXTEN} pathDepth:${pathDepth} scanPath:${scanPath})  
    same => n,Set(n=0)
    same => n,Set(chosenFolderNumber=0)  
    same => n,While($[${n} <= 9])
    same => n,GotoIf($[${n} < 1]?end); hacky workaround so while does 1 loop and knows where endwhile is
    same => n,BackGround(press-${n}&for&digits/${n})
    same => n(end),Set(n=${INC(n)})
    same => n,EndWhile()
    same => n(loop),WaitExten(3) ; this is where exten => _[1-8] will arrive back at after the ExitWhile
    same => n,Goto(s,1)    

exten => _[1-8],1,Set(chosenFolderNumber=${EXTEN})  
    same => n,Set(n=99) ; so that when we hit ExitWhile in the next line, the test for < 9 is now false
    same => n,ExitWhile() ; MUST do this to ensure we've broken out of loop
    same => n,Goto(s,1) ; this is needed to catch what happens if you press a second key during the WaitExten
```

But in this following bit of dialplan, if you press a number as soon as it starts speaking, it'll fail because it won't have "seen" the EndWhile()

```
[while-loop-tester-fail]
exten => s,1,Verbose(1,Context:${CONTEXT} Extension:${EXTEN} pathDepth:${pathDepth} scanPath:${scanPath})  
    same => n,Set(n=0)
    same => n,Set(chosenFolderNumber=0)  
    same => n,While($[${n} <= 9])
    same => n,BackGround(press-${n}&for&digits/${n})
    same => n(end),Set(n=${INC(n)})
    same => n,EndWhile()
    same => n(loop),WaitExten(3);
    same => n,Goto(s,1)    

exten => _[1-8],1,Set(chosenFolderNumber=${EXTEN})  
    same => n,Set(n=99)
    same => n,ExitWhile() ; MUST do this to ensure we've broken out of loop
    same => n,Goto(s,1) 
```

Doesn't matter if all that makes sense right now, just do it!

### Function REMAINDER does not give REMAINDER!

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

### FILE will not create a new file when appending, *unless* the line ending type is given

This doesn't work:

    same => n,SET(FILE(/tmp/${EPOCH},,,al)=${EPOCH})
    
This will work:

    same => n,SET(FILE(/tmp/${EPOCH},,,al,u)=${EPOCH})
    
FILE will read garbage into a variable. It's a [known bug](https://issues.asterisk.org/jira/browse/ASTERISK-26481)

The workaround is 

     same => n,Set(featurefile=/home/test/feature-1.txt)
     same => n,Set(unfilteredfeat2=${FILE(${featurefile},0,1,l,u)})
     same => n,Set(feature2=${SHIFT(unfilteredfeat2)})

### Channel "optimization" will ruin your channel settings!

See https://wiki.asterisk.org/wiki/display/AST/Local+Channel+Modifiers and https://wiki.asterisk.org/wiki/display/AST/Local+Channel+Optimization

tl;dr: add a /n at the end of the dial string, like exten => 4,1,Dial(Local/2@services**/n**)
