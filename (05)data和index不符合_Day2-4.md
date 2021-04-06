# 2021-04-06(二) data和index不符合      
   
-----   
> #### 單位回報跑報表出現錯誤訊息如下   
   
Execute SRM_CA609_SP_TEMP失敗   
ORA-00001:unique constraint(DBASRM.SRMEMPCS_UQIDX03) violated   
ORA-06512:at "DBASRM.SRMCA609_SP_TEMP",line 21   
ORA-06512:at line 1   
   
內容為constraint不一致，analyze檢查 SRMEMPCS   
   
SVRMGR> analyze table dbasrm.srmempcs validate structure cascade;   
analyze table dbasrm.srmempcs validate structure cascade   
*   
ORA-01499: table/index cross reference failure - see trace file   
   
trace內容顯示table與index資料不一致   
檢視 trace 資料   
    
*** SESSION ID:(25.234)   
Table/Index row count mismatch   
table 0 : index 36, 0              <---------------table資料0筆，但是index資料出現36筆   
Index root = tsn: 6 rdba: 0x05c1a1f0   
   
對於0x05c1a1f0這個位址，由於他是一個16進制的數字串，轉換為10進制為96575984（這裡困擾了我很久，查了stack overflow才知道...）   
   
SVRMGR> select dbms_utility.data_block_address_file(96575984)  "Rfile#",   
     2>        dbms_utility.data_block_address_block(96575984) "Block#"   
     3> from dual;   
   
Rfile#     Block#   
---------- ----------   
23         106992   
1 row selected.   
   
SVRMGR> select owner, segment_name, segment_type   
     2> from  dba_segments   
     3> where header_file = 23   
     4> and header_block = 106992;   
   
OWNER    SEGMENT_NAME          SEGMENT_TYPE   
-------- --------------------- -----------------   
DBASRM   SRMEMPCS_PK           INDEX   
1 row selected.   
   
將index SRMEMPCS_PK 移除重建。   
   
請單位會計重新執行，出現錯誤   
   
Execute SRM_CA609_SP_TEMP失敗   
ORA-04043: object DBASRM does not exist   
   
再將procedure DBASRM.SRMCA609_SP_TEMP 作 Compiler with Debug動作後恢復正常。   
（這裡也是困擾了我，在我嘗試要做這步驟前也是查了很多資料，詢問前輩後有共識後嘗試這樣做）   
   
經測試，DB重啟後可正常使用，但還別急著通知user   
   
完整備份資料庫後再通知
   
-----
   
### 我是一名資料工程師 wei，我們下次見 see u everyone～
