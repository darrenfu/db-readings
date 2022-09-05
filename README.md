# Readings in Databases

A list of papers essential to understanding latest database development and building new database systems. The list is curated and maintained by Darren Fu (@darrenfu). If you want to contribute a paper review to this list, please submit a pull request. 

## <a name='TOC'>Table of Contents</a>

  1. [VLDB Papers](#vldb)


## <a name='basic-and-algo'> VLDB Papers
* [Assembling a Query Engine From Spare Parts](https://www.firebolt.io/content/firebolt-vldb-cdms-2022): This paper introduces how Firebolt database is assembled using different opensource components as stepping stones within 18 months. Specifically:
  1. they chose *Hyrise* as the foundation of its SQL parser and planner (we would recommend *DuckDB* or *Calcite* now).
  1. it chose a *vectorization* runtime: *ClickHouse*. They also implemented a new Firebolt distributed processing stack to replace the ClickHouse's stack.
  1. The storage engine uses a columnar data layout.
  1. For cross-network communication, it chose a custom *protobuf*-based serialization format (*Substrait* seems to be a better alternative now).  
  
    As a lesson learnt, they chose assembling on top of a solid foundation, building in a single language for high velocity, investing heavily to connect different systems, e.g. unifying the type systems across planner and runtime. 
* [Velox: Meta’s Unified Execution Engine](https://research.facebook.com/file/477542930588455/Velox-Metas-Unified-Execution-Engine-p1030-pedreira-cr2-1.pdf): TBD
* [Magma: AHighDataDensity Storage Engine Used in Couchbase](https://www.vldb.org/pvldb/vol15/p3496-lakshman.pdf): TBD
* Reference: [VLDB Papers in 2022](https://vldb.org/2022/?papers-industrial](https://vldb.org/2022/?paper-session)
