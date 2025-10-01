# Ericore Indexer

A high-performance Solana blockchain indexer with support for multiple program types including Anchor, SPL Token, and custom programs.

## Features

- **Multi-Protocol Support**: Anchor, SPL Token, System programs
- **Pluggable Formatters**: Extensible formatter system via Git submodules
- **Real-time Processing**: Geyser gRPC streaming with ClickHouse storage
- **Type Safety**: Generic IDL parsing with Borsh deserialization
- **High Performance**: Optimized for throughput and low latency

## Installation

### Binary Releases

Download pre-compiled binaries from the [Ericore Indexer Releases](https://github.com/Eri-Core/go-indexer-releases) repository:

```bash
# Quick download (replace with latest version)
wget https://github.com/Eri-Core/go-indexer-releases/releases/latest/download/ericore-indexer-latest-linux-amd64.tar.gz
```

1. **Download** the appropriate binary for your system:
   - Linux AMD64: Download from [releases repository](https://github.com/Eri-Core/go-indexer-releases/releases)
   - Linux ARM64: Download from [releases repository](https://github.com/Eri-Core/go-indexer-releases/releases)

2. **Extract** the tarball:
   ```bash
   tar -xzf ericore-indexer-*.tar.gz
   ```

3. **Configure** your environment:
   ```bash
   cp .env.example .env
   # Edit .env with your configuration
   ```

4. **Run** the indexer:
   ```bash
   ./ericore-indexer-*
   ```

### From Source

## Quick Start

### Prerequisites

- Go 1.21+
- ClickHouse database
- Access to Solana RPC/Geyser endpoint

### Setup

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd go-indexer
   ```

2. **Initialize formatters submodule**
   ```bash
   ./scripts/setup-formatters.sh
   ```

3. **Configure the indexer**
   - Copy configuration templates from `config-samples/`
   - Update database connection in `.env`
   - Configure programs in `configs/`

4. **Build and run**
   ```bash
   go build -o ericore-indexer .
   ./ericore-indexer
   ```

## Architecture

### Formatters System

The indexer uses a modular formatter system managed via Git submodule:

- **Location**: `./formatters/` (Git submodule)
- **Repository**: [Eri-Core/formatters](https://github.com/Eri-Core/formatters)
- **Configuration**: `./configs/formatters/ericore-formatters.json`

### Development vs Production

- **Development**: Uses formatters from local submodule
- **Production**: Uses pre-built formatter binaries

### Supported Programs

| Program | Type | Formatter |
|---------|------|-----------|
| pAMM | Anchor | `pamm/basic` |
| Sharkify | Anchor | `sharkify/basic` |
| Monaco | Anchor | `monaco/basic` |
| Jupiter | Anchor | `jupiter/basic` |
| OpenBook | Anchor | `openbook/basic` |

## Configuration

### Environment Variables

```bash
ENV=development                    # development or production
CLICKHOUSE_URL=localhost:9000     # ClickHouse connection
GEYSER_ENDPOINT=grpc://...        # Solana Geyser endpoint
```

### Program Configuration

Programs are configured in `configs/program_*.json`:

```json
{
  "program_id": "pAMMBay6oceH9fJKBRHGP5D4bD4sWpmSwMn52FMfXEA",
  "idl_path": "./path/to/idl.json",
  "enabled": true
}
```

## Development

### Working with Formatters

```bash
# Update formatters to latest
git submodule update --remote formatters

# Work on formatters locally
cd formatters
# Make changes, commit, push

# Update main repo to use new formatter version
cd ..
git add formatters
git commit -m "Update formatters to latest version"
```

### Running Tests

```bash
# Run all tests
go test ./...

# Run specific test suites
go test ./internal/parser/test/...
go test ./internal/parser/anchor/test/...
```

### Adding New Formatters

1. Add formatter to the [formatters repository](https://github.com/Eri-Core/formatters)
2. Update `configs/formatters/ericore-formatters.json`
3. Update formatters submodule: `git submodule update --remote formatters`

## Project Structure

```
go-indexer/
├── formatters/              # Git submodule - formatter implementations
├── configs/                 # Program and formatter configurations
├── internal/
│   ├── formatter/          # Formatter management system
│   ├── parser/             # Anchor, SPL parsing logic
│   ├── services/           # Core indexer services
│   └── storage/            # ClickHouse integration
├── scripts/                # Setup and utility scripts
└── proto/                  # gRPC protocol definitions
```

## Performance

The indexer is optimized for high-throughput processing:

- **Concurrent Processing**: Multi-goroutine account processing
- **Efficient Parsing**: Zero-copy Borsh deserialization where possible
- **Batch Inserts**: Optimized ClickHouse batch operations
- **Memory Management**: Careful allocation patterns for GC optimization

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make changes and add tests
4. Ensure all tests pass: `go test ./...`
5. Submit a pull request

For formatter contributions, see the [formatters repository](https://github.com/Eri-Core/formatters).

## License

[License information]