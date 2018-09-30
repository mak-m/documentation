---
outputFileName: index.html
---

# System Projections

Event Store ships with 4 built in projections.

-   By Category (`$by_category`)
-   By Event Type (`$by_event_type`)
-   Stream by Category (`$stream_by_category`)
-   Streams (`$streams`)

## Enabling System Projections

When you start Event Store from a fresh database, these projections are present but disabled and querying their statuses returns `Stopped`. You can enable a projection by issuing an HTTP request to _http://{event-store-ip}:{ext-http-port}/projection/{projection-name}/command/enable_. The status of the projection switches from `Stopped` to `Running`.

## By Category

The `$by_category` projection links existing events from streams to a new stream with a `$ce-` prefix (a category) by splitting a stream `id` by a configurable separator.

<!-- TODO: This is a little confusing, what is it? -->

```
$by_category\

first
-
```

You can configure the separator, as well as where to split the stream `id`. You can edit the projection and provide your own values if the defaults don't fit your particular scenario.

The first parameter specifies how the separator is used, and the possible values for that parameter is `first` or `last`. The separator can be any character.

For example, if the body of the projection is `first` and `-`, for a stream id of `account-1`, the resulting stream name from the projection is `$ce-account`.

If the body of the projection is last and `-`, for a stream id of `shopping-cart-1`, the resulting stream name from the projection is `$ce-shopping-cart`.

## By Event Type

The `$by_event_type` projection links existing events from streams to a new stream with a stream id in the format `$et-{event-type}`.

You cannot configure this projection.

## Stream By Category

The `$stream_by_category` projection links existing events from streams to a new stream with a `$category` prefix by splitting a stream `id` by a configurable separator.

<!-- TODO: Again, what is this? -->

`$stream_by_category`

```json
first
-
```

By default the `$stream_by_category` projection links existing events from a stream id with a name such as `account-1` to a stream called `$category-account`. You can configure the separator as well as where to split the stream `id`. You can edit the projection and provide your own values if the defaults don't fit your particular scenario.

The first parameter specifies how the separator is used and the possible values for that parameter is `first` or `last`. The separator can be any character.

For example, if the body of the projection is `first` and `-`, for a stream id of `account-1`, the resulting stream name from the projection is `$category-account`.

If the body of the projection is `last` and `-`, for a stream id of `shopping-cart-1`, the resulting stream name from the projection is `$category-shopping-cart`.

## Streams

The `$streams` projection links existing events from streams to a stream named `$streams`

You cannot configure this projection.