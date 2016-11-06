
```
[comparisonTest]
; Shows how you can compare multiple variables

exten => s,1,Verbose(1,Comparator test)
    same => n,Set(a=1)
    same => n,Set(b=1)
    same => n,Set(c=0)
    same => n(loop),Set(result=${IF($[${a} & ${b}]?yes:no)})
    same => n,Verbose(1, Simple test result should be yes on both loops: ${result})
    same => n,Set(result=${IF($[${b} & ${c}]?yes:no)})
    same => n,Verbose(1, Simple test result should be no on first loop, yes on second: ${result})
    same => n,Set(result=${IF($[$["${a}"="1"] & $[0${b}=1]]?yes:no)})
    same => n,Verbose(1, complex test result, should be yes on both loops: ${result})
    same => n,GotoIf($[${a} & ${b} & ${c}]?done)
    same => n,Set(c=1)
    same => n,Goto(loop)
    same => n(done),Hangup()
    ```
