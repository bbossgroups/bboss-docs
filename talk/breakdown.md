### mysql 执行查询导致服务器崩溃求解：Some pointers may be invalid and cause the dump to abort

120720  9:25:25  InnoDB: Assertion failure in thread 1175046464 in file row/row0mysql.c line 1534

InnoDB: Failing assertion: index->type & DICT_CLUSTERED

InnoDB: We intentionally generate a memory trap.

InnoDB: Submit a detailed bug report to http://bugs.mysql.com.

InnoDB: If you get repeated assertion failures or crashes, even

InnoDB: immediately after the mysqld startup, there may be

InnoDB: corruption in the InnoDB tablespace. Please refer to

InnoDB: http://dev.mysql.com/doc/refman/5.1/en/forcing-recovery.html

InnoDB: about forcing recovery.

InnoDB: Thread 1103137088 stopped in file handler/ha_innodb.cc line 4583

120720  9:25:25 - mysqld got signal 11 ;

This could be because you hit a bug. It is also possible that this binary or one of the libraries it was linked against is corrupt, improperly built, or misconfigured. This error can also be caused by malfunctioning hardware. We will try our best to scrape up some info that will hope fully help diagnose the problem, but since we have already crashed, something is definitely wrong and this may fail.

InnoDB: Thread 1170630976 stopped in file ../../storage/innobase/include/sync0sync.ic line 115

key_buffer_size=33554432

read_buffer_size=2097152

max_used_connections=180

max_threads=600

threads_connected=177

It is possible that mysqld could use up to

key_buffer_size + (read_buffer_size + sort_buffer_size)*max_threads = 6182880 K

bytes of memory

Hope that's ok; if not, decrease some variables in the equation.

thd: 0xf60ed30

Attempting backtrace. You can use the following information to find out where mysqld died. If you see no messages after this, something wentterribly wrong...

stack_bottom = 0x4609c118 thread_stack 0x30000

InnoDB: Thread 1167018304 stopped in file os/os0sync.c line 588

/data/mysql/bin/mysqld(my_print_stacktrace+0x20) [0xa05744]

/data/mysql/bin/mysqld(handle_segfault+0x368) [0x60074c]

/lib64/libpthread.so.0 [0x3c8cc0eb10]

/data/mysql/bin/mysqld(row_unlock_for_mysql+0x11e) [0x915112]

/data/mysql/bin/mysqld(ha_innobase::unlock_row()+0x57) [0x83550f]

/data/mysql/bin/mysqld [0x68e01c]

/data/mysql/bin/mysqld(sub_select(JOIN*, st_join_table*, bool)+0xae) [0x68dcaa]

/data/mysql/bin/mysqld [0x68d80f]

/data/mysql/bin/mysqld(JOIN::exec()+0x1990) [0x6881e0]

/data/mysql/bin/mysqld(subselect_single_select_engine::exec()+0x26d) [0x59e4c1]

/data/mysql/bin/mysqld(Item_subselect::exec()+0x42) [0x59dc12]

/data/mysql/bin/mysqld(Item_singlerow_subselect::val_int()+0xb5) [0x59ed8f]

/data/mysql/bin/mysqld(Item::save_in_field(Field*, bool)+0x275) [0x507fb7]*

/data/mysql/bin/mysqld(Item::save_in_field_no_warnings(Field*, bool)+0x60) [0x52cc8c]

/data/mysql/bin/mysqld [0x72a4ab]

/data/mysql/bin/mysqld [0x725901]

/data/mysql/bin/mysqld [0x72617c]

/data/mysql/bin/mysqld(SQL_SELECT::test_quick_select(THD*, Bitmap<64u>, unsigned long long, unsigned long long, bool)+0x6aa) [0x724144]

/data/mysql/bin/mysqld [0x675934]

/data/mysql/bin/mysqld(JOIN::optimize()+0x44e) [0x66e734]

/data/mysql/bin/mysqld(mysql_select(THD*, Item***, TABLE_LIST*, unsigned int, List(Item)&, Item*, unsigned int, st_order*, st_order*, Item*, st_order*, unsigned long long, select_result*, st_select_lex_unit*, st_select_lex*)+0x98) [0x66c734]

/data/mysql/bin/mysqld(handle_select(THD*, st_lex*, select_result*, unsigned long)+0x16c) [0x695220]*

/data/mysql/bin/mysqld [0x616ad8]

/data/mysql/bin/mysqld(mysql_execute_command(THD*)+0x4b00) [0x610c30]*

/data/mysql/bin/mysqld(mysql_parse(THD*, char const*, unsigned int, char const**)+0x20a) [0x61706e]*

/data/mysql/bin/mysqld(dispatch_command(enum_server_command, THD*, char*, unsigned int)+0x133f) [0x60b5d9]

/data/mysql/bin/mysqld(do_command(THD*)+0x114) [0x60a296]

/data/mysql/bin/mysqld(handle_one_connection+0xd20) [0x60564c]

/lib64/libpthread.so.0 [0x3c8cc0673d]

/lib64/libc.so.6(clone+0x6d) [0x3c8c0d3d1d]

Trying to get some variables.

Some pointers may be invalid and cause the dump to abort...

thd->query at 0xff36150 = SELECT DISTINCT g.USER_ID FROM SYS_USER_GROUP g  LEFT JOIN 

SYS_USER u on g.USER_ID=u.USER_ID  WHERE GROUP_ID = (SELECT GROUP_ID  FROM 

SYS_GROUP WHERE GROUP_CODE='ZB_JIHUAHUAN_GROUP') AND u.org_id = 2

thd->thread_id=209

thd->killed=NOT_KILLED

The manual page at http://dev.mysql.com/doc/mysql/en/crashing.html contains information that should help you find out what is causing the crash.

120720 09:25:26 mysqld_safe Number of processes running now: 0

120720 09:25:26 mysqld_safe mysqld restarted

InnoDB: Log scan progressed past the checkpoint lsn 124 4150467491

120720  9:25:27  InnoDB: Database was not shut down normally!

InnoDB: Starting crash recovery.

InnoDB: Reading tablespace information from the .ibd files...

InnoDB: Restoring possible half-written data pages from the doublewrite

InnoDB: buffer...

InnoDB: Doing recovery: scanned up to log sequence number 124 4151885387

120720  9:25:27  InnoDB: Error: table 'tmp/#sql3712_1558c_2'

InnoDB: in InnoDB data dictionary has tablespace id 4219,

InnoDB: but tablespace with that id or name does not exist. Have

InnoDB: you deleted or moved .ibd files?

InnoDB: This may also be a table created with CREATE TEMPORARY TABLE

InnoDB: whose .ibd and .frm files MySQL automatically removed, but the

InnoDB: table still exists in the InnoDB internal data dictionary.

InnoDB: Please refer to

InnoDB: http://dev.mysql.com/doc/refman/5.1/en/innodb-troubleshooting.html

InnoDB: for how to resolve the issue.

120720  9:25:27  InnoDB: Starting an apply batch of log records to the database...

InnoDB: Progress in percents: 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86 87 88 89 90 91 92 93 94 95 96 97 98 99

InnoDB: Apply batch completed

InnoDB: Last MySQL binlog file position 0 18981718, file name ./mysql-bin.006479

120720  9:25:33  InnoDB: Started; log sequence number 124 4151885387

120720  9:25:33 [Note] Recovering after a crash using mysql-bin

120720  9:25:33 [Note] Starting crash recovery...

120720  9:25:33 [Note] Crash recovery finished.

120720  9:25:33 [Note] Event Scheduler: Loaded 0 events

120720  9:25:33 [Note] /data/mysql/bin/mysqld: ready for connections.

Version: '5.1.35-log'  socket: '/tmp/mysql.sock'  port: 3306  MySQL Community Server (GPL)