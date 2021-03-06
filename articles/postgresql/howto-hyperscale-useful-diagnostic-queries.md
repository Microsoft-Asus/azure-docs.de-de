---
title: Hilfreiche Diagnoseabfragen mit Hyperscale (Citus) – Azure Database for PostgreSQL
description: Abfragen zu verteilten Daten und anderen Aspekten
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 90f8b74168f1b02647f14645aa4dc7a3dff8c2ba
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937479"
---
# <a name="useful-diagnostic-queries"></a>Hilfreiche Diagnoseabfragen

## <a name="finding-which-node-contains-data-for-a-specific-tenant"></a>Ermitteln, welcher Knoten Daten für einen bestimmten Mandanten enthält

In Anwendungsfällen mit mehreren Mandanten lässt sich ermitteln, welcher Workerknoten die Zeilen für einen bestimmten Mandanten enthält.  Hyperscale (Citus) gruppiert die Zeilen verteilter Tabellen in Shards und platziert jeden Shard auf einem Workerknoten in der Servergruppe. 

Nehmen wir einmal an, die Mandanten unserer Anwendung sind Stores, und wir möchten herausfinden, welcher Workerknoten die Daten für den Store mit der ID 4 enthält.  Anders gesagt: Wir möchten die Platzierung für den Shard ermitteln, der Zeilen enthält, deren Verteilungsspalte den Wert 4 aufweist:

``` postgresql
SELECT shardid, shardstate, shardlength, nodename, nodeport, placementid
  FROM pg_dist_placement AS placement,
       pg_dist_node AS node
 WHERE placement.groupid = node.groupid
   AND node.noderole = 'primary'
   AND shardid = (
     SELECT get_shard_id_for_distribution_column('stores', 4)
   );
```

Die Ausgabe enthält den Host und den Port der Workerdatenbank.

```
┌─────────┬────────────┬─────────────┬───────────┬──────────┬─────────────┐
│ shardid │ shardstate │ shardlength │ nodename  │ nodeport │ placementid │
├─────────┼────────────┼─────────────┼───────────┼──────────┼─────────────┤
│  102009 │          1 │           0 │ 10.0.0.16 │     5432 │           2 │
└─────────┴────────────┴─────────────┴───────────┴──────────┴─────────────┘
```

## <a name="finding-the-distribution-column-for-a-table"></a>Ermitteln der Verteilungsspalte für eine Tabelle

Jede verteilte Tabelle in Hyperscale (Citus) besitzt eine „Verteilungsspalte“. (Weitere Informationen finden Sie unter [Auswählen einer Verteilungsspalte](concepts-hyperscale-choose-distribution-column.md).) Es kann wichtig sein, zu wissen, um welche Spalte es sich handelt. Ein Beispiel: Beim Einbinden oder Filtern von Tabellen werden möglicherweise Fehlermeldungen mit Hinweisen wie „Fügen Sie der Verteilungsspalte einen Filter hinzu“ angezeigt.

Die `pg_dist_*`-Tabellen auf dem Koordinatorknoten enthalten diverse Metadaten über die verteilte Datenbank. Insbesondere enthält `pg_dist_partition` Informationen zur Verteilungsspalte für jede Tabelle. Sie können eine praktische Hilfsfunktion verwenden, um den Namen der Verteilungsspalte in den allgemeinen Details der Metadaten zu suchen. Hier sehen Sie Beispiel sowie die zugehörige Ausgabe:

``` postgresql
-- create example table

CREATE TABLE products (
  store_id bigint,
  product_id bigint,
  name text,
  price money,

  CONSTRAINT products_pkey PRIMARY KEY (store_id, product_id)
);

-- pick store_id as distribution column

SELECT create_distributed_table('products', 'store_id');

-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Beispielausgabe:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ store_id      │
└───────────────┘
```

## <a name="detecting-locks"></a>Erkennen von Sperren

Diese Abfrage wird auf allen Workerknoten ausgeführt und identifiziert Sperren, die Dauer ihrer Aufrechterhaltung sowie die problematischen Abfragen:

``` postgresql
SELECT run_command_on_workers($cmd$
  SELECT array_agg(
    blocked_statement || ' $ ' || cur_stmt_blocking_proc
    || ' $ ' || cnt::text || ' $ ' || age
  )
  FROM (
    SELECT blocked_activity.query    AS blocked_statement,
           blocking_activity.query   AS cur_stmt_blocking_proc,
           count(*)                  AS cnt,
           age(now(), min(blocked_activity.query_start)) AS "age"
    FROM pg_catalog.pg_locks         blocked_locks
    JOIN pg_catalog.pg_stat_activity blocked_activity
      ON blocked_activity.pid = blocked_locks.pid
    JOIN pg_catalog.pg_locks         blocking_locks
      ON blocking_locks.locktype = blocked_locks.locktype
     AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
     AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
     AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
     AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
     AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
     AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
     AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
     AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
     AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
     AND blocking_locks.pid != blocked_locks.pid
    JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
    WHERE NOT blocked_locks.GRANTED
     AND blocking_locks.GRANTED
    GROUP BY blocked_activity.query,
             blocking_activity.query
    ORDER BY 4
  ) a
$cmd$);
```

Beispielausgabe:

```
┌───────────────────────────────────────────────────────────────────────────────────┐
│                               run_command_on_workers                              │
├───────────────────────────────────────────────────────────────────────────────────┤
│ (10.0.0.16,5432,t,"")                                                             │
│ (10.0.0.20,5432,t,"{""update ads_102277 set name = 'new name' where id = 1; $ sel…│
│…ect * from ads_102277 where id = 1 for update; $ 1 $ 00:00:03.729519""}")         │
└───────────────────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-your-shards"></a>Abfragen der Größe von Shards

Diese Abfrage gibt die Größe jedes Shards der verteilten Tabelle `my_distributed_table` zurück:

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT json_build_object(
    'shard_name', '%1$s',
    'size',       pg_size_pretty(pg_table_size('%1$s'))
  );
$cmd$);
```

Beispielausgabe:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                                result                                 │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │ {"shard_name" : "my_distributed_table_102008", "size" : "2416 kB"}    │
│  102009 │ t       │ {"shard_name" : "my_distributed_table_102009", "size" : "3960 kB"}    │
│  102010 │ t       │ {"shard_name" : "my_distributed_table_102010", "size" : "1624 kB"}    │
│  102011 │ t       │ {"shard_name" : "my_distributed_table_102011", "size" : "4792 kB"}    │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-all-distributed-tables"></a>Abfragen der Größe aller verteilten Tabellen

Diese Abfrage ruft eine Liste mit allen Größen für jede verteilte Tabelle sowie die Größen der zugehörigen Indizes ab.

``` postgresql
SELECT
  tablename,
  pg_size_pretty(
    citus_total_relation_size(tablename::text)
  ) AS total_size
FROM pg_tables pt
JOIN pg_dist_partition pp
  ON pt.tablename = pp.logicalrelid::text
WHERE schemaname = 'public';
```

Beispielausgabe:

```
┌───────────────┬────────────┐
│   tablename   │ total_size │
├───────────────┼────────────┤
│ github_users  │ 39 MB      │
│ github_events │ 98 MB      │
└───────────────┴────────────┘
```

Beachten Sie, dass es weitere Hyperscale (Citus)-Funktionen für die Abfrage der Größe einer verteilten Tabelle gibt. Informationen dazu finden Sie unter [Ermitteln der Tabellengröße](howto-hyperscale-table-size.md).

## <a name="identifying-unused-indices"></a>Identifizieren nicht verwendeter Indizes

Die folgende Abfrage identifiziert nicht verwendete Indizes auf Workerknoten für eine bestimmte verteilte Tabelle (`my_distributed_table`).

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT array_agg(a) as infos
  FROM (
    SELECT (
      schemaname || '.' || relname || '##' || indexrelname || '##'
                 || pg_size_pretty(pg_relation_size(i.indexrelid))::text
                 || '##' || idx_scan::text
    ) AS a
    FROM  pg_stat_user_indexes ui
    JOIN  pg_index i
    ON    ui.indexrelid = i.indexrelid
    WHERE NOT indisunique
    AND   idx_scan < 50
    AND   pg_relation_size(relid) > 5 * 8192
    AND   (schemaname || '.' || relname)::regclass = '%s'::regclass
    ORDER BY
      pg_relation_size(i.indexrelid) / NULLIF(idx_scan, 0) DESC nulls first,
      pg_relation_size(i.indexrelid) DESC
  ) sub
$cmd$);
```

Beispielausgabe:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                            result                                     │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │                                                                       │
│  102009 │ t       │ {"public.my_distributed_table_102009##some_index_102009##28 MB##0"}   │
│  102010 │ t       │                                                                       │
│  102011 │ t       │                                                                       │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="monitoring-client-connection-count"></a>Überwachen der Anzahl von Clientverbindungen

Die folgende Abfrage zählt die auf dem Koordinatorknoten geöffneten Verbindungen und gruppiert sie anhand des Typs.

``` sql
SELECT state, count(*)
FROM pg_stat_activity
GROUP BY state;
```

Beispielausgabe:

```
┌────────┬───────┐
│ state  │ count │
├────────┼───────┤
│ active │     3 │
│ idle   │     3 │
│ ∅      │     6 │
└────────┴───────┘
```

## <a name="index-hit-rate"></a>Indextrefferquote

Diese Abfrage stellt die Indextrefferquote für alle Knoten bereit. Die Indextrefferquote ist ein nützlicher Wert, wenn Sie ermitteln möchten, wie häufig Indizes bei Abfragen verwendet werden:

``` postgresql
SELECT nodename, result as index_hit_rate
FROM run_command_on_workers($cmd$
  SELECT CASE sum(idx_blks_hit)
    WHEN 0 THEN 'NaN'::numeric
    ELSE to_char((sum(idx_blks_hit) - sum(idx_blks_read)) / sum(idx_blks_hit + idx_blks_read), '99.99')::numeric
    END AS ratio
  FROM pg_statio_user_indexes
$cmd$);
```

Beispielausgabe:

```
┌───────────┬────────────────┐
│ nodename  │ index_hit_rate │
├───────────┼────────────────┤
│ 10.0.0.16 │ 0.88           │
│ 10.0.0.20 │ 0.89           │
└───────────┴────────────────┘
```

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über andere [Systemtabellen](reference-hyperscale-metadata.md), die bei Diagnosen nützlich sein können.
