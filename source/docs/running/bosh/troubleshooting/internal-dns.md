---
title: Inspecting and understanding the internal DNS
---

Each micro bosh, and optionally each full bosh, includes an internal DNS. Each job VM within a deployment has one or more DNS entries that all VMs within the deployment can use to reference it.

Consider a deployment of 4 jobs, each with one vm, where the `api/0` job has a public IP address:

<pre>
+-----------+---------+---------------+------------------------------+
| Job/index | State   | Resource Pool | IPs                          |
+-----------+---------+---------------+------------------------------+
| api/0     | running | medium        | 10.32.153.73, 54.225.102.129 |
| core/0    | running | medium        | 10.118.62.135                |
| dea/0     | running | small         | 10.201.218.167               |
| uaa/0     | running | small         | 10.70.135.244                |
+-----------+---------+---------------+------------------------------+
</pre>

The postgres database (whichever is used by powerdns, though is shared amongst all jobs within a micro bosh) includes a `records` table and would include the following records.

<pre>
bosh=> select * from records where type='A';
 id |            name             | type |    content     |  ttl  | prio | change_date | domain_id 
----+-----------------------------+------+----------------+-------+------+-------------+-----------
  3 | ns.microbosh                | A    |                | 14400 |      |             |         1
  4 | 0.core.default.cf.microbosh | A    | 10.118.62.135  |   300 |      |  1371499158 |         1
  8 | 0.uaa.default.cf.microbosh  | A    | 10.70.135.244  |   300 |      |  1371510835 |         1
 12 | 0.api.default.cf.microbosh  | A    | 10.32.153.73   |   300 |      |  1371510894 |         1
 16 | 0.api.floating.cf.microbosh | A    | 54.225.102.129 |   300 |      |  1371510894 |         1
 20 | 0.dea.default.cf.microbosh  | A    | 10.201.218.167 |   300 |      |  1371511006 |         1
(6 rows)
</pre>