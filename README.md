#Schema Evolution Exploration

These notebooks were created to test out how schema evolution works in both Spark and Presto.

There are basically four modes of reading parquet for our use case:
1. Spark
2. Spark with mergeSchema = True
3. parquet2hive + Presto using column index (hive.parquet.use-column-names=false)
4. parquet2hive + Presto using column name (hive.parquet.use-column-names=false)

For each scenario, I've tested all of these modes. The scenarios I've covered are:
1. Adding a column
2. Removing a column
3. Renaming a column
4. Replacing a column (new type)
5. Transposing columns
6. Transposing with add and remove

Note that modes 1 and 2 are repeated in both notebooks. Any discrepencies should be pointed out immediately.

##Setting Presto to use column names
By default, Presto uses column indices to map column names. This way, even if a column name changes, the earlier data will still be read since it is mapping instead to an index. To change this behavior and instead always map to column names (the ones defined in the parquet schema):

```
echo 'hive.parquet.use-column-names=true' >> /etc/presto/conf.dist/catalog/hive.properties
sudo restart presto-server
```
