---
outputFileName: index.html
---

# Step 3 - Projections

[!include[<Getting Started Intro>](~/getting-started/_intro.md)]

When running a projection, you can choose whether the query should run and give you all results present or whether the query should continue running into the future finding new results as they happen and updating its result set.

## Setting up Projections

You enable projections with the command line argument `--run-projections`. For example, the command below enables all projection modes (system and user-defined):

> [!TIP]
> [Read this guide](~/server/command-line-arguments.md#projections-options) for all the possible parameter values.

#### [Windows](#tab/tabid-1)

```powershell
EventStore.ClusterNode.exe --run-projections=all --start-standard-projections=true
```

To disable them again, run:

```powershell
EventStore.ClusterNode.exe --run-projections=none
```

### [Linux](#tab/tabid-2)

Add `EVENTSTORE_RUN_PROJECTIONS=All` and `EVENTSTORE_START_STANDARD_PROJECTIONS=true` to your environment variables, or the _/etc/eventstore/eventstore.conf_ configuration file and start Event Store:

```bash
sudo systemctl start eventstore
```

To disable them again, change the values to `EVENTSTORE_RUN_PROJECTIONS=none`.

### [Docker](#tab/tabid-3)

The Event Store Docker image has projections enabled by default, but you need to enable standard projections:

```bash
docker run --name eventstore-node -it -p 2113:2113 -p 1113:1113 -e EVENTSTORE_RUN_PROJECTIONS=All -e EVENTSTORE_START_STANDARD_PROJECTIONS=true eventstore/eventstore
```

To disable them again:

```bash
docker run --name eventstore-node -it -p 2113:2113 -p 1113:1113 -e EVENTSTORE_RUN_PROJECTIONS=None eventstore/eventstore
```

* * *

You then see new tabs enabled in the Web admin UI with the 4 system projections in a `Stopped` state:

![Projections tab](~/images/projections-menu-item.png)

![Projections default state](~/images/projections-default.png)

You can also query the state of all projections using the HTTP API.

### [Request](#tab/tabid-4)

[!code-bash[getting-started-projections-get-all-request](~/code-examples/getting-started/list-all-projections.sh)]

### [Response](#tab/tabid-5)

The response is a list of all known projections and useful information about them.

[!code-json[getting-started-projections-get-all-response](~/code-examples/getting-started/list-all-projections.json)]

* * *

## Add Sample Data

Download the following files that contain sample data used throughout this step of the getting started guide.

-   [shoppingCart-b989fe21-9469-4017-8d71-9820b8dd1164.json](~/code-examples/getting-started/shoppingCart-b989fe21-9469-4017-8d71-9820b8dd1164.json)
-   [shoppingCart-b989fe21-9469-4017-8d71-9820b8dd1165.json](~/code-examples/getting-started/shoppingCart-b989fe21-9469-4017-8d71-9820b8dd1165.json)
-   [shoppingCart-b989fe21-9469-4017-8d71-9820b8dd1166.json](~/code-examples/getting-started/shoppingCart-b989fe21-9469-4017-8d71-9820b8dd1166.json)
-   [shoppingCart-b989fe21-9469-4017-8d71-9820b8dd1167.json](~/code-examples/getting-started/shoppingCart-b989fe21-9469-4017-8d71-9820b8dd1167.json)

Add the sample data to four different streams:

### [HTTP API](#tab/tabid-http-api)

[!code-bash[getting-started-projections-add-sample-data](~/code-examples/getting-started/add-sample-data.sh)]

### [.NET API](#tab/tabid-net-api)

[!code-csharp[getting-started-projections-add-sample-data](~/code-examples/dotnet-client/Program.cs?range=142-144,147-148)]

### [JVM Client](#tab/tabid-jvm-api)

[!code-java[getting-started-write-multiple](~/code-examples/jvm-client/WriteMultipleEventsExample.java?start=41&end=74)]

* * *

> [!NOTE]
> While it's not the fastest method, you can also use the web admin interface for creating the streams, and all the tasks in this step, as we saw in [step 1](~/getting-started/index.md).

## Writing your first projection

> [!TIP]
> [Read this guide](~/projections/user-defined-projections.md) to find out more about the user defined projection's API.

The projection counts the number of 'XBox One S's that customers added to their shopping carts.

A Projection starts with a selector, in this case `fromAll()`. Another possibility is `fromCategory({category})` which this step discusses later, but for now, `fromAll` should do.

The second part of a projection is a set of filters. There is a special filter called `$init` that sets up an initial state. You want to start a counter from 0 and each time Event Store observes an `ItemAdded` event for an 'Xbox One S,' increment the counter.

Here is the projection, you can download it as a file [here](~/code-examples/getting-started/xbox-one-s-counter.json):

[!code-json[getting-started-projections-projection](~/code-examples/getting-started/xbox-one-s-counter.json)]

You create a projection by calling the projection API and providing it with the definition of the projection. Here you decide how to run the projection, declaring that you want the projection to start from the beginning and keep running. You can create a projection using the web admin interface by opening the _Projections_ tab, clicking the _New Projection_ button and filling in the details of your projection.

![Creating a projection with the Event Store Web admin interface](~/assets/getting-started-create-projection.png)

And to use the HTTP or .NET API:

### [HTTP API](#tab/tabid-create-proj-bash)

[!code-bash[getting-started-projections-create-projection](~/code-examples/getting-started/create-projection.sh)]

> [!NEXT]
> [Read here](~/projections/api.md) for more information on creating projections with the HTTP API and the parameters available, or [our projections section](~/projections/index.md) for details on projection syntax.

### [.NET API](#tab/tabid-create-proj-net)

[!code-csharp[getting-started-projections-create-projection-dotnet](~/code-examples/dotnet-client/Program.cs?range=26-27,165)]

> [!NEXT]
> [Read here](~/dotnet-api/projections.md) for more information on creating projections with the .NET API and the parameters available, or [our projections section](~/projections/index.md) for details on projection syntax.

* * *

## Querying for the state of the projection

Now the projection is running, you can query the state of the projection. As this projection has a single state, query it with the following request:

### [HTTP API](#tab/tabid-8)

[!code-bash[getting-started-projections-query-state-request](~/code-examples/getting-started/query-state.sh)]

### [.NET API](#tab/tabid-get-state-dotnet)

[!code-csharp[getting-started-projections-add-sample-data](~/code-examples/dotnet-client/Program.cs?range=167-168)]

### [Response](#tab/tabid-9)

[!code-json[getting-started-projections-query-state-response](~/code-examples/getting-started/query-state.json)]

* * *

## Writing to Streams from Projections

The above gives you the correct result but requires you to poll for the state of a projection. What if you wanted Event Store to notify you about state updates via subscriptions?

### Output State

Update the projection to output the state to a stream by calling the `outputState()` method on the projection which by default produces a `$projections-{projection-name}-result` stream.

Below is the updated projection, you can download it as a file [here](~/code-examples/getting-started/xbox-one-s-counter-outputState.json):

[!code-json[getting-started-projections-output-state](~/code-examples/getting-started/xbox-one-s-counter-outputState.json)]

To update the projection, edit the projection definition in the web admin UI, or issue the following request:

### [HTTP API](#tab/tabid-update-proj-http)

[!code-bash[getting-started-projections-output-state-update](~/code-examples/getting-started/xbox-one-s-counter-outputState.sh)]

### [.NET API](#tab/tabid-update-proj-dotnet)

[!code-csharp[getting-started-projections-update](~/code-examples/dotnet-client/Program.cs?range=192)]

* * *

Then reset the projection you created above:

### [HTTP API](#tab/tabid-reset-http)

[!code-bash[getting-started-projections-reset-request](~/code-examples/getting-started/reset-projection.sh)]

### [.NET API](#tab/tabid-reset-dotnet)

[!code-csharp[getting-started-projections-reset](~/code-examples/dotnet-client/Program.cs?range=193-193)]

### [Response](#tab/tabid-11)

[!code-json[getting-started-projections-reset-response](~/code-examples/getting-started/reset-projection.json)]

* * *

You can now read the events in the result stream by issuing a read request.

### [HTTP API](#tab/tabid-read-stream-http)

[!code-bash[getting-started-projections-read-projection-events-request](~/code-examples/getting-started/read-projection-events.sh)]

### [.NET API](#tab/tabid-read-stream-dotnet)

[!code-csharp[getting-started-projections-state](~/code-examples/dotnet-client/Program.cs?range=195-198)]

### [Response](#tab/tabid-13)

[!code-json[getting-started-projections-read-projection-events-response](~/code-examples/getting-started/read-projection-events.json)]

* * *

## Configure Projection Properties

You can configure properties of the projection by updating values of the `options` object. For example, the following projection changes the name of the results stream, you can download it as a file [here](~/code-examples/getting-started/update-projection-options.json):

[!code-json[getting-started-projections-update-options](~/code-examples/getting-started/update-projection-options.json)]

Then send the update to the projection:

### [HTTP API](#tab/tabid-update-proj-config-http)

[!code-bash[getting-started-projections-config-update-request](~/code-examples/getting-started/update-projection-options.sh)]

### [.NET API](#tab/tabid-update-proj-config-dotnet)

[!code-csharp[getting-started-projections-config-update](~/code-examples/dotnet-client/Program.cs?range=218)]

* * *

> [!TIP]
> You can find all the options available in the [user defined projections guide](~/projections/user-defined-projections.md).

Now you can read the result as above, but use the new stream name:

### [HTTP API](#tab/tabid-read-projection-events-renamed-http)

[!code-bash[getting-started-projections-read-projection-events-renamed-request](~/code-examples/getting-started/read-projection-events-renamed.sh)]

### [.NET API](#tab/tabid-read-projection-events-renamed-dotnet)

[!code-csharp[getting-started-projections-state](~/code-examples/dotnet-client/Program.cs?range=220-223)]

* * *

## The Number of items per shopping cart

The example in this step so far relied on a global state for the projection, but what if you wanted a count of the number of items in the shopping cart per shopping cart.

Event Store has a built-in `$by_category` projection that lets you select events from a particular list of streams. Enable this projection with the following command.

### [HTTP API](#tab/tabid-enablebycategory-http)

[!code-bash[getting-started-projections-enable-by-category-request](~/code-examples/getting-started/enable-by-category.sh)]

### [.NET API](#tab/tabid-enablebycategory-dotnet)

[!code-csharp[getting-started-projections-enable](~/code-examples/dotnet-client/Program.cs?range=231)]

* * *

The projection links events from existing streams to new streams by splitting the stream name by a separator. You can configure the projection to specify the position of where to split the stream `id` and provide a separator.

By default, the category splits the stream `id` by a dash. The category is the first string.

| Stream Name        | Category                               |
| ------------------ | -------------------------------------- |
| shoppingCart-54    | shoppingCart                           |
| shoppingCart-v1-54 | shoppingCart                           |
| shoppingCart       | _No category as there is no separator_ |

You want to define a projection that produces a count per stream for a category, but the state needs to be per stream. To do so, use `$by_category` and it's `fromCategory` API method.

Below is the projection, you can download the file [here](~/code-examples/getting-started/shopping-cart-counter.json):

[!code-json[getting-started-projections-count-per-stream](~/code-examples/getting-started/shopping-cart-counter.json)]

Create the projection with the following request:

### [HTTP API](#tab/tabid-projections-count-per-stream-http)

[!code-bash[getting-started-projections-count-per-stream](~/code-examples/getting-started/shopping-cart-counter.sh)]

### [.NET API](#tab/tabid-projections-count-per-stream-dotnet)

[!code-csharp[getting-started-projections-count-per-stream](~/code-examples/dotnet-client/Program.cs?range=248)]

* * *

#### Querying for the state of the projection by partition

Querying for the state of the projection is different due to the partitioning of the projection. You have to specify the partition and the name of the stream.

### [HTTP API](#tab/tabid-read-partition-http)

[!code-bash[getting-started-projections-read-state-partition-request](~/code-examples/getting-started/read-state-partition.sh)]

### [.NET API](#tab/tabid-read-partition-dotnet)

[!code-csharp[getting-started-projections-read-state-partition-request](~/code-examples/dotnet-client/Program.cs?range=250-251)]

### [Response](#tab/tabid-15)

[!code-json[getting-started-projections-read-state-partition-response](~/code-examples/getting-started/read-state-partition.json)]

* * *

## Next Step

In this third part of our getting started guide you learned about projections. The next, and final part covers which API or SDK to use, and when.

-   [Step 4 - Which API or SDK](~/getting-started/which-api-sdk.md)
