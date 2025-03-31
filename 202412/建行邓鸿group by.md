```
nohup ./cancel_stress2_river -s ccb_break_connection_groupby -S select txn_num, card_attr, scn_lrd, count(1), sum(cast(ifnull(PnAmt, Txn_Amt) as decimal(16, 2))), sum(cast(ifnull(Stlm_Amt, '0') as decimal(16, 2))),  sum(cast(ifnull(Mcht_Fee, '0') as decimal(16, 2))), sum(cast(ifnull(Ahn_TxnAmt, PnAmt) as decimal(16, 2))) from tbl_ot_jnl_c_202_20001_0 FORCE INDEX(IDX_OT_C_202_20001_0_7) where Mcht_No = '10000000000' and sys_date_time >= '20200702080000' and sys_date_time <= '20200710190000' and sys_date >= '20200702' and sys_date <= '20200710' and rev_flag <> '1' and 1=1 and Txn_Status='01' and Txn_Num in ('100112005' , '100110020' , '100110040' , '100110070' , '100110090' , '100110100' , '100110130' , '100110160', '100110170', '100110180', '100110190', '100110280', '100110710', '100110750', '100110800', '100110820') group by txn_num, card_attr, scn_lrd; -w 0 -t 12 &

nohup sh ccb_conn_break.sh &
```





