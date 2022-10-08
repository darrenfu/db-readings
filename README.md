# Readings in Databases

A list of papers essential to understanding latest database development and building new database systems. The list is curated and maintained by Darren Fu (@darrenfu). If you want to contribute a paper review to this list, please submit a pull request. 

## <a name='TOC'>Table of Contents</a>

  1. [Emerging database technologies](#newdb)
  1. [Query management](#query-mgnt)
  1. [Query execution](#query-exec)
  1. [Storage](#storage)
  1. [Miscellaneous papers](#misc)
  1. [Tech talks](#techtalks)
  1. [References](#ref)


## <a name='newdb'> Emerging database technologies
1. [The Snowflake Elastic Data Warehouse](https://dl.acm.org/doi/pdf/10.1145/2882903.2903741): This paper highlights the key characteristics and features of Snowflake DW. Its main design ideas are:  
    * SaaS model 
    * Multi-cluster, shared-data architecture
    * Separation of storage (S3) and compute

    Its architecture is very modern in 2016 to embrace the cloud:  
    * VW elasticity.
    * Local caching (in each worker node) and file stealing. It follows LRU replacement policy to replace cache contents over time lazily via consistent hashing algorithm. File stealing is an interesting technique to deal with slow/straggler node to catch up with its worker process to pull files from peer nodes.
    * Execution engine: columnar, vectorized, push-based. *This is very similar to Facebook Velox's implementation.*
    * Query management: TBD
    * Concurrency control: MVCC, time travel, copy from old snapshots.
    * Pruning: TBD
    
    I want to highlight some interesting features to make Snowflake unique:
    * TBD
 
1. [Assembling a Query Engine From Spare Parts](https://www.firebolt.io/content/firebolt-vldb-cdms-2022): This paper introduces how **Firebolt** database is assembled using different opensource components as stepping stones within 18 months. Specifically:  
      * They chose *Hyrise* as the foundation of its SQL parser and planner (we would recommend *DuckDB* or *Calcite* now). The pros and cons comparison is a highlight in this paper to learn how to select a suitable opensource project as stepping stone.
    * It chose a *vectorization* runtime: *ClickHouse*. They also implemented a new Firebolt distributed processing stack to replace the ClickHouse's stack.
    * The storage engine uses a columnar data layout.
    * For cross-network communication, it chose a custom *protobuf*-based serialization format (*Substrait* seems to be a better alternative now).  
  
    As a lesson learnt, they chose assembling on top of a solid foundation, building in a single language for high velocity, investing heavily to connect different systems, e.g. unifying the type systems across planner and runtime. 
* [Velox: Meta’s Unified Execution Engine](https://research.facebook.com/file/477542930588455/Velox-Metas-Unified-Execution-Engine-p1030-pedreira-cr2-1.pdf): TBD

## <a name='query-mgnt'> Query management
TBD
  
## <a name='query-exec'> Query execution
TBD

## <a name='storage'> Storage
1. [Magma: A High Data Density Storage Engine Used in Couchbase](https://www.vldb.org/pvldb/vol15/p3496-lakshman.pdf): This paper primarily introduces a bunch of optimization techniques to the LSM tree based storage engine in Couchbase. The next generation storage engine, *Magma*, will replace the current one, *Couchstore*, which is based on Copy-On-Write B+Tree. Its design goals is to minimize write amplification, to scale concurrent compactions, to optimize for SSDs, and to lower the memory footprint.  
  
    The gist of the optimizations is to separate the index data structure (*LSM Tree Index*) from the document data storage (*Log Structured Object Store*, which uses *segmented log* concept and allows range query by seqno). To separate key and value, Magma takes a different approach than *Wisckey*. It leverages sequential I/O access patterns by avoiding the index lookup during garbage collection. Other optimization highlights:  
      * [Index Block Cache] A read cache for caching the recently read (LRU) index blocks and locating the documents on log-structured storage. This object-level managed cache is more efficient than the block-level cache for document objects. 
    * [Compression] Magma uses block-level compression (BLC) using the LZ4 algorithm with better compression ratios for cold log segments while Couchstore uses document-level compression. 
    * [Gabarge collection] The key idea is to maintain a logically sorted *delete list LSM tree* of stale document seqnos and their size per log segment. 
    * [Crash recovery] Maintaining a metadata file to store a point-in-time snapshot checkpoint
    * [Scaling with high IOPS] Using async I/O. 

1. [Fast Scans on Key-Value Stores](https://vldb.org/pvldb/vol10/p1526-bocksrocker.pdf): This paper enumerates the dominant factors impacting the performance of Key-Value Store (KVS) with point queries versus range queries. It depicts the SQL-over-NoSQL architecture and shows the possible compromises to support mixed OLAP/OLTP workloads on top of a KVS. The alternative approaches are based on three performance characteristics in terms of storage efficiency (fragmentation), concurrency (addition conflicts), cost to implement versioning and GC, and efficiency for scan and get/put operations. There are a total of 24 ways to build KVS using this taxonomy:  
    * (update-in-place vs. log-structured vs. delta-main)  
    * (row-major vs. column-major / PAX)  
    * (clustered-versions vs. chained-versions)
    * (periodic vs. piggy-backed garbage collection)

    The authors implemented two of the variants as below. TellStore makes careful compromises with regard to latency vs. throughput tradeoffs (e.g. batching) and time vs. space tradeoffs (lock-free hash table) to help remedy the effects of concurrent updates on scans.
    * TellStore-Log: based on logstructured with chained-versions in a row-major format and 
    * TellStore-Col: using a delta-main structure with clustered-versions in a column-major format.
  
## <a name='misc'> Miscellaneous papers  
TBD

## <a name='techtalks'> Tech talks
1. [Power of the Log:LSM & Append Only Data Structures](https://www.infoq.com/presentations/lsm-append-data-structures/) (2017)

## <a name='ref'> References
1. [VLDB Papers in 2022](https://vldb.org/2022/?paper-session)
1. [Dr. Jana Giceva Makreshanska's publications](https://db.in.tum.de/~giceva/index.shtml?lang=en)
