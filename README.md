# merkletree-adapter-rocksdb

A RocksDB-backed persistence adapter for the Sparse Merkle Tree (SMT) core library (runcodedad/merkletree).  
This repository implements the storage concerns required by the SMT core — node/value/root/metadata persistence, snapshots, write-batch atomicity, pruning/GC, optional caching, and observability — and exposes implementations of the SMT persistence interfaces (ISmtNodeReader, ISmtNodeWriter, ISmtSnapshotManager, ISmtMetadataStore).

This adapter intentionally does NOT implement hashing, proof logic, or blockchain rules; those responsibilities belong to the SMT core or calling systems. See the SMT core README for adapter guidance and interface definitions:
https://github.com/runcodedad/merkletree/blob/d3708e8647bd50540e8fabe3898bc51ff829f755/src/MerkleTree/Smt/README.md

Key features
- RocksDB storage with recommended column families: nodes, values (optional), roots, meta
- Binary-key encoding using node/value hashes (no string encoding)
- Atomic batch writes using RocksDB WriteBatch with configurable WAL sync options
- Read-only DB snapshots for consistent proof serving
- Optional in-memory LRU cache and hot-node pinning
- Configurable pruning & garbage collection policies (keep-last-N, explicit GC)
- Metrics and optional .NET logging integration hooks
- Comprehensive test suite: persistence, snapshots, crash-safety, and performance

Quick usage (conceptual)
- Implementations here target the SMT persistence interfaces used by the core library (see SMT README).
- Typical flow:
  1. Initialize adapter with configuration (db path, CFs, cache sizing, WAL policy).
  2. Store SmtMetadata on first-run or validate on open.
  3. Use WriteBatch to atomically persist nodes/values/roots returned by the SMT core operations.
  4. Use snapshots when serving proofs to guarantee a stable read view.
  5. Configure pruning/GC to reclaim unreachable nodes as needed.

Roadmap — recommended implementation order
(Only titles and links to the corresponding issues are listed here — details live on each issue.)

1) Foundation — Metadata & compatibility  
   - Metadata storage and schema/version management — https://github.com/runcodedad/merkletree-adapter-rocksdb/issues/4

2) Core persistence primitives — Nodes & Keys  
   - Implement Node Storage (persist & dedupe SMT nodes) — https://github.com/runcodedad/merkletree-adapter-rocksdb/issues/1

3) Column families & key-format enforcement  
   - Column family layout + binary key format enforcement — https://github.com/runcodedad/merkletree-adapter-rocksdb/issues/5

4) Snapshot support  
   - Snapshot support for read-only consistent proofs — https://github.com/runcodedad/merkletree-adapter-rocksdb/issues/7

5) Optional values CF and caching  
   - Optional Value Storage column family for large values — https://github.com/runcodedad/merkletree-adapter-rocksdb/issues/2  
   - In-memory caches (LRU) and hot-node optimization — https://github.com/runcodedad/merkletree-adapter-rocksdb/issues/9

6) Observability & docs  
   - Observability: metrics + optional .NET logging integration — https://github.com/runcodedad/merkletree-adapter-rocksdb/issues/11  
   - API & docs: adapter interface, configuration, and migration notes — https://github.com/runcodedad/merkletree-adapter-rocksdb/issues/13

Links
- SMT core README (adapter guidance): https://github.com/runcodedad/merkletree/blob/d3708e8647bd50540e8fabe3898bc51ff829f755/src/MerkleTree/Smt/README.md
- This repository issues: https://github.com/runcodedad/merkletree-adapter-rocksdb/issues

License
- (Add your project's license here.)
