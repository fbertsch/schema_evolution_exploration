# Schema Evolution Exploration

These notebooks were created to test out how schema evolution works in both Spark and Presto.

There are basically four modes of reading parquet for our use case:
1. Spark
2. Spark with mergeSchema = True
3. parquet2hive + Presto using column index (hive.parquet.use-column-names=false)
4. parquet2hive + Presto using column name (hive.parquet.use-column-names=true)

For each scenario, I've tested all of these modes. The scenarios I've covered are:
1. Adding a column
2. Removing a column
3. Renaming a column
4. Replacing a column (new type)
5. Transposing columns
6. Transposing with add and remove
7. Adding a new subcolumn to a nested Row type
8. Removing a subcolumn from a nested Row type

Note that modes 1 and 2 are repeated in both notebooks. Any discrepencies should be pointed out immediately.

## Running the Notebook

You'll need parquet2hive_server running on the machine with the Hive metastore (probably Presto cluster master, unless you've done some config, in which case you already know what you're doing).

```bash
sudo pip install parquet2hive_server
start_parquet2hive_server
```

Make sure the notebook has the correct DNS entry for the associated Hive metastore in the ipynb notebook.

Then you need to run the notebook. These notebooks DO error, as it is expected behavior - this halts Jupyter running the cells sequentially. You can either run them manually, or run the following to get the output notebook:

```bash
mkdir -p ~/analyses/output; cd ~/analyses/output
export NOTEBOOK_NAME="Schema_Evolution_Exploration.ipynb"
export PYSPARK_DRIVER_PYTHON=jupyter
PYSPARK_DRIVER_PYTHON_OPTS="nbconvert --ExecutePreprocessor.timeout=-1 --ExecutePreprocessor.kernel_name=python2 --to notebook --log-level=10 --execute \"../${NOTEBOOK_NAME}\" --allow-errors --output-dir ./ " \
pyspark
```

Then the resulting notebook, with errors, will be produced in the ~/analyses/output directory.

## Setting Presto to use column names
By default, Presto uses column indices to map column names. This way, even if a column name changes, the earlier data will still be read since it is mapping instead to an index. To change this behavior and instead always map to column names (the ones defined in the parquet schema):

```bash
echo 'hive.parquet.use-column-names=true' | sudo tee -a /etc/presto/conf.dist/catalog/hive.properties
sudo restart presto-server
```
