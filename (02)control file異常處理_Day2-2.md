# 2021-04-06(二) control file故障   
   
-----   
> #### 這一年來雖然沒有在更新github，但每次遇到的狀況我都會記錄在自己的電腦中
> #### 單位來電反應，登入資料庫後無法使用，先查詢DB狀況   
   
SVRMGR> connect internal/spmanager   
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
   
查詢錯誤編號，ORA-01122、ORA-01100、ORA-01207，可知道目前DB裡的資料比控制   
   
文件紀錄的狀態還要新，引起此狀況的原因很多，在查詢server的OS事件檢視發現DB有發   
   
生突然的斷電，導致DB重啟時出現錯誤，控制文件裡面包含了紀錄DB所有datafile、   
   
日誌文件...等內容，不過為何突然斷電會出現此錯誤呢，因為根據Oracle DB的運行期間   
   
，由於check point的發生會不斷的對控制文件進行更新，但是突然的斷電會導致當前的狀   
   
態沒有更新進去，再次重啟DB時當Oracle背景程序檢查控制文件是否和其他文件一致時，就   
   
會出現此錯誤。   
   
> #### 處理此問題就是重建DB的control file
   
解決這問題就是重建controlfile。   
   
先導出controlfile文件   
SVRMGR> shutdown immediate;   
ORA-01109: database not open   
Database dismounted.   
ORACLE instance shut down.   
SVRMGR> startup mount pfile=d:\database\files\init.ora   
ORACLE instance started.   
Total System Global Area                       1151671216 bytes   
Fixed Size                                          52144 bytes   
Variable Size                                   365109248 bytes   
Database Buffers                                786432000 bytes   
Redo Buffers                                        77824 bytes   
Database mounted.   
SVRMGR> alter database backup controlfile to trace;   
Statement processed.   

-----以下為導出的control file-----   
內容太多，就先省略   
   
導出的control file會在d:orant\rdbms80\trace裡面（待會會用到）   
   
接著把DB啟動到nomount狀態   
SVRMGR> startup nomount pfile=d:\database\files\init.ora   
ORACLE instance started.   
Total System Global Area                       1151671216 bytes   
Fixed Size                                          52144 bytes   
Variable Size                                   365109248 bytes   
Database Buffers                                786432000 bytes   
Redo Buffers                                        77824 bytes   

再來，把原本在DB以及另一顆硬碟的control file檔案rename（隨意rename即可）   
   
然後把剛剛導出的control file SQL用internal權限執行   
   
Statement processed.   
SVRMGR> recover database;   
Media recovery complete.   
SVRMGR> alter database open;   
Statement processed.   
   
經測試，DB重啟後可正常使用，但還別急著通知user   
   
完整備份資料庫後再通知

-----
   
### 我是一名資料工程師 wei，我們下次見 see u everyone～
