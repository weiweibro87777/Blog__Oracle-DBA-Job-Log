# 2021-04-06(二) control file故障   
   
-----   
> #### 板橋廠 2bc 跳電發生錯誤訊息   
   
Execute SRM_CA609_SP_TEMP 失敗   
code=8102   
ORA-08102:index key not found, obj# 33023, dba 163630631 (2)   
ORA-06512:at "DBASRM.SRM_CA609_SP_TEMP", line 19   
ORA-06512:at line 1   
   
看到此錯誤訊息，看起來是要將index重建   
   
以防萬一，先查詢此object是否真的為index   
SELECT owner, object_name, object_type   
FROM Dba_Objects   
WHERE object_id IN (33023);    
   
SVRMGR> SELECT owner, object_name, object_type   
     2> FROM Dba_Objects   
     3> WHERE object_id IN (33023);   
OWNER                          OBJECT_NAME          OBJECT_TYPE   
------------------------------ -------------------- -------------------   
DBASRM                         SRMEMPCS_PK          INDEX   
1 row selected.   
   
alter index srmempcs_pk rebuild;   
   
什麼！竟然無效，只能...砍掉index重新create囉！
   
經測試，drop index後重新create， DB可正常使用   
   
老樣子，先完整備份DB再通知user使用

-----
   
### 我是一名資料工程師 wei，我們下次見 see u everyone～