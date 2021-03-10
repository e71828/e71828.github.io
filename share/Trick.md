# Some Tricks uesful for school life 

## Reduce PDF size with Adobe
* print virtually with the lowest dpi and black-and-white mode
* add the bookmark on the correct pages
* split the PDF by the bookmark

## Disable adobe right-click context menu
```Powershell
cd 'C:\Program Files (x86)\Adobe\Acrobat DC\Acrobat Elements'
regsvr32.exe /u ContextMenuShim64.dll
regsvr32.exe /u ContextMenu64.dll
regsvr32.exe /u ContextMenu.dll
regsvr32.exe /u ContextMenu.dll
```
