mysql-permute-indexes
=====================

**Summary**

Autogenerate all valid index statements for EXPLAIN to optimize complex queries automatically for MySQL, Postgres, Oracle and SQL Server.

Some use cases:

- accelerate existing Open Source (WordPress, Joomla, etc.) and commercial applications
- test database optimizer results
- stress-test databases with hundreds of index objects
- verify understanding of optimizer index selection  behavior by DBA
- and the most interesting to me, improve performance of infrastructure tools that use metadata stores (OpenStack, CloudStack, DynamoDB, Bacula, etc.) 

Typical performance improvements are 100x to 1,000x for multi-table joins, subselect and reporting queries.

**Example**

<pre>
$ vi permute_index.pl # (update user settings)

$ permute_index.pl | tee permute_index.txt
   alter table t1 add index idx_jb_001 (c1,c3);
   alter table t1 add index idx_jb_002 (c1,c2,c3);
   alter table t1 add index idx_jb_003 (c1,c3,c2);
   [...]
   alter table t2 add index idx_jb_007 (c4,c5);
   [...]

# load a backup or representative dataset to a dev system (do not run in production!)

$ mysql -h dev -u root -p test &lt;permute_index.txt

$ mysql -h dev -u root -p test
  mysql&gt; explain select * from t1, t2 where c1=c4 and c1=? and c2=? and c3=? and c5=?;
  Table | Key
  ------------------
  t1    | idx_jb_002
  t2    | idx_jb_007

(Now drop all the autogenerated indexes except idx_jb_002 and idx_jb_007.)
</pre>
