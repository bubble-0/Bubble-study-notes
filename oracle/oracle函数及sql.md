# oracle函数及特殊sql
## 简介
  本文主要记录oracle的一些函数及特殊的一些sql

### oracle删除表中重复记录只保留一条
```
-- 思路:分组查出多条记录的数据,然后删除,并保留rowid最小的记录
DELETE FROM AML_IO_TA_AGENCYINFO
 WHERE AGENCYNO IN (SELECT AGENCYNO
                      FROM AML_IO_TA_AGENCYINFO
                     GROUP BY AGENCYNO
                    HAVING COUNT(*) > 1)
   AND ROWID NOT IN (SELECT MIN(ROWID)
                       FROM AML_IO_TA_AGENCYINFO
                      GROUP BY AGENCYNO
                     HAVING COUNT(*) > 1)
```

