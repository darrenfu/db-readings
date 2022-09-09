# Readings in Databases

A list of papers essential to understanding latest database development and building new database systems. The list is curated and maintained by Darren Fu (@darrenfu). If you want to contribute a paper review to this list, please submit a pull request. 

## <a name='TOC'>Table of Contents</a>

  1. [VLDB Papers](#vldb)
  2. [Tech talks](#techtalks)


## <a name='vldb-papers'> VLDB Papers
* [Assembling a Query Engine From Spare Parts](https://www.firebolt.io/content/firebolt-vldb-cdms-2022): This paper introduces how Firebolt database is assembled using different opensource components as stepping stones within 18 months. Specifically:
  1. they chose *Hyrise* as the foundation of its SQL parser and planner (we would recommend *DuckDB* or *Calcite* now). The pros and cons comparison is a highlight in this paper to learn how to select a suitable opensource project as stepping stone.
  1. it chose a *vectorization* runtime: *ClickHouse*. They also implemented a new Firebolt distributed processing stack to replace the ClickHouse's stack.
  1. The storage engine uses a columnar data layout.
  1. For cross-network communication, it chose a custom *protobuf*-based serialization format (*Substrait* seems to be a better alternative now).  
  
    As a lesson learnt, they chose assembling on top of a solid foundation, building in a single language for high velocity, investing heavily to connect different systems, e.g. unifying the type systems across planner and runtime. 
* [Velox: Meta’s Unified Execution Engine](https://research.facebook.com/file/477542930588455/Velox-Metas-Unified-Execution-Engine-p1030-pedreira-cr2-1.pdf): TBD
* [Magma: A High Data Density Storage Engine Used in Couchbase](https://www.vldb.org/pvldb/vol15/p3496-lakshman.pdf): This paper primarily introduces a bunch of optimization techniques to the LSM tree based storage engine in Couchbase. The next generation storage engine, *Magma*, will replace the current one, *Couchstore*, which is based on Copy-On-Write B+Tree. Its design goals is to minimize write amplification, to scale concurrent compactions, to optimize for SSDs, and to lower the memory footprint. 
    The gist of the optimizations is **to separate the index data structure (stored in a component called LSM Tree Index) from the document data storage** (a new component called *Log Structured Object Store* - it uses segmented log concepts and allows range query by seqno). There is another new component, *Index Block Cache*, a read cache for caching the recently read (LRU) index blocks and locating the documents on log-structured storage. This object-level managed cache is more efficient than the block-level cache for document objects. To separate key and value, Magma takes a different approach than *Wisckey*. It leverages sequential I/O access patterns by avoiding the index lookup during garbage collection. Speaking of compression, Magma uses block-level compression (BLC) using the LZ4 algorithm with better compression ratios for cold log segments while Couchstore uses document-level compression. During gabarge collection, the key idea is to maintain a logically sorted *delete list LSM tree* of stale document seqnos and their size per log segment. There are other optimizations for crash recovery (maintaining a metadata file to store a point-in-time snapshot checkpoint), scaling with high IOPS using async I/O, and etc. 
* Reference: [VLDB Papers in 2022](https://vldb.org/2022/?paper-session)

## <a name='tech-talks'> Tech talks
* [Power of the Log:LSM & Append Only Data Structures](https://www.infoq.com/presentations/lsm-append-data-structures/) (2017)
  
