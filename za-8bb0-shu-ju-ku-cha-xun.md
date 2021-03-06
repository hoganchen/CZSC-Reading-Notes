### 杂记--数据库查询

```
# 查询符合均价条件
select * from hist_day_data where date = '2018-08-24' and  ma5 < ma10 * 0.9 and low > 15;
select * from hist_day_data where date = date_sub(curdate(), interval 1 day) and  ma5 < ma10 * 0.9 and low > 15;

# 查询符合成交量条件
select * from hist_day_data where date = '2018-09-03' and  volume < v_ma5 * 0.8 and v_ma5 < v_ma10 * 0.8 and low > 30;
select * from hist_day_data where date = date_sub(curdate(), interval 1 day) and  volume < v_ma5 * 0.8 and v_ma5 < v_ma10 * 0.8 and low > 30;

# 从60分钟线上查询
select * from hist_60m_data where date = '2018-09-03 15:00:00' and  ma5 < ma10 * 0.95 and ma10 < ma20 * 0.95 and low > 10;
select * from hist_60m_data where date = concat(date_sub(curdate(), interval 1 day), ' 15:00:00') and ma5 < ma10 * 0.95 and ma10 < ma20 * 0.95 and low > 10;

# 从周线上选股并验证
select code, date, close from stock.hist_day_data where code in (select code from stock.hist_extend_week_data where date = '2019-04-30' and close > ma5 * 1.2 and volume < v_ma5 * 0.8 and volume > v_ma60) and date = '2019-04-30' union select code, date, close from stock.hist_day_data where code in (select code from stock.hist_extend_week_data where date = '2019-04-30' and close > ma5 * 1.2 and volume < v_ma5 * 0.8 and volume > v_ma60) and date = '2019-05-17';
select code, date, close from stock.hist_day_data where code in (select code from stock.hist_extend_week_data where date = '2019-04-30' and close > ma5 * 1.1 and volume < v_ma5 * 0.8) and date = '2019-04-30' union select code, date, close from stock.hist_day_data where code in (select code from stock.hist_extend_week_data where date = '2019-04-30' and close > ma5 * 1.1 and volume < v_ma5 * 0.8) and date = '2019-05-17';
select code, date, close from stock.hist_day_data where code in (select code from stock.hist_extend_week_data where date = '2019-04-30' and close > ma5 * 1.1 and volume < v_ma5 * 0.8) and (date = '2019-04-30' or date = '2019-05-17');
select code, date, close from stock.hist_day_data where code in (select code from stock.hist_extend_week_data where date = '2019-04-30' and close > ma5 * 1.1 and close < v_ma250 and volume < v_ma5 * 0.6) and (date = '2019-05-17' or date = '2019-04-30');
select code, date, close from stock.hist_day_data where code in (select code from stock.hist_extend_week_data where date = '2019-04-30' and close > ma5 * 1.1 and close < v_ma250 and volume < v_ma5 * 0.6) and (date = date_add('2019-04-30', interval 1 week) or date = '2019-04-30');

# 查询符合上市满2年
select * from hist_day_data where code in (select code from basics_data where timeToMarket < date_sub(curdate(), interval 2 year)) and date = date_sub(curdate(), interval 1 day) and volume < v_ma5 * 0.8 and v_ma5 < v_ma10 * 0.8 and low > 30;

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

# 查询预计盈利预计增收中涨幅小的
select * from k_extend_day_data where date = date_sub(curdate(), interval 1 day) and 
  code in (select code from industry_hot_concept_data 
  where classified_str = 'chgn_700023') order by close / ma240;
  
# 查询最近4个季度相比去年增收20%的股票中，涨幅相比最低价涨幅较低的
select a.code, a.close / b.low_price_250days, a.close, b.high_price_250days, b.low_price_250days 
  from (select * from k_extend_day_data where date = date_sub(curdate(), interval 1 day)) as a, 
  (select code, high_price_250days, low_price_250days from financial_data where eps_last_four_quarter / eps_last_year > 1.2) as b 
  where a.code = b.code and a.ma240 > 0 and a.close / b.low_price_250days < 1.5 order by a.close / b.low_price_250days;

# 查询最近4个季度相比去年增收20%的股票中，涨幅相比最高价跌幅较高的
select a.code, a.close / b.high_price_250days, a.close, b.high_price_250days, b.low_price_250days 
  from (select * from k_extend_day_data where date = date_sub(curdate(), interval 1 day)) as a, 
  (select code, high_price_250days, low_price_250days from financial_data where eps_last_four_quarter / eps_last_year > 1.2) as b 
  where a.code = b.code and a.ma240 > 0 and a.close / b.high_price_250days < 0.7 order by a.close / b.high_price_250days;
```



