### 杂记--数据库查询

```
select * from hist_day_data where date = '2018-08-24' and  ma5 < ma10 * 0.9 and low > 15;
select * from hist_day_data where date = date_sub(curdate(), interval 1 day) and  ma5 < ma10 * 0.9 and low > 15;


select * from hist_day_data where date = '2018-09-03' and  volume < v_ma5 * 0.8 and v_ma5 < v_ma10 * 0.8 and low > 30;
select * from hist_day_data where date = date_sub(curdate(), interval 1 day) and  volume < v_ma5 * 0.8 and v_ma5 < v_ma10 * 0.8 and low > 30;
# 查询符合上市满2年
select * from hist_day_data where code in (select code from basics_data where timeToMarket < date_sub(curdate(), interval 2 year)) and date = date_sub(curdate(), interval 1 day) and volume < v_ma5 * 0.8 and v_ma5 < v_ma10 * 0.8 and low > 30;


select * from hist_60m_data where date = '2018-09-03 15:00:00' and  ma5 < ma10 * 0.95 and ma10 < ma20 * 0.95 and low > 10;
select * from hist_60m_data where date = concat(date_sub(curdate(), interval 1 day), ' 15:00:00') and ma5 < ma10 * 0.95 and ma10 < ma20 * 0.95 and low > 10;

# 查询符合上市满2年
mysql> select * from stock.hist_extend_day_data where code in (select code from stock.basics_data where timeToMarket < date_sub(curdate(), interval 2 year)) and date = '2019-05-09' and ma5 < ma60 * 0.9 and ma5 < ma250 * 0.9 and v_ma5 < v_ma10 * 0.9;

mysql> select * from hist_day_data where code = (select code from basics_data where timeToMarket < date_sub(curdate(), interval 2 year)) and date = date_sub(curdate(), interval 1 day) and volume < v_ma5 * 0.8 and v_ma5 < v_ma10 * 0.8 and low > 30;
ERROR 1242 (21000): Subquery returns more than 1 row
mysql> select * from hist_day_data where code in (select code from basics_data where timeToMarket < date_sub(curdate(), interval 2 year)) and date = date_sub(curdate(), interval 1 day) and volume < v_ma5 * 0.8 and v_ma5 < v_ma10 * 0.8 and low > 30;
+------------+--------+------+------+-------+-------+---------+--------------+----------+--------+--------+--------+---------+---------+---------+----------+
| date       | code   | open | high | close | low   | volume  | price_change | p_change | ma5    | ma10   | ma20   | v_ma5   | v_ma10  | v_ma20  | turnover |
+------------+--------+------+------+-------+-------+---------+--------------+----------+--------+--------+--------+---------+---------+---------+----------+
| 2018-12-06 | 000538 | 75.8 | 77.5 | 75.02 | 74.33 | 56694.7 |        -1.58 |    -2.06 | 75.476 | 73.804 | 73.574 | 71540.5 | 90911.7 | 66679.8 |        0 |
+------------+--------+------+------+-------+-------+---------+--------------+----------+--------+--------+--------+---------+---------+---------+----------+
1 row in set (0.31 sec)
```



