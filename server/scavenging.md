---
outputFileName: index.html
---

# Scavenging events

When you delete events or streams in Event Store, they aren't removed immediately. To permenantly delete these events you will need to run a 'scavenge' on your database.

A scavenge reclaims disk space by rewriting your database chunks, minus the events to delete, and then deleting the old chunks. Once a scavenge has run, you cannot recover any deleted events.

> [!NOTE]
> Scavenges only affect completed chunks, so deleted events in the current chunk will still be there after you run a scavenge.

## Starting a scavenge

Scavenges are not run automatically by Event Store. Our recommendation is that you set up a scheduled task, for example using cron or Windows Scheduler, to trigger a scavenge as often as you need.

You can start a scavenge by issuing an empty `POST` request to the HTTP API with the credentials of an `admin` or `ops` user:

```bash
curl -i -d {} -X POST http://localhost:2113/admin/scavenge -u "admin:changeit"
```

You can also start scavenges from the _Admin_ page of the Admin UI.

> [!TIP]
> Each node in a cluster has its own independent database. As such, when you run a scavenge, you will need to issue a scavenge request to each node.

## Stopping a scavenge

You stop a running scavenge operation by issuing a `DELETE` request to the HTTP API with the credentials of an `admin` or `ops` user and the ID of the scavenge you want to stop:

```bash
curl -i -X DELETE http://localhost:2113/admin/scavenge/{scavengeId} -u "admin:changeit"
```

You can also stop scavenges from the _Admin_ page of the Admin UI.

> [!TIP]
> Each node in a cluster has its own independent database. As such, when you run a scavenge, you will need to issue a scavenge request to each node.

## How often should you run a scavenge

This depends on the following:

-   How often you delete streams.
-   Depending on how you set `$maxAge`, `$maxCount` or `$tb` metadata on your streams.

## Scavenging while Event Store is online

It is safe to run a scavenge while Event Store is running and processing events, it is designed to be an online operation.

> [!WARNING]
> Scavenging increases the number of reads/writes made to disk, and it is not recommended to run it when your system is under heavy load.
