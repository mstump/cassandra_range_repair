cassandra_range_repair
======================

Python script to repair the primary range of a cassandra node in N discrete steps

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

Sample Output:

```
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
