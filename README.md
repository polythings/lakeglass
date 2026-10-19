# lakeglass

An app for looking inside object storage: point it at a bucket and it finds what's there (Iceberg, Delta Lake,
Hudi, Paimon, Lance, and other tables, or plain files) and lets you browse tables, metadata files, and data files,
query them with the engine of your choice, see how a query reads a table, and find what could be stored better. It
runs in the browser, as a Mac app, and from the command line.

**Status:** design. Nothing is built yet. The design is in [spec/](spec/README.md), one spec per part.

## Planned

- Runs in the browser with nothing to install (Rust compiled to WebAssembly), or natively as a Mac app and a CLI
- Connect to S3, GCS, Azure Blob Storage, S3-compatible stores (R2, Tigris, MinIO), or a local directory
- Detect table formats and file formats from listings and file signatures, or browse the store as plain prefixes
- Table views: snapshots and commits over time, schema history, partitions, metadata files, data files, and deletes
- File views: the byte layout of Parquet, Arrow IPC, ORC, Avro, Vortex, Lance, Puffin, and SSTable files, with their
  encodings, statistics, and indexes
- SQL through pluggable engines (DataFusion first, DuckDB next), each in its own crate
- A trace of each query: which metadata it read, which files and row groups it skipped and why, and the requests
  and bytes it cost
- Checks that point out small files, missing statistics, unsorted data, piled-up deletes, old snapshots, and more
- Read-only by default; a write mode per connection, with a banner, previews, and confirmations

## License

[Apache License 2.0](LICENSE).
