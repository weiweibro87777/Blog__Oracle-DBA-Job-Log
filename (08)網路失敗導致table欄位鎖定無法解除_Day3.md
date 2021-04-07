# 2021-04-07(三) 網路失敗導致table欄位鎖定無法解除   
   
-----   
> #### 6CS 2021/02/04 PM 04:20 發生單位在開單會跳出錯誤訊息   
   
ORA-01591:lock held by in-doubt distributed transaction 6.14.1702157   
   
發現單位網路斷斷續續，請資訊駐區將Forti（俗稱的Router）重啟後網路恢復穩定   
   
不過單位還是無法執行開單作業，以下為DB alert log   
Thread 1 advanced to log sequence 565717   
  Current log# 1 seq# 565717 mem# 0: D:\DATABASE\FILES\LOG1.ORA   
  Current log# 1 seq# 565717 mem# 1: F:\REDO\LOG1.ORA   
Thu Feb 04 15:15:00 2021   
Thread 1 advanced to log sequence 565718   
  Current log# 2 seq# 565718 mem# 0: D:\DATABASE\FILES\LOG2.ORA   
  Current log# 2 seq# 565718 mem# 1: F:\REDO\LOG2.ORA   
Thu Feb 04 16:11:01 2021                                               <------16:11發生問題   
Error 2068 trapped in 2PC on transaction 6.14.1702157. Cleaning up.    <------從這裡開始   
Error stack returned to user:   
ORA-02054: transaction 6.14.1702157 in-doubt   
ORA-02068: following severe error from ECARE                          <------寫ECARE   
ORA-03113: end-of-file on communication channel   
Thu Feb 04 16:11:01 2021   
DISTRIB TRAN F229.WORLD.a5084a31.6.14.1702157   
  is local tran 6.14.1702157 (hex=06.0e.19f90d)   
  insert pending prepared tran, scn=2272571478090 (hex=211.1fd0d04a)   
Thu Feb 04 16:11:01 2021   
Thread 1 advanced to log sequence 565719   
  Current log# 5 seq# 565719 mem# 0: D:\DATABASE\FILES\LOG5.ORA   
  Current log# 5 seq# 565719 mem# 1: F:\REDO\LOG5.ORA   
Thu Feb 04 16:15:14 2021   
Errors in file D:\ORANT\rdbms80\trace\f229RECO.TRC:   
ORA-02068: following severe error from ECARE   
ORA-03113: end-of-file on communication channel   
   
Thu Feb 04 16:20:50 2021   
Errors in file D:\ORANT\rdbms80\trace\f229RECO.TRC:   
ORA-02068: following severe error from ECARE   
ORA-03113: end-of-file on communication channel   
   
Thu Feb 04 16:23:53 2021   
Error 2068 trapped in 2PC on transaction 4.3.1697503. Cleaning up.   
Error stack returned to user:   
ORA-02050: transaction 4.3.1697503 rolled back, some remote DBs may be in-doubt   
ORA-02068: following severe error from APAS   
ORA-03113: end-of-file on communication channel   
Thu Feb 04 16:23:53 2021   
DISTRIB TRAN F229.WORLD.a5084a31.4.3.1697503   
  is local tran 4.3.1697503 (hex=04.03.19e6df)   
  insert pending collecting tran, scn=2272571543519 (hex=211.1fd1cfdf)   
Thu Feb 04 16:24:55 2021   
Errors in file D:\ORANT\rdbms80\trace\f229RECO.TRC:   
ORA-02068: following severe error from APAS   
ORA-03113: end-of-file on communication channel   
   
Thu Feb 04 17:01:54 2021   
Errors in file D:\ORANT\rdbms80\trace\ORA00044.TRC:   
   
針對ORA-01591作以下動作:   
   
rollback force '6.14.1702157'; 無效   
   
delete from dba_2pc_pending where local_tran_id = '6.14.1702157';   
   
delete from sys.pending_sessions$ where local_tran_id = '6.14.1702157';   
  
delete from sys.pending_sub_sessions$ where local_tran_id = '6.14.1702157';   
   
commit;   一樣無效   
   
將ecare相關交易的session刪除，無效。   
   
目前只要查詢工單 select * from  JFFMC6CS20204026 還有之後的單子，全都出現ORA-01591錯誤   
   
找不到解決方法，與前輩和主管討論後決定，明早將資料恢復到2021/02/04 16:10，再請單位補之後丟失的資料   
   
開始作業，先把DB shutdown後啟動至mount狀態   
   
執行基於時間點的資料恢復   
   
SVRMGR> recover database using backup controlfile until time '20210204 16:10:00'   
   
ORA-00279: change 2272557598125 generated at 02/03/20 23:00:49 needed for thread1   
ORA-00289: suggestion : F:\ARCHIVE\ARC565704.1   
ORA-00280: change 2272557598125 for thread 1 is in sequence #565704   
Specify log: {<RET>=suggested | filename | AUTO | CANCEL}   
auto   
Log applied.   
.   
.   
.   
.   
.   
恢復過程內容為自動產出，過多省略，流程為DB需要archive log以便恢復到20210204 16:10的狀態   
   
Media recovery complete.   
   
SVRMGR> alter database open;   
alter database open   
*   
ORA-01589: must use RESETLOGS or NORESETLOGS option for database open   
   
SVRMGR> alter database open resetlogs;   
Statement processed.   
SVRMGR> select status from v$instance;   
STATUS   
-- -- --   
OPEN   
1 row selected.   
   
復原完成   
   
查詢昨天被交易鎖定的資料   
SVRMGR> select * from srmjob10 where job_no='JFFMC6CS20204026';   
   
資料可正常select   
   
先做完整的資料庫備份後，協助單位補0204資料，要將OS系統時間調整到0204讓單位補資料   
   
呼....成功！   
   
-----
   
### 我是一名資料工程師 wei，我們下次見 see u everyone～
