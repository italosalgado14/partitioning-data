# Key Concepts You Need First

A handful of ideas recur in every later chapter. Internalize these five and the rest of the guide reads as application of them.

**Raw vs. Derived (and immutability).** Split your data into two trees with opposite lifecycles. `raw/` holds the original captures — SVO2 recordings, original video, original images — and is **write-once and immutable**: mount it read-only where you can, set files to `0444`, and treat any correction as a *new* file rather than an overwrite. `derived/` holds anything you can regenerate from `raw/` plus pipeline code — depth maps, exported point clouds, transcodes, thumbnails, detections — and is **disposable**: under storage pressure you delete it and recompute later. This split is the single highest-leverage decision for ZED data, because an SVO2 stores only the stereo streams and sensors; depth maps and point clouds are *recomputed at replay*, so they are derived caches, not raw data. Tag every derived artifact with the pipeline version that produced it so stale caches are obvious.

**Hot / Warm / Cold tiering.** Not all data is accessed equally, and not all storage costs the same per terabyte. Keep recent, actively-debugged data **hot** (fast NVMe/SSD), the last year or so **warm** (bulk HDD, ideally ZFS/RAIDZ), and old, rarely-touched archives **cold** (high-density HDD, LTO tape, or external drives kept physically offline). Because time is the natural retention axis, tiers map directly onto time-based partitions: an entire `YYYY/MM` directory can be moved between tiers with a single `mv`, `rsync`, `tar`, or `zfs send`. Design the layout so this is one command, not a file-by-file migration.

**The Catalog / Index (teaser).** As soon as you have tens of thousands of clips and point clouds spread over years, `find` and `grep` stop being enough — "every ZED capture from line 2 in March where `zed_depth_valid_pct` was under 60" is not a filesystem query. The fix is a small queryable index (a single SQLite file, or Hive-partitioned Parquet queried offline with DuckDB) holding one row per asset with its path, hash, timestamp, and searchable metadata. It is cheap, it is the highest-ROI piece of the whole system, and it is *derived* — if it is ever lost you rebuild it by re-walking the tree. The full treatment, including schema and queries, is in **The Catalog / Index in Practice**.

**Metadata sidecars.** The catalog is queryable but disposable; the **source of truth** for an asset's metadata should travel *with* the file as a small sidecar — typically a `metadata.json` (and a `.sha256`/`.b3` checksum) sharing the asset's basename. Sidecars are human-readable, need no database to interpret, survive being copied to any medium (including a tape carried offsite), and make the archive self-describing. Populate them with standard tools (`ffprobe` for video, `exiftool` for images, the ZED SDK for SVO2, PDAL/`lasinfo` for point clouds), write them atomically (temp file, `fsync`, rename) to avoid drift, and mirror their contents into the catalog. Truth lives beside the bytes; the catalog is the fast index built from it.

> **Mining-server note:** Self-describing sidecars matter most exactly where you have no cloud safety net. If the catalog DB is ever lost on an isolated server, a directory of `.json` + `.sha256` sidecars means the archive is still searchable with `find`/`jq` and still verifiable on the next integrity scrub — the index is a convenience, not a dependency.

**A warning on the word "partitioning."** It means at least four different things in this space; later chapters use it precisely, so disambiguate it now:

| "Partitioning" as used in… | What it actually means | Where it appears in this guide |
|---|---|---|
| **Directory / data partitioning** | Splitting data into a hierarchical tree (by date, sensor, modality) for human and tool access | Directory Layout & Partitioning Strategies |
| **Hive-style partitioning** | Encoding partition columns as `key=value` directory names that query engines (DuckDB, Spark, PyArrow) auto-detect and *prune* | Hive-Style Partitioning (key=value) |
| **Database table partitioning** | An engine-internal split of one logical table into many physical chunks (e.g. PostgreSQL/TimescaleDB) | Relational and Time-Series sections |
| **Disk / block partitioning** | Carving a physical device into block-level partitions (`fdisk`, GPT) — the lowest layer, mostly out of scope here | Mentioned only in passing |

When this guide says "partition" without qualification, it means **directory/data partitioning** — the layout decision from the mental model above.
