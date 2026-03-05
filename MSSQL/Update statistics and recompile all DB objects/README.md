# Update statistics and recompile all DB objects

Following SQL script create a series of SQL commands for updating statistics on all objects in the selected database:
```sql
SELECT 
    'update statistics ['+RTRIM(t.TABLE_SCHEMA)+'].['+RTRIM(o.name)+']['+RTRIM(i.name)+']'
FROM 
    dbo.sysindexes AS i WITH (NOLOCK),
    INFORMATION_SCHEMA.TABLES AS t WITH (NOLOCK),
    sysobjects AS o WITH (NOLOCK),
    sysusers AS u WITH (NOLOCK)  
WHERE 
    i.id = o.id
    AND i.indid >= 1 
    AND	i.indid < 255 
    --AND     i.dpages > 0 
    AND o.xtype = 'U' 
    AND PERMISSIONS(o.id) != 0 
    AND u.uid = o.uid
    AND t.TABLE_NAME = o.name
    --AND datediff(day,STATS_DATE(i.id, i.indid),getdate()) > 14
ORDER BY u.name, o.name, i.name
```

Following SQL script create a series of SQL commands for recompiling all objects in the selected database:
```sql
SELECT 
    'exec dbo.sp_recompile ['+RTRIM(t.TABLE_SCHEMA)+'.'+RTRIM(o.name)+']'
FROM 
    sysobjects as o with (nolock),
    INFORMATION_SCHEMA.TABLES as t with (nolock)   
WHERE   
    o.xtype = 'U' 
    AND PERMISSIONS(o.id) != 0 
    AND t.TABLE_NAME = o.name
ORDER BY t.TABLE_NAME, o.name
```
