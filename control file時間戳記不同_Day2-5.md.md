# 2021-04-06(二) control file時間戳記不同      
   
-----   
> #### 豐原廠4FY無法使用DB，連線資料庫查詢   
   
C:\svrmgr30   
   
Oracle Server Manager Release 3.0.6.0.0 - Production   
   
(c) Copyright 1999, Oracle Corporation.  All Rights Reserved.   
   
Oracle8 Release 8.0.6.0.0 - Production   
PL/SQL Release 8.0.6.0.0 - Production   
   
SVRMGR> connect internal/spmanager   
Connected.   
SVRMGR> select status from v$instance;   
select status from v$instance   
*   
ORA-01034: ORACLE not available   
   
資料庫未開啟，啟動資料庫   
   
SVRMGR> startup pfile=d:\database\files\init.ora   
ORACLE instance started.   
Total System Global Area                       1723583408 bytes   
Fixed Size                                          52144 bytes   
Variable Size                                   648912896 bytes   
Database Buffers                               1073561600 bytes   
Redo Buffers                                      1056768 bytes   
ORA-00214: controlfile 'D:\DATABASE\FILES\CTL1.ORA' version 101116606 inconsiste   
nt with file 'D:\DATABASE\FILES\CTL2.ORA' version 101116604   
   
開啟資料庫   
SVRMGR> startup pfile=d:\database\files\init.ora   
ORACLE instance started.   
Total System Global Area                       1723583408 bytes   
Fixed Size                                          52144 bytes   
Variable Size                                   648912896 bytes   
Database Buffers                               1073561600 bytes   
Redo Buffers                                      1056768 bytes   
Database mounted.   
ORA-01172: recovery of thread 1 stuck at block 361939 of file 2   
ORA-01151: use media recovery to recover block, restore backup if needed   
上面錯誤資訊描述檔案2需要做media recovery   
   
SVRMGR> select file#,status from v$datafile;   
FILE#      STATUS   
---------- -------   
         1 SYSTEM   
         2 RECOVERY   
         3 ONLINE   
         4 ONLINE   
         5 ONLINE   
         6 ONLINE   
         7 ONLINE   
         8 ONLINE   
         9 ONLINE   
        10 ONLINE   
        11 ONLINE   
        12 ONLINE   
        13 ONLINE   
        14 ONLINE   
        15 ONLINE   
        16 ONLINE   
        17 ONLINE   
        18 ONLINE   
        19 ONLINE   
        20 ONLINE   
        21 ONLINE   
        22 ONLINE   
        23 ONLINE   
        24 ONLINE   
        25 ONLINE   
        26 ONLINE   
        27 ONLINE   
        28 ONLINE   
        29 ONLINE   
        30 ONLINE   
        31 ONLINE   
        32 ONLINE   
        33 ONLINE   
        34 ONLINE   
        35 ONLINE   
        36 ONLINE   
        37 ONLINE   
        38 ONLINE   
        39 ONLINE   
        40 ONLINE   
        41 ONLINE   
        42 ONLINE   
        43 ONLINE   
        44 ONLINE   
        45 ONLINE   
        46 ONLINE   
        47 ONLINE   
        48 ONLINE   
        49 ONLINE   
        50 ONLINE   
        51 ONLINE   
        52 ONLINE   
        53 ONLINE   
        54 ONLINE   
        55 ONLINE   
        56 ONLINE   
        57 ONLINE   
        58 ONLINE   
        59 ONLINE   
   
59 rows selected.   
SVRMGR> recover datafile 2;   
Media recovery complete.   
SVRMGR> alter database datafile 2 online;   
Statement processed.   
SVRMGR> select status from v$instance;   
STATUS   
 --------   
MOUNTED   
1 row selected.   
SVRMGR> alter database open;   
Statement processed.   
SVRMGR> select status from v$instance;   
STATUS   
 --------   
OPEN   
1 row selected.   
經測試，已可正常操作   
   
經檢查系統日誌，發現日誌顯示下午 10:02 在 2016/1/13 之前發生意外關機。   

-----
   
### 我是一名資料工程師 wei，我們下次見 see u everyone～
