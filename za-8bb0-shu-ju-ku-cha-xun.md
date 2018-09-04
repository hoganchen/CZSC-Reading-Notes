### 杂记--数据库查询

```
select * from hist_day_data where date = '2018-08-24' and  ma5 < ma10 * 0.9 and low > 15;

select * from hist_day_data where date = '2018-09-03' and  volume < v_ma5 * 0.8 and v_ma5 < v_ma10 * 0.8 and low > 30;

select * from hist_60m_data where date = '2018-09-03 15:00:00' and  ma5 < ma10 * 0.95 and ma10 < ma20 * 0.95 and low > 10;
```



