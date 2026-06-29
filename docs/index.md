# Storing Large, Heterogeneous Data at Scale: A Practical Guide

Production machine-vision and ML pipelines do not just produce results — they produce *evidence*: debug images, video recordings, depth maps, point clouds, and sensor logs that pile up for years and that you will eventually need to search, replay, or re-process. When that data lives on isolated, air-gapped servers (no cloud durability, no managed backups, no infinite bucket) the storage problem stops being "where do I put this file" and becomes "how do I keep terabytes of heterogeneous data findable, intact, and affordable for the better part of a decade." This guide is a practical reference for building that storage layer with self-hosted, offline-capable tools that scale from gigabytes to many terabytes.

> **Mining-server note:** Throughout, the running example is a mining operation whose debug *image* storage already works fine. The new pain is **video** and **Stereolabs ZED 3D data** (SVO/SVO2 recordings, depth maps, point clouds) — much larger, more heterogeneous, and burdened by multi-year retention on servers that may never touch the internet.

## How to Use This Guide

**Who this is for.** Engineers and operators who run data-producing pipelines and have to store the output themselves — on their own disks, on isolated networks, for years. You should be comfortable with the command line, basic filesystems, and SQL. You do not need to be a storage specialist; the goal is to make you one for your own use case.

**How it is organized.** The guide moves from foundations to specifics:

- **Concepts** — *How to Use This Guide* (this section), *The Mental Model: Storage Engine vs. Layout vs. Catalog*, and *Key Concepts You Need First*: the mental model and vocabulary everything else depends on.
- **Storage Options: The Landscape** — every place bytes can live, from a plain filesystem to object stores, databases, scientific array formats, and lakehouses, each with the same *What it is / Best for / Avoid when / Tools / Trade-offs* breakdown.
- **Directory Layout & Partitioning Strategies** — how to physically organize the files once you have picked an engine.
- **Cross-Cutting Concerns** — the catalog, integrity over years, backup/dedup, and compression that apply no matter what you chose.
- **Modality-Specific Playbooks** — concrete recipes for images, video, and 3D/point-cloud data.
- **Decision Guide** and **Recommended Architecture** — decision tree, comparison matrix, and a concrete default stack for isolated servers, followed by a tooling quick reference, further reading, and a glossary.

**One-line reading path.** Read this Concepts chapter, skim *The Landscape* to find the two or three engines that match your data, then jump straight to *Recommended Architecture for Production Sensor Data (Isolated Servers)* and work backward into the cross-cutting and modality chapters as needed.
