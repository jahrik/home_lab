## Raid 50, raid 10, and 2 hot spares
* 12 physical drives
```
mega_raid10.sh
#!/bin/bash

export E=`MegaCli -PDlist -a0 | grep -e '^Enclosure Device ID:' | cut -d":" -f2 | uniq`

MegaCli -CfgClr -aALL
MegaCli -CfgSpanAdd -R50 -Array0[$E:0,$E:1,$E:2] Array1[$E:3,$E:4,$E:5] WB RA Direct CachedBadBBU -a0
MegaCli -CfgSpanAdd -R10 -Array0[$E:6,$E:7] -Array1[$E:8,$E:9] -a0
MegaCli -PDHSP -Set -PhysDrv "[$E : 10]" -a0
MegaCli -PDHSP -Set -PhysDrv "[$E : 11]" -a0
```

Show what this looks like
```
MegaCli -LDInfo -Lall -aALL
                                     

Adapter 0 -- Virtual Drive Information:
Virtual Drive: 0 (Target Id: 0)
Name                :
RAID Level          : Primary-5, Secondary-0, RAID Level Qualifier-3
Size                : 10.914 TB
State               : Optimal
Strip Size          : 64 KB
Number Of Drives per span:3
Span Depth          : 2
Default Cache Policy: WriteBack, ReadAhead, Direct, Write Cache OK if Bad BBU
Current Cache Policy: WriteBack, ReadAhead, Direct, Write Cache OK if Bad BBU
Access Policy       : Read/Write
Disk Cache Policy   : Disk's Default
Encryption Type     : None


Virtual Drive: 1 (Target Id: 1)
Name                :
RAID Level          : Primary-1, Secondary-0, RAID Level Qualifier-0
Size                : 5.457 TB
State               : Optimal
Strip Size          : 64 KB
Number Of Drives per span:2
Span Depth          : 2
Default Cache Policy: WriteBack, ReadAdaptive, Direct, No Write Cache if Bad BBU
Current Cache Policy: WriteBack, ReadAdaptive, Direct, No Write Cache if Bad BBU
Access Policy       : Read/Write
Disk Cache Policy   : Disk's Default
Encryption Type     : None
```
