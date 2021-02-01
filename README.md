# SEA-2021

## This is a preliminary repository for the submission SEA 2021

Once accepted, this repository will also contain the source code of our tool.

## Direct access to the database

Once the docker is set up, it is possible to access to the database of runs by
``` 
docker run -it sea sqlite3 /home/hyperclique/tuwien.sqlite
```

Then a number of queries are possible:
- Counts the number of jobs in the database : 
```sql 
SELECT COUNT(*) FROM runs;
```
- Prints the benchmarks with their category:
``sql
SELECT interestingbenchs.bench, category FROM interestingbenchs, categories
WHERE interestingbenchs.bench = categories.bench
ORDER BY category, categories.bench;
```

## Run hyperclique

hyperclique is available at `/home/hyperclique/hyperclique`. You may want to run it with
```
docker run -it sea /home/hyperclique/hyperclique -help
```
let's say you want to run again a configuration in the database
