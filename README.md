range_repair.py
======================

A Python script to repair the primary range of a Cassandra node in N discrete steps [using best practices](http://www.datastax.com/dev/blog/advanced-repair-techniques).

### Background
When Cassandra begins the repair process it constructs a [merkle tree](http://en.wikipedia.org/wiki/Merkle_tree), which is a tree of hashes over segments of data that the node is responsible for. The node compares it's tree to that of the replicas, if there is a difference in the hash values for any of the nodes then the segment for that hash is requested from the replica and is re-inserted.

By default the Merkle tree for Cassandra represents 15 discreet segments, which means that the data on the node is broken into 15 pieces. If the data for one of these 15 pieces is different from that of the replicas then it will result in 1/15th of the data being streamed and re-inserted. This can cause problems for two use cases: dense nodes, and DSE Solr nodes running at or near capacity.

#### Solr Nodes
For DSE Solr nodes when the data transferred as the result of a repair is re-inserted it is also re-indexed because each host maintains it's own independent set of indexes. If the node is already at or near capacity then the additional strain caused by the repair/re-index can push it over the edge. As a last result Cassandra will shed load by dropping mutations. If a mutation is dropped the data will at some point need to be brought over from the replicas through the repair process which unfortunately begins a never ending cycle of re-index/repair.

#### Dense Nodes
For clusters that have a large amount of data per node the repair process could require an unacceptably large amount of data to be streamed and re-inserted.

### How the script works
The script works by figuring out the primary range for the node that it's being executed on, and instead of running repair on the entire range, run the repair on only a smaller sub-range. When a repair is initiated on a sub-range Cassandra constructs a merkle tree only for the range specified, which in turn divides the much smaller range into 15 segments. If there is disagreement in any of the hash values then a much smaller portion of data needs to be transferred which lessens load on the system.

### The Future
The functionality provided in this script should be integrated into [DataStax OpsCenter](http://www.datastax.com/what-we-offer/products-services/datastax-opscenter) version 3.2.2. The automation and scheduling provided by OpsCenter is superior to that of this script, this script should only be considered a stopgap until the OpsCenter release is available.

### Options

```
Usage: range_repair.py [options]

Options:
  -h, --help            show this help message and exit
  -k KEYSPACE, --keyspace=KEYSPACE
                        keyspace to repair
  -s STEPS, --steps=STEPS
                        number of discrete ranges
  -q, --quiet           don't print status messages to stdout
```

### Sample

```
⇒ ./range_repair.py -k demo
repair over range (-9223372036854775808, 09223372036854775808] with 100 steps for keyspace demo
step 0100 repairing range (-9223372036854775808, -9038904596117680292] for keyspace demo ...  SUCCESS
step 0099 repairing range (-9038904596117680292, -8854437155380584776] for keyspace demo ...  SUCCESS
step 0098 repairing range (-8854437155380584776, -8669969714643489260] for keyspace demo ...  SUCCESS
step 0097 repairing range (-8669969714643489260, -8485502273906393744] for keyspace demo ...  SUCCESS
step 0096 repairing range (-8485502273906393744, -8301034833169298228] for keyspace demo ...  SUCCESS
step 0095 repairing range (-8301034833169298228, -8116567392432202712] for keyspace demo ...  SUCCESS
step 0094 repairing range (-8116567392432202712, -7932099951695107196] for keyspace demo ...  SUCCESS
step 0093 repairing range (-7932099951695107196, -7747632510958011680] for keyspace demo ...  SUCCESS
step 0092 repairing range (-7747632510958011680, -7563165070220916164] for keyspace demo ...  SUCCESS
step 0091 repairing range (-7563165070220916164, -7378697629483820648] for keyspace demo ...  SUCCESS
...
```

### Dependencies
-   Python 2.6
-   Cassandra ```nodetool``` must exist in the ```PATH```

### Limitations
-   Does not work with vnodes
