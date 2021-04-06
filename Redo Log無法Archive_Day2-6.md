# 2021-04-06(二) Redo Log無法Archive      
   
-----   
> #### 新竹廠alert log出現錯誤訊息如下      
   
ORA-00255   
ORA-00312   
ORA-00334   
ORA-00354   
ORA-00353   
   
此狀況從無發生過，推測為DB開啟Archive log模式後產生的，因為OS的記憶體   
   
配置不佳導致Redo log file無法archive出來。
   
SVRMGR>connect internal/spmanager   
SVRMGR>startup pfile=D:\database\files\init.ora;   
此時會看到上面幾個錯誤訊息   
   
SVRMGR>select * from V$LOG  查看看目前錯誤的REDO LOG是哪一個GROUP#，今天查為GROUP 5有問題   
SVRMGR>alter database clear unarchived logfile group 5;   
SVRMGR>alter database open;   
SVRMGR>alter system switch logfile;  多做幾次試試看是否有問題   
   
測試DB，錯誤訊息已消失，仍需再觀察看看，若再次發生   
   
就要著手調整init.ora裡的參數或是OS的DB架構...   
   
-----
   
### 我是一名資料工程師 wei，我們下次見 see u everyone～
