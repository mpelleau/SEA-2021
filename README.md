# SEA-2021

## Building the Docker image


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
```sql
SELECT interestingbenchs.bench, category FROM interestingbenchs, categories
WHERE interestingbenchs.bench = categories.bench
ORDER BY category, categories.bench;
```

## Run hyperclique

hyperclique is available at `/home/hyperclique/hyperclique`. You may want to run it with the help argument to get informations about running it
```
docker run -it sea /home/hyperclique/hyperclique -help
```

All the problems are under `/home/hyperclique/tuwien-htd`. You can, for instance, run hyperclique with the first method (a) with the default values with:
```bash
root@91a79eae4e67:/home/hyperclique# ./hyperclique -a -j -i tuwien-htd/Dubois-030.xml.htd 
{"bench": "tuwien-htd/Dubois-030.xml.htd", "o": 0, "f": 0, "n": 0, "hyperedges": 60, "nodes": 90, "maxrank": 3, "parsingtime": 0.000228, "m": "a", "cliques": [{"nbnodes": 90, "nbedges": 60, "nbcliques": 60, "rank": 3, "time": 0.00983233, "nbcalls": 308}], "totalcliques": 60, "totalcalls": 308, "timedout": 0, "bktotaltime": 0.0104824, "totaltime": 0.0107108}
```

To help reading/parsing the output, `jq` is installed on the docker. You can pretty print the output with
``` bash
root@91a79eae4e67:/home/hyperclique# ./hyperclique -a -j -i tuwien-htd/Dubois-030.xml.htd | jq
```
Which gives you the JSON output:
```json
{
  "bench": "tuwien-htd/Dubois-030.xml.htd",
  "o": 0,
  "f": 0,
  "n": 0,
  "hyperedges": 60,
  "nodes": 90,
  "maxrank": 3,
  "parsingtime": 0.00019,
  "m": "a",
  "cliques": [
    {
      "nbnodes": 90,
      "nbedges": 60,
      "nbcliques": 60,
      "rank": 3,
      "time": 0.0103165,
      "nbcalls": 308
    }
  ],
  "totalcliques": 60,
  "totalcalls": 308,
  "timedout": 0,
  "bktotaltime": 0.0109974,
  "totaltime": 0.0111877
}
```



let's say you want to run again a configuration in the database. The main component in the traces is the table `runid`. 
```
sqlite> .schema runs 
CREATE TABLE runs
     (runid Integer primary key AUTOINCREMENT, 
     bench varchar(250), 
     o int, 
     f int,
     n int,
     m CHAR(1) CHECK( m IN ('a','b','c') ),
     maxrank int,
     timedout int(1),
     timeout float,
     hyperedges int,
     nodes int,
     totalcliques int,
     parsingtime float,
     totaltime float,
     bktotaltime float,
     totalcalls int);
```
It is possible to see the main configurations and results  by
```sql
SELECT runs.bench, m, n, f, o, totaltime, timedout
FROM runs, interestingbenchs 
WHERE runs.bench=interestingbenchs.bench
LIMIT 10;
```
which gives (the `LIMIT` keyword is of course optional)
```
./tuwien-htd/rand-3-24-24-76-632-50.xml.htd|c|2|1|4|0.0077765|0
./tuwien-htd/Nonogram-074-table.xml.htd|c|2|1|4|0.0296264|0
./tuwien-htd/rand-3-28-28-93-632-48.xml.htd|c|2|1|4|0.0201116|0
./tuwien-htd/reg-s20-p03-c20-d10-n10-l5-97.xml.htd|c|2|1|4|0.00680803|0
./tuwien-htd/reg-s20-p03-c20-d10-n10-l5-96.xml.htd|c|2|1|4|0.00688477|0
./tuwien-htd/rand-7-40-8-40-02500-0.xml.htd|c|2|1|4|0.00638341|0
./tuwien-htd/rand-3-24-24-76-632f-32.xml.htd|c|2|1|4|0.00356882|0
./tuwien-htd/Nonogram-151-table.xml.htd|c|2|1|4|0.44246|0
./tuwien-htd/mdd-7-25-5-ps05-psh07-7.xml.htd|c|2|1|4|0.0065936|0
./tuwien-htd/Nonogram-155-table.xml.htd|c|2|1|4|0.0301679|0
```
