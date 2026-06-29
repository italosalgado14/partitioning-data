# Reference & Glossary

## Tooling Quick Reference

| Category | Tool | Role |
|---|---|---|
| **Storage engines** | OpenZFS | Checksumming filesystem + volume manager; RAIDZ, snapshots, `zpool scrub`, `zfs send` |
| | Btrfs | CoW filesystem with data checksums/snapshots (single/RAID1; **not** RAID5/6) |
| | XFS | High-throughput large-file FS; metadata CRC only, scales to billions of files |
| | ext4 | Simplest FS; no data checksums; enable `large_dir` for huge directories |
| | SeaweedFS | Apache-2.0 S3-compatible object store; small files + large blobs, EC, Object Lock |
| | Garage | AGPLv3 lightweight S3 store; 3× replication only, low RAM |
| | Ceph + RGW | Feature-complete object/block/file; 5+ nodes, ops team |
| **Embedded / KV** | SQLite | Single-file SQL catalog; WAL, atomic, small-blob storage |
| | LMDB | mmap CoW B+tree KV store; read-heavy, crash-proof |
| | RocksDB | LSM-tree KV store; write-heavy, built-in zstd |
| **Formats** | FFV1 + Matroska (MKV) | Lossless archival video master with per-frame CRC |
| | H.264 / H.265 (HEVC) | Lossy video masters + proxies (via FFmpeg) |
| | HDF5 / NetCDF-4 | Single-file chunked N-D arrays (depth, voxels); single-writer |
| | Zarr v3 | Parallel-write chunked arrays; sharding avoids tiny-file blowup |
| | TileDB | Dense + sparse arrays (point clouds); embeddable, versioned |
| | Apache Parquet | Columnar storage for catalogs / exploded point tables |
| | Apache Arrow / Feather | In-memory / interchange (not archival — use Parquet to store) |
| | Lance | ML-native columnar format with versioning |
| | E57 / LAZ / PLY / PCD | Point-cloud archival (E57, LAZ) and interchange (PLY, PCD) |
| | COPC | LAZ reorganized as a streamable octree |
| | OpenEXR / 16-bit PNG | Lossless depth-map storage |
| **Catalog / query** | DuckDB | Serverless SQL over Parquet; Hive partition pruning, `parquet_metadata()` |
| | SQLite | Single-file relational catalog for mutable flags |
| | PostgreSQL (JSONB) | Relational catalog for heterogeneous metadata |
| | DuckLake / delta-rs | Offline ACID + time-travel table layer |
| **Versioning** | DVC | Pointer files in Git; cache to local or S3-compatible (SeaweedFS/Garage) remote; `dvc.yaml` pipelines |
| | git-annex | Distributed KV over Git; special remotes, `fsck`, `numcopies` |
| | DataLad | git + git-annex with provenance capture and subdatasets |
| | lakeFS | Git-like branch/commit over object store (needs PostgreSQL) |
| **Backup / integrity** | restic / BorgBackup / Kopia | Encrypted, deduplicating backup; append-only, verification |
| | b3sum (BLAKE3) / sha256sum | Fixity checksums for multi-year verification |
| | par2 | Parity files for repairing offline/tape archives |
| | zstd | General-purpose lossless compression (text, JSON, Parquet) |
| **Media** | FFmpeg / ffprobe | Encode/transcode/segment/proxy; extract JSON metadata sidecars |
| | VLC | Offline playback of MKV/FFV1 and HEVC |
| **3D** | Stereolabs ZED SDK | Record SVO2; export depth/point clouds |
| | PDAL / LAStools | Point-cloud pipelines; LAS↔LAZ; COPC, pgpointcloud writers |
| | Entwine / Potree | Build octrees; serve point clouds offline (static files) |
| | Open3D / PCL / CloudCompare | Point-cloud I/O, processing, inspection |
| | libE57 | Read/write/inspect E57 |
| **Search / telemetry** | LanceDB / pgvector / Qdrant / Milvus | Embedding storage + ANN search |
| | TimescaleDB / InfluxDB 3 | Sensor telemetry time-series |
| | FiftyOne | Dataset curation (uses vector backends) |

## Further Reading

**Filesystems & embedded stores**
- OpenZFS — Checksums and Their Use in ZFS — https://openzfs.github.io/openzfs-docs/Basic%20Concepts/Checksums.html
- OpenZFS — Workload Tuning (recordsize, compression) — https://openzfs.github.io/openzfs-docs/Performance%20and%20Tuning/Workload%20Tuning.html
- Btrfs — Status (RAID5/6 stability) — https://btrfs.readthedocs.io/en/latest/Status.html
- SQLite — 35% Faster Than The Filesystem — https://sqlite.org/fasterthanfs.html
- SQLite — Internal Versus External BLOBs — https://sqlite.org/intern-v-extern-blob.html
- SQLite — As An Application File Format — https://sqlite.org/appfileformat.html
- sqlite-zstd (phiresky) — https://github.com/phiresky/sqlite-zstd
- LMDB — official documentation — http://www.lmdb.tech/doc/
- RocksDB — Overview (wiki) — https://github.com/facebook/rocksdb/wiki/RocksDB-Overview

**Object storage**
- MinIO vs Ceph RGW vs SeaweedFS vs Garage (Onidel, 2025) — https://onidel.com/blog/minio-ceph-seaweedfs-garage-2025
- MinIO GitHub in maintenance mode — what's next (InfoQ) — https://www.infoq.com/news/2025/12/minio-s3-api-alternatives/
- SeaweedFS — README / architecture — https://github.com/seaweedfs/seaweedfs/blob/master/README.md
- Garage — Goals and use cases — https://garagehq.deuxfleurs.fr/documentation/design/goals/
- Ceph — hardware recommendations — https://docs.ceph.com/en/reef/start/hardware-recommendations/
- What Is RustFS? (Sealos) — https://sealos.io/blog/what-is-rustfs/
- Amazon S3 — multipart upload limits — https://docs.aws.amazon.com/AmazonS3/latest/userguide/qfacts.html
- Amazon S3 — Object Lock (WORM) — https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html
- Amazon S3 — Object Key Design Best Practices — https://hidekazu-konishi.com/entry/amazon_s3_object_key_design_best_practices.html

**Databases & catalogs**
- To BLOB or Not To BLOB (Microsoft Research, MSR-TR-2006-45) — https://www.microsoft.com/en-us/research/wp-content/uploads/2006/04/tr-2006-45.pdf
- Binary data performance in PostgreSQL (CYBERTEC) — https://www.cybertec-postgresql.com/en/binary-data-performance-in-postgresql/
- PostgreSQL — Large Objects (Chapter 33) — https://www.postgresql.org/docs/current/largeobjects.html
- MySQL 8.4 — The BLOB and TEXT Types — https://dev.mysql.com/doc/refman/8.4/en/blob.html
- MongoDB — GridFS for Self-Managed Deployments — https://www.mongodb.com/docs/manual/core/gridfs/
- Redis — Anti-Patterns Every Developer Should Avoid — https://redis.io/tutorials/redis-anti-patterns-every-developer-should-avoid/

**Scientific array & columnar formats**
- HDF5 — Single-Writer/Multiple-Reader (SWMR) — https://support.hdfgroup.org/documentation/hdf5-docs/advanced_topics/intro_SWMR.html
- Zarr-Python 3 release (Jan 2025) — https://zarr.dev/blog/zarr-python-3-release/
- ZEP 2 — Sharding codec — https://zarr.dev/zeps/accepted/ZEP0002.html
- netCDF-4/HDF5 File Format (NASA Earthdata) — https://www.earthdata.nasa.gov/about/esdis/esco/standards-practices/netcdf-4hdf5
- TileDB — LiDAR / point clouds — https://tiledb.com/data-types/lidar/
- TileDB 101: Point Clouds — https://medium.com/tiledb/tiledb-101-point-clouds-1de21fed3d49
- Apache Parquet — format spec — https://github.com/apache/parquet-format/
- Apache Arrow — Streaming, Serialization, IPC (Feather v2) — https://arrow.apache.org/docs/python/ipc.html
- Lance — Open Lakehouse Format for Multimodal AI — https://github.com/lance-format/lance

**Vector & time-series databases**
- LanceDB — https://github.com/lancedb/lancedb
- pgvector — https://github.com/pgvector/pgvector
- Qdrant — Quantization documentation — https://qdrant.tech/documentation/manage-data/quantization/
- Milvus — Deployment options (Lite / Standalone / Distributed) — https://milvus.io/docs/install-overview.md
- FAISS — Faiss on the GPU — https://github.com/facebookresearch/faiss/wiki/Faiss-on-the-GPU
- TimescaleDB — https://github.com/timescale/timescaledb
- InfluxData — Which InfluxDB 3 should I use? — https://docs.influxdata.com/influxdb3/which-influxdb-3/

**Versioning, catalogs & lakehouse**
- DVC — Remote Storage — https://dvc.org/doc/user-guide/data-management/remote-storage
- git-annex — special remotes — https://git-annex.branchable.com/special_remotes/
- DataLad (peer-reviewed, PMC) — https://pmc.ncbi.nlm.nih.gov/articles/PMC11514317/
- lakeFS — Architecture & local checkouts — https://docs.lakefs.io/understand/architecture/
- Hudi vs Delta vs Iceberg feature comparison (Onehouse) — https://www.onehouse.ai/blog/apache-hudi-vs-delta-lake-vs-apache-iceberg-lakehouse-feature-comparison
- DuckLake — integrated data lake + catalog — https://ducklake.select/

**Directory layout & partitioning**
- DuckDB — Hive Partitioning — https://duckdb.org/docs/current/data/partitioning/hive_partitioning
- DuckDB — Querying Parquet Metadata — https://duckdb.org/docs/lts/data/parquet/metadata
- PyArrow — Tabular Datasets — https://arrow.apache.org/docs/python/dataset.html
- Apache Spark — Parquet Partition Discovery — https://spark.apache.org/docs/latest/sql-data-sources-parquet.html
- Medallion lakehouse architecture (Microsoft Learn) — https://learn.microsoft.com/en-us/azure/databricks/lakehouse/medallion
- ISO 8601 (lexicographic = chronological) — https://en.wikipedia.org/wiki/ISO_8601
- ext4 — HTree indexing, large_dir (Wikipedia) — https://en.wikipedia.org/wiki/Ext4

**Video**
- Library of Congress — FFV1 codec sustainability — https://www.loc.gov/preservation/digital/formats/fdd/fdd000341.shtml
- Library of Congress — Matroska with FFV1 — https://www.loc.gov/preservation/digital/formats/fdd/fdd000343.shtml
- FFV1 — official codec specification — https://github.com/FFmpeg/FFV1/blob/master/ffv1.md
- FFmpeg — ffprobe documentation — https://ffmpeg.org/ffprobe.html
- SVT-AV1 — Common Questions / encoding guide — https://gitlab.com/AOMediaCodec/SVT-AV1/-/blob/master/Docs/CommonQuestions.md
- Why force fixed & closed GOPs in FFmpeg — https://marcinchmiel.com/articles/2020-10/why-you-should-force-fixed-closed-gops-and-how-to-do-it-in-ffmpeg/

**3D & point clouds**
- libE57 — tools for E57 (ASTM E2807) — http://libe57.org/
- COPC — Cloud Optimized Point Cloud spec 1.0 — https://copc.io/
- COPC — Cloud-Native Geospatial Formats Guide — https://guide.cloudnativegeo.org/copc/
- LASzip — Lossless Compression of Lidar Data (Isenburg) — https://lastools.osgeo.org/download/laszip.pdf
- Potree — WebGL point-cloud viewer — https://github.com/potree/potree
- PDAL — Entwine / EPT — https://pdal.io/en/stable/workshop/introduction/entwine.html
- pgpointcloud — PostgreSQL point-cloud extension — https://pgpointcloud.github.io/pointcloud/
- OpenEXR — Technical Introduction (PXR24 for depth) — https://openexr.com/en/latest/TechnicalIntroduction.html
- Khronos — glTF Draco geometry compression (mesh-only) — https://www.khronos.org/news/press/khronos-announces-gltf-geometry-compression-extension-google-draco
- Open3D — point cloud tutorial (voxel_down_sample) — https://www.open3d.org/docs/release/tutorial/geometry/pointcloud.html
- Point cloud file formats overview (E57, LAS, PLY, PCD) — https://www.thefuture3d.com/learn/point-cloud-file-formats/

**Integrity, backup & compression**
- Klara Systems — Understanding ZFS Scrubs and Data Integrity — https://klarasystems.com/articles/understanding-zfs-scrubs-and-data-integrity/
- restic — Working with repositories (check / check --read-data) — https://restic.readthedocs.io/en/latest/045_working_with_repos.html
- BorgBackup — init / encryption & append-only — https://borgbackup.readthedocs.io/en/stable/usage/init.html
- Zstandard — official site — http://facebook.github.io/zstd/

**ZED / Stereolabs (primary)**
- Stereolabs — Video Recording (SVO/SVO2 format, compression modes) — https://www.stereolabs.com/docs/video/recording
- Stereolabs — ZED SDK recording module — https://docs.stereolabs.com/docs/development/zed-sdk/modules/camera/recording
- Stereolabs Help Center — file size for ZED recordings — https://support.stereolabs.com/hc/en-us/articles/1500009124262
- Stereolabs Help Center — convert SVO to AVI / image/depth sequences — https://support.stereolabs.com/hc/en-us/articles/360009986754
- zed-sdk — SVO export sample (16-bit depth PNG) — https://github.com/stereolabs/zed-sdk/blob/master/recording/export/svo/README.md

## Glossary

- **Object storage** — A flat namespace of buckets and objects addressed by a single key string (the S3 model). "Folders" are a UI illusion built from key prefixes; there is no real directory tree. Strong at scale, rich per-object metadata, and parallel HTTP access; no in-place edit/append.
- **BLOB (Binary Large Object)** — An opaque chunk of binary data (a video, an SVO2 file, a point cloud). The guide's rule: store BLOBs as files/objects and keep only their metadata + path + checksum in a database.
- **Catalog / index** — A queryable record of every asset's location, capture metadata, checksum, and retention policy. Derived and disposable: rebuildable by re-scanning the tree, because human-readable sidecars are the source of truth.
- **Sidecar** — A small companion file (e.g. `clip.mkv.json`) holding metadata or a checksum next to the asset it describes, so the archive is self-describing without a database.
- **Chunking (content-defined)** — Splitting files into variable-length pieces by content so that unchanged regions deduplicate across backups; used by restic/Borg/Kopia.
- **GOP (Group of Pictures)** — The interval between video keyframes. You can only seek or cut cheaply at keyframes, so archives use closed, fixed-interval GOPs.
- **Proxy / master** — A *master* is the write-once, high-quality (often lossless) copy kept for years; a *proxy* is a small, regenerable low-resolution copy for fast browsing.
- **Partition key** — The field(s) used to split data into subdirectories (e.g. date). Order keys most-stable / most-filtered first; keep volatile fields out of the path.
- **Hive-style partitioning** — Encoding partition values as `key=value` directory names (`year=2026/month=06`) that DuckDB, Spark, and Arrow auto-detect and prune on.
- **Lakehouse** — A table format (Iceberg, Delta, Hudi, DuckLake) that adds ACID transactions, schema evolution, and time-travel over Parquet files. Stores structured tables, never raw video/SVO blobs.
- **Content-addressed storage** — Storing each object under a key derived from its hash (e.g. SHA-256 fanned out as `ab/cd/...`), giving automatic deduplication and built-in integrity verification.
- **Fixity / checksum** — A hash (SHA-256, BLAKE3) recorded per file and re-verified on a schedule to prove the bytes are unchanged. Checksums *detect* corruption but do not *repair* it.
- **Scrubbing** — A filesystem operation (ZFS/Btrfs `scrub`) that re-reads every block, verifies its checksum, and, with redundancy, self-heals from a good copy — the defense against silent bit-rot over years.
- **Bit-rot (silent corruption)** — A flipped bit returned with no drive error. The core multi-year threat, because ordinary copies propagate it into backups before anyone notices.
- **Erasure coding** — Splitting an object into *k* data + *m* parity shards (Reed–Solomon) so it survives losing any *m* shards, at far less overhead than full replication; the object-storage equivalent of RAID parity, with per-read checksums.
- **WORM / Object Lock** — Write-Once-Read-Many immutability. S3 Object Lock (Governance/Compliance/Legal Hold) prevents deletion or overwrite until an expiry, the strongest retention guarantee.
- **SVO / SVO2** — Stereolabs' proprietary single-file container (default since ZED SDK 4.1) storing stereo video plus timestamped sensor/IMU/custom data. It does **not** store depth or point clouds — those are recomputed on playback, so pin and archive the SDK version.
- **Point cloud** — A set of 3D points (X, Y, Z plus attributes like intensity/RGB). Archived as E57 or LAZ; queried via TileDB or Parquet; viewed offline with Potree.
- **Depth map** — A per-pixel distance image. Stored losslessly as OpenEXR (float) or 16-bit PNG (millimeters, range < 65.5 m) — never JPEG.
- **Embedding / vector** — A numeric array summarizing an image/frame for similarity search. Stored in a vector DB; comparable only within the same model version, so changing models forces a re-embed.
- **ANN (Approximate Nearest Neighbor)** — Index structures (HNSW, IVF) that find similar vectors fast without scanning every one; the engine behind near-duplicate detection.
- **3-2-1-1-0** — Backup rule: 3 copies, on 2 media types, 1 offsite, 1 offline/immutable, with 0 verified errors (proven by test restores).
