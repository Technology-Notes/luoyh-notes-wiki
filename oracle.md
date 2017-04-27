# oracle notes

> batch insert with one SQL

```
insert into a(id, col0, col1) 
select seq.nextval,t.col0,t.col1 from 
(
	select '00' col0, '01' col1 from dual
	union all
	select '10' col0, '11' col1 from dual
) t

```

> find locked session

```
// find session
select sess.sid, 
   sess.serial#, 
   lo.oracle_username, 
   lo.os_user_name, 
   ao.object_name, 
   lo.locked_mode 
   from v$locked_object lo, 
   dba_objects ao, 
   v$session sess 
where ao.object_id = lo.object_id and lo.session_id = sess.sid; 

// or
select * from v$session t1, v$locked_object t2 where t1.sid = t2.SESSION_ID; 

// kill locked, 158=SID,3601=SERIAL#
alter system kill session '158,3601';
```