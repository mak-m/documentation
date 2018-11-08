---
outputFileName: index.html
sinceVersion: 4.0.2
---

# Configuring Projections

By changing these settings, you can lessen the amount of pressure projections put on an Event Store node or improve projection performance. You can change these settings on a case-by-case basis, and monitor potential improvements.

## Emit options

<!-- TODO: Is this clear? -->

These options control how projections write events.

In busy systems, projections can put a lot of extra pressure on the master node. This is especially true for Event Store servers that also have persistent subscriptions running, which only the master node can process. If you see a lot of commit timeouts and slow writes from your projections and other clients, then start with these settings.

### Emit Enabled

This setting determines whether a projection can emit events and any projection that calls `emit()` or `linkTo()` requires it. If this option is not set and a projection attempts to emit events, you see an error message like the following:

```bash
'emit' is not allowed by the projection/configuration/mode
```

Event Store disables this setting by default, and is usually set when you create the projection and if you need the projection to emit events.

### Track Emitted Streams

This setting enables tracking of a projection's emitted streams. It only has an affect if the projection has `EmitEnabled` enabled.

Tracking emitted streams enables you to delete a projection and all the streams that it has created. You should only the setting if you intend to delete a projection and create new ones that project to the same stream.

Event Store disables this setting by default, when enabled, an event written records the stream name of each event emitted by the projection. This means that write amplification is a possibility, as each event that the projection emits writes a separate event. As such, this option is not recommended for projections that emit a lot of events, and you should enable only where necessary.

The stream that tracks emitted stream IDs is `$projections-{projection_name}-emittedstreams`.

> [!TIP]
> Between Event Store versions 3.8.0 and 4.0.2, this option was enabled by default when a projection was created through the UI. If you have any projections created during this time frame, it's worth checking whether this option is enabled.

### Max Allowed Writes in Flight

This sets the maximum number of writes to allow for a projection. Because a projection can write to multiple different streams, it's possible for the projection to send multiple writes at the same time. This option sets the number of concurrent writes that a projection can perform.

By default, projections try to perform writes as quickly as they come. This can add a lot of pressure to a node, especially for projections that emit to many different streams. If you see your projections causing frequent commit timeouts or slow reads, you can try lowering this value to see if there is any improvement.

> [!NOTE]
> Lower values may cause the projection to slow down as the number of writes are throttled, but the trade off for this is cleaner logs and fewer commit timeouts.

By default, this is unbounded, allowing a projection to write as fast as it can.

### Max Write Batch Length

This determines the maximum number of events the projection can write in a batch at a time.
The default for this option is 500.

## Checkpoint options

Checkpoints store how far along a projection is in the streams it is processing from.
There is a bit of performance overhead with writing a checkpoint, as it does more than simply write an event. So writing them too often can slow projections down.
However, we recommend you try other methods of improving projections before changing these values, as checkpoints are an important part of running projections.

### Checkpoint After Ms

This prevents a new checkpoint from being written within a certain time frame from the previous one.
The aim of this option is to keep a projection from writing too many checkpoints too quickly - something that can happen in a very busy system.

By default, this is set to 0 seconds, which means there is no limit to how quickly checkpoints can be written.

### Checkpoint Handled Threshold

This controls the number of events that a projection can handle before attempting to write a checkpoint.
An event is only considered handled if it actually passed through the projection's filter. As such, if the projection is set to checkpoint every 4,000 events, but it only reads from the `foo` stream, the projection will only checkpoint every 4,000 `foo` events.

The default for this option is 4,000 events.

### Checkpoint Unhandled Bytes Threshold

This specifies the number of bytes a projection can process before attempting to write a checkpoint.
Unhandled bytes are the events that are not processed by the projection itself. For example, if the projection only reads from the `foo` stream, but only writes from the `bar` stream comes through, a checkpoint will be written after this number of bytes have been processed. This is to prevent the projection from having to read through a potentially large number of unrelated events again because none of them actually passed its filter.

This option defaults to 10mb.

## Processing options

### Pending Events Threshold

This determines the number of events that can be pending before the projection is paused.
Pausing the projection stops the projection from reading, allowing it to finish with the current events that are waiting to be processed. Once the pending queue has drained to half the threshold, the projection will start reading again.

The default is 5000.

## Setting these config options

These options are accesible through the admin UI from the `Config` tab when editing a projection.

> [!NOTE]
> The config of a projection can only be changed when the projection has been stopped.
