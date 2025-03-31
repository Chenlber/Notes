

```
jmeter -n -t tuikuan_jieji.jmx  -l tuikuan_jieji.jtl -e -o ./tuikuan_jieji



```



```
jmeter -n -t 兴业银行造数.jmx  -l 兴业银行造数.jtl

```







```
/data/apache-jmeter-5.6/bin/jmeter -n -t mchtUpdate_all_use_hai.jmx  -l mchtUpdate_all_use_hai.jtl -e -o ./pay_ruzhang


```



```
/data/apache-jmeter-5.6/bin/jmeter -n -t rcc_insertH.jmx  -l rcc_insertH.jtl



```







```
explain analyze select Mcht_No, Mcht_Seq, Dir_Flag, Cust_Id, Mcht_Name, Deal_Name, Deal_Site, Mcc, Mcht_Type, Mcht_Blng_Dept, Mcht_Status, Forcd_Flag, Fee_Calc_Mode, Fee_Cycle, Fee_Cycle_Date, Fee_Pay_Amt, Mng_Brh_No, Mng_Brh_Name, Stlm_Brh_No, Stlm_Brh_Name, Risk_Flag, Gather_No, Dmst_Itnl_Flag, Acc_Mode, Stlm_Cycle, Stlm_Acc_Type, Stlm_Acc_No, Stlm_Acc_Name, Stlm_Awbk_No, Stlm_Awbk_Name, Inr_Acc_No, Inr_Acc_Name, Pay_Awbk_No, Cln_No, Cln_Flag, Stlm_Contract_Acc_No, Stlm_Contract_Acc_Name, Fee_Mode, Fee_Acc_Type, Fee_Contract_Acc_No, Fee_Acc_No, Fee_Acc_Name, Fee_Awbk_No, Fee_Awbk_Name, Onl_Refund_Swicth_Type, Onl_Refund_Acc_Type, Onl_Refund_Contract_Acc_No, Onl_Refund_Acc_No, Onl_Refund_Acc_Name, Onl_Refund_Awbk_No, Onl_Refund_Awbk_Name, Mrgn_Acc_Type, Mrgn_Acc_No, Mrgn_Acc_Name, Mrgn_Awbk_No, Mrgn_Awbk_Name, Cust_Docag_Tmlmt, Spd_Flag, Stlm_Mode, Morning_Stlm_Flag, Morning_End_Hour, Midday_Stlm_Flag, Midday_End_Hour, Nightfall_Stlm_Flag, Nightfall_End_Hour, Even_Stlm_Flag, Even_End_Hour, Day_Switch_Stlm_Flag, Big_Amt_Flag, Upd_Time, MblPh_No, Kik_ID, Dmst_Itnl_Idr_CD, ParmMnt_Tmlns_Rqs_Dys, Bid_Mrch_Ind, RlTmAccEntrMoeSwtcECD, Remark_1, Remark_2, Remark_3, Remark_Amt, Remark_5, PayInstId, case when OnlnRetGdsLgtTmlnsVal is null then 0 when OnlnRetGdsLgtTmlnsVal='' then 0 else OnlnRetGdsLgtTmlnsVal end, StoreAngOnlnRetGdsInd, Acq_GMrch_Ind, GMrch_ID, case when AvailQueryInterval is null then 31 when AvailQueryInterval='' then 31 else AvailQueryInterval end, White_Black_State, Sum_RefundAmt_Ctrl_DateTime, Day_Acm_Debit_Qot, Day_Acm_Credit_Qot, Mon_Acm_Debit_Qot, Mon_Acm_Credit_Qot, Chnl_Ctrl_Bmp, Tpc_Ctrl_Rule_No, Day_Acm_Qot, Mon_Acm_Qot, SMS_Rmndr_Ind from TBL_MCHT_INF where PayInstId="1000000000000" order by Mcht_No limit 1;

```



```
select Mcht_No, Mcht_Seq, Dir_Flag, Cust_Id, Mcht_Name, Deal_Name, Deal_Site, Mcc, Mcht_Type, Mcht_Blng_Dept, Mcht_Status, Forcd_Flag, Fee_Calc_Mode, Fee_Cycle, Fee_Cycle_Date, Fee_Pay_Amt, Mng_Brh_No, Mng_Brh_Name, Stlm_Brh_No, Stlm_Brh_Name, Risk_Flag, Gather_No, Dmst_Itnl_Flag, Acc_Mode, Stlm_Cycle, Stlm_Acc_Type, Stlm_Acc_No, Stlm_Acc_Name, Stlm_Awbk_No, Stlm_Awbk_Name, Inr_Acc_No, Inr_Acc_Name, Pay_Awbk_No, Cln_No, Cln_Flag, Stlm_Contract_Acc_No, Stlm_Contract_Acc_Name, Fee_Mode, Fee_Acc_Type, Fee_Contract_Acc_No, Fee_Acc_No, Fee_Acc_Name, Fee_Awbk_No, Fee_Awbk_Name, Onl_Refund_Swicth_Type, Onl_Refund_Acc_Type, Onl_Refund_Contract_Acc_No, Onl_Refund_Acc_No, Onl_Refund_Acc_Name, Onl_Refund_Awbk_No, Onl_Refund_Awbk_Name, Mrgn_Acc_Type, Mrgn_Acc_No, Mrgn_Acc_Name, Mrgn_Awbk_No, Mrgn_Awbk_Name, Cust_Docag_Tmlmt, Spd_Flag, Stlm_Mode, Morning_Stlm_Flag, Morning_End_Hour, Midday_Stlm_Flag, Midday_End_Hour, Nightfall_Stlm_Flag, Nightfall_End_Hour, Even_Stlm_Flag, Even_End_Hour, Day_Switch_Stlm_Flag, Big_Amt_Flag, Upd_Time, MblPh_No, Kik_ID, Dmst_Itnl_Idr_CD, ParmMnt_Tmlns_Rqs_Dys, Bid_Mrch_Ind, RlTmAccEntrMoeSwtcECD, Remark_1, Remark_2, Remark_3, Remark_Amt, Remark_5, PayInstId, case when OnlnRetGdsLgtTmlnsVal is null then 0 when OnlnRetGdsLgtTmlnsVal='' then 0 else OnlnRetGdsLgtTmlnsVal end, StoreAngOnlnRetGdsInd, Acq_GMrch_Ind, GMrch_ID, case when AvailQueryInterval is null then 31 when AvailQueryInterval='' then 31 else AvailQueryInterval end, White_Black_State, Sum_RefundAmt_Ctrl_DateTime, Day_Acm_Debit_Qot, Day_Acm_Credit_Qot, Mon_Acm_Debit_Qot, Mon_Acm_Credit_Qot, Chnl_Ctrl_Bmp, Tpc_Ctrl_Rule_No, Day_Acm_Qot, Mon_Acm_Qot, SMS_Rmndr_Ind from TBL_MCHT_INF where PayInstId="11" order by Mcht_No limit 1;
```



```
date
nohup jmeter -n -t lianjijiaoyi_3p_2h_200th.jmx  -l lianjijiaoyi_3p_2h_200th.jtl -e -o ./lianjijiaoyi_3p_2h_200th
date
nohup jmeter -n -t lianjijiaoyi_3p_3h_200th.jmx  -l lianjijiaoyi_3p_3h_200th.jtl -e -o ./lianjijiaoyi_3p_3h_200th
date
nohup jmeter -n -t lianjijiaoyi_3p_4h_200th.jmx  -l lianjijiaoyi_3p_4h_200th.jtl -e -o ./lianjijiaoyi_3p_4h_200th
date

```



去掉select 的性能



联机交易的语句给river





## 定时执行

```
echo "sh /data/lukatai/sh/test.sh >> /data/lukatai/sh/log.log 2>&1" | at now + 1 minute
```







## 后台且排序执行



















![Screenshot_2024_0824_084955](/Users/lukatai/Library/Containers/com.tencent.WeWorkMac/Data/Documents/Profiles/056169B87C4ABC8A79619DD48656B6B0/Caches/Images/2024-08/dbcf265c6e9c782d3368b40795928a45/Screenshot_2024_0824_084955.png)







```
语句比例

一致性

死锁




```









![image-20240905134723125](/Users/lukatai/Library/Application Support/typora-user-images/image-20240905134723125.png)



![image-20240905134902428](/Users/lukatai/Library/Application Support/typora-user-images/image-20240905134902428.png)











