# e71828.github.io
可怜无定河边骨，犹是春闺梦里人。


### Win10 schedule shutdown
`schtasks /Query /tn shutdown_on_time`

`schtasks /create /TN shutdown_on_time /TR "shutdown /s /t 60" /ST 23:00:00 /SC DAILY`

`schtasks /Delete /tn shutdown_on_time`
