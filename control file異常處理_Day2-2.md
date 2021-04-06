# 2021-04-06(二) control file故障   
   
-----   

> #### 單位來電反應，登入資料庫後無法使用，先查詢DB狀況   
   
VRMGR> connect internal/spmanager   
Connected.   
SVRMGR> select file#,status from v$datafile;   
select file#,status from v$datafile   
*   
ORA-01034: ORACLE not available   
SVRMGR> startup pfile=d:\database\files\init.ora   
ORACLE instance started.   
Total System Global Area                       1151671216 bytes   
Fixed Size                                          52144 bytes   
Variable Size                                   365109248 bytes   
Database Buffers                                786432000 bytes   
Redo Buffers                                        77824 bytes   
Database mounted.   
ORA-01122: database file 1 failed verification check   
ORA-01110: data file 1: 'D:\DATABASE\PUBLIC\SYS1.DBF'   
ORA-01207: file is more recent than controlfile - old controlfile   
   


-----
   
### 我是一名資料工程師 wei，我們下次見 see u everyone～