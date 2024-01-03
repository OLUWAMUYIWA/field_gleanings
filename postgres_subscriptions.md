### Subscriptions


A _subscription_ is downstream of a logical replication. For there to exist a subscription, there must be another database publishing. The node where the _subscription_ is defined is called the `subscriber`.
A `subscriber` behaves just like any other Postgres instance; it can even have its own subscribers. The subscription defines how it connects to the publisher.


Each `subscription` receives changes from its publisher via a replication slot. 
What replication slots prevent is the loss of data. When a subscriber subscribes to a publisher (primary) through a replication slot, the primary does not delete WAL segments until all the standbys (subscribers) have consumed it. 

A subscriber may have more than one publication it's subscribed to. It can even have multiple subscriptions to one publisher, but care must be taken to ensure that these do not overlap.

At the beginning of a subscription, the number of replication slots might be more than one to stream existing data, but once these preexisting data have been streamed, these additional replication slots are dropped, and only one is left to deliver changes from publisher to subscriber via the subscription.

When a subscription is dropped and recreated, the synchronization data is deleted, therefore the data has to be resynchronized again.

Schema definitions are not replicated, hence the subscriber must already contain the published tables. Only normal tables can be replicated. Views cannot.
Tables must be fully qualified in subscriptions. They must bear the same names.
Columns are fully  qualified too, but the order of the columns in the publisher does not have to match that of the subscriber. Columns types do not even need to match! What matters is that the types in the subscriber must be convertible from the text representation of the data in the publisher. E.g. an `integer` column in the publisher can be matched against a `bigint` in the subscription.

Normally, a replication slot is created on the publisher side automatically when a subscription is created on the subscriber side. Also, additional slots might be created at the beginning and dropped after initial changes have been delivered. Replication slots when the subscription is dropped too.
