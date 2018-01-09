
以下列出的 case 是测试程序本身即有问题，

### engines/rr_trx

```
engines/rr_trx.rr_iud_rollback-multi-50
engines/rr_trx.rr_c_stats
engines/rr_trx.rr_replace_7-8
engines/rr_trx.rr_id_900
engines/rr_trx.rr_id_3
engines/rr_trx.rr_insert_select_2
engines/rr_trx.rr_c_count_not_zero
engines/rr_trx.rr_i_40-44
engines/rr_trx.rr_s_select-uncommitted
engines/rr_trx.rr_sc_sum_total
engines/rr_trx.rr_u_10-19_nolimit
engines/rr_trx.rr_sc_select-limit-nolimit_4
engines/rr_trx.rr_sc_select-same_2
engines/rr_trx.rr_u_10-19
engines/rr_trx.rr_u_4
engines/rr_trx.init_innodb
```
测试未正确的初始化，原版 MySQL 5.6.36 也全部失败。


### perfschema_stress

因测试未及时更新，当前版本的 performance_schema 数据库中表 events_waits_current、events_waits_history 的结构与测试预期不一致。

```
perfschema_stress.read
```

以下为错误输出：
```
 NAME	ENABLED	TIMED
 SELECT * FROM performance_schema.events_waits_current
 WHERE (TIMER_END - TIMER_START != TIMER_WAIT);
-THREAD_ID	EVENT_ID	EVENT_NAME	SOURCE	TIMER_START	TIMER_END	TIMER_WAIT	SPINS	OBJECT_SCHEMA	OBJECT_NAME	OBJECT_TYPE	OBJECT_INSTANCE_BEGIN	NESTING_EVENT_ID	OPERATION	NUMBER_OF_BYTESFLAGS
+THREAD_ID	EVENT_ID	END_EVENT_ID	EVENT_NAME	SOURCE	TIMER_START	TIMER_END	TIMER_WAIT	SPINS	OBJECT_SCHEMA	OBJECT_NAME	INDEX_NAME	OBJECT_TYPE	OBJECT_INSTANCE_BEGIN	NESTING_EVENT_ID	NESTING_EVENT_TYPE	OPERATION	NUMBER_OF_BYTES	FLAGS
 SELECT * FROM performance_schema.events_waits_history
 WHERE SPINS != NULL;
-THREAD_ID	EVENT_ID	EVENT_NAME	SOURCE	TIMER_START	TIMER_END	TIMER_WAIT	SPINS	OBJECT_SCHEMA	OBJECT_NAME	OBJECT_TYPE	OBJECT_INSTANCE_BEGIN	NESTING_EVENT_ID	OPERATION	NUMBER_OF_BYTESFLAGS
+THREAD_ID	EVENT_ID	END_EVENT_ID	EVENT_NAME	SOURCE	TIMER_START	TIMER_END	TIMER_WAIT	SPINS	OBJECT_SCHEMA	OBJECT_NAME	INDEX_NAME	OBJECT_TYPE	OBJECT_INSTANCE_BEGIN	NESTING_EVENT_ID	NESTING_EVENT_TYPE	OPERATION	NUMBER_OF_BYTES	FLAGS
 SELECT * FROM performance_schema.threads p,
 performance_schema.events_waits_current e
 WHERE p.THREAD_ID = e.THREAD_ID
 AND TIMER_START = 0
 ORDER BY e.EVENT_ID;
-THREAD_ID	NAME	TYPE	PROCESSLIST_ID	PROCESSLIST_USER	PROCESSLIST_HOST	PROCESSLIST_DB	PROCESSLIST_COMMAND	PROCESSLIST_TIME	PROCESSLIST_STATE	PROCESSLIST_INFO	PARENT_THREAD_ID	ROLE	INSTRUMENTED	THREAD_ID	EVENT_ID	EVENT_NAME	SOURCE	TIMER_START	TIMER_END	TIMER_WAIT	SPINS	OBJECT_SCHEMA	OBJECT_NAME	OBJECT_TYPE	OBJECT_INSTANCE_BEGIN	NESTING_EVENT_ID	OPERATION	NUMBER_OF_BYTES	FLAGS
+THREAD_ID	NAME	TYPE	PROCESSLIST_ID	PROCESSLIST_USER	PROCESSLIST_HOST	PROCESSLIST_DB	PROCESSLIST_COMMAND	PROCESSLIST_TIME	PROCESSLIST_STATE	PROCESSLIST_INFO	PARENT_THREAD_ID	ROLE	INSTRUMENTED	THREAD_ID	EVENT_ID	END_EVENT_ID	EVENT_NAME	SOURCE	TIMER_START	TIMER_END	TIMER_WAIT	SPINS	OBJECT_SCHEMA	OBJECT_NAME	INDEX_NAME	OBJECT_TYPE	OBJECT_INSTANCE_BEGIN	NESTING_EVENT_ID	NESTING_EVENT_TYPE	OPERATION	NUMBER_OF_BYTES	FLAGS
 SELECT * FROM performance_schema.events_waits_current
 WHERE THREAD_ID IN (SELECT THREAD_ID
 FROM performance_schema.threads
@@ -20,7 +20,7 @@
 AND TIMER_END = 0
 AND TIMER_WAIT != NULL
 ORDER BY EVENT_ID;
-THREAD_ID	EVENT_ID	EVENT_NAME	SOURCE	TIMER_START	TIMER_END	TIMER_WAIT	SPINS	OBJECT_SCHEMA	OBJECT_NAME	OBJECT_TYPE	OBJECT_INSTANCE_BEGIN	NESTING_EVENT_ID	OPERATION	NUMBER_OF_BYTESFLAGS
+THREAD_ID	EVENT_ID	END_EVENT_ID	EVENT_NAME	SOURCE	TIMER_START	TIMER_END	TIMER_WAIT	SPINS	OBJECT_SCHEMA	OBJECT_NAME	INDEX_NAME	OBJECT_TYPE	OBJECT_INSTANCE_BEGIN	NESTING_EVENT_ID	NESTING_EVENT_TYPE	OPERATION	NUMBER_OF_BYTES	FLAGS
 SELECT SUM(COUNT_READ) AS sum_count_read,
 SUM(COUNT_WRITE) AS sum_count_write,
 SUM(SUM_NUMBER_OF_BYTES_READ) AS sum_num_bytes_read,
```