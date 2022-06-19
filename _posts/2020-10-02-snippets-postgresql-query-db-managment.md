---
title: "Snippets - PostgreSQL DB Managment"
last_modified_at: 2020-10-02T21:07:24
categories:
  - Snippets
tags:
  - SQL
  - Database
toc: true
toc_sticky: true
toc_label: "Dans cette page..."
---


Un aide mémoire pour PostgreSQL...

<figure style="width: 0px; visibility: hidden;" class="">
  <a href="https://www.postgresql.org/media/img/about/press/elephant.png"><img src="https://www.postgresql.org/media/img/about/press/elephant.png"></a>
</figure>

# General

## Dump

Check : https://www.postgresql.org/docs/13/app-pgdump.html

```bash
pg_dump -U username -h localhost -W -F p db_name > D:\my_backup.sql
```


## Restore

Check : https://www.postgresql.org/docs/13/app-pgrestore.html

```bash
pg_restore -U username -h localhost -d db_name -1 -f D:\my_backup.sql
```

## Drop
```bash
dropdb mydb
```


# Database informations

## Database size 
```sql
SELECT
    pg_database.datname,
    pg_size_pretty(pg_database_size(pg_database.datname)) AS size
FROM pg_database;
```

## tables sizes 

```sql
SELECT 
	b.nspname as schema,
	a.relname as table,
	b.nspname || '.' || a.relname AS full_name,
	pg_size_pretty(pg_relation_size(a.oid)) AS "size",
	to_char(a.reltuples,'999G999G999') as nb_rows
FROM 
	pg_class a
LEFT JOIN 
	pg_namespace b 
ON
	b.oid = a.relnamespace
WHERE 
	nspname NOT IN ('pg_catalog', 'information_schema', 'pg_toast')
	--AND a.relhasindex is True -- to filter only on tables
ORDER BY 
	pg_relation_size(a.oid) DESC
```

# Lines Number on each tables

```sql
SELECT 
  nspname AS schema_name,
  relname as tabme_name,
  to_char(reltuples,'999G999G999D999') as rows_count
FROM pg_class C
LEFT JOIN 
    pg_namespace N ON (N.oid = C.relnamespace)
WHERE 
  nspname NOT IN ('pg_catalog', 'information_schema')
  AND relkind='r' 
ORDER BY reltuples DESC;
```

# Database maintenance

https://medium.com/swlh/maintaining-your-database-b9911167112f

## Check applied vacuum
```sql
SELECT 
    relname,
    last_vacuum,
    last_autovacuum,
    last_analyze,
    last_autoanalyze
FROM pg_stat_all_tables
WHERE 
    schemaname = 'public' 
    AND relname = 'MY_TABLE';
```

## Vaccum applying

To do after a table changes

MY_TABLE depends on the table name

```sql
vacuum full verbose MY_TABLE;
```

## To automatise Vacuum

MY_TABLE depends on the table name

```sql
/* Specifies the minimum number of inserted, updated or deleted tuples needed to trigger an ANALYZE in any one table. */
ALTER TABLE MY_TABLE SET (autovacuum_analyze_threshold = 100);

/* Specifies a fraction of the table size to add to autovacuum_analyze_threshold when deciding whether to trigger an ANALYZE. The default is 0.1 (10% of table size).  */ 
ALTER TABLE MY_TABLE SET (autovacuum_analyze_scale_factor = 0);

/*Specifies the minimum number of updated or deleted tuples needed to trigger a VACUUM in any one table. The default is 50 tuples. */
ALTER TABLE MY_TABLE SET (autovacuum_vacuum_threshold = 100);

/*Specifies a fraction of the table size to add to autovacuum_vacuum_threshold when deciding whether to trigger a VACUUM. The default is 0.2 (20% of table size). */
ALTER TABLE MY_TABLE SET (autovacuum_vacuum_scale_factor = 0.2);
```
