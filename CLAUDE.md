# CLAUDE.md

## Module Info

- **Repository:** buf.build/codefly-dev/proto
- **Location:** /Users/antoine/Development/deus/codefly.dev/proto/
- **Dependencies:** grpc-gateway, googleapis, protovalidate

## Proto Organization

- **codefly/base/v0/** — Core domain types (Workspace, Module, Service, Endpoint, Agent, Configuration, NetworkMapping)
- **codefly/services/agent/v0/** — Agent capabilities and commands
- **codefly/services/builder/v0/** — Build and deploy lifecycle
- **codefly/services/runtime/v0/** — Runtime lifecycle (Load -> Init -> Start -> Stop -> Destroy)
- **codefly/services/code/v0/** — Code intelligence (LSP, file ops, git, call graph)
- **codefly/cli/v0/** — CLI-daemon bridge protocol
- **codefly/mcp/v0/** — Model Context Protocol
- **codefly/observability/v0/** — Logging, inventory, dependencies
- **codefly/actions/v0/** — Imperative actions
- **mind/v1/** — Mind AI service (40+ RPCs: sessions, chat, execution, observation)
- **mind/debug/v1/** — Debug introspection
- **mind/gateway/v1/** — Gateway firewall (31 RPCs: files, LSP, build, git, deps)

## Key Proto Patterns

- Agent lifecycle: Load -> Init -> Start -> Stop -> Destroy, each with Status enum (UNKNOWN, SUCCESS, ERROR)
- Streaming: bidirectional for Communicate, server-side for SendMessage/InitContext/StreamEvents
- Code service uses unified Execute RPC with oneof dispatch
- NetworkMapping has instances for Container, Native, Public access
- Configuration carries origin + runtime_context + key/value pairs with secret flags

## Generation

```bash
cd /Users/antoine/Development/deus/codefly.dev/proto && buf dep update
cd /Users/antoine/Development/deus/codefly.dev/core/generated && buf generate
```

- Go output: core/generated/go/
- Python output: python/codefly-cli/codefly_cli/

## Versioning

- All codefly services at v0 (still evolving)
- Mind services at v1 (more stable)
- Each service can evolve independently

## Rules

- NEVER break backward compatibility without coordinating across core + CLI + agents
- After editing .proto files: run buf generate, then update both Go and Rust consumers
- Use buf lint before committing
- Validation uses CEL expressions via protovalidate (field constraints in proto files)
- Endpoint names: hostname pattern, 3-20 chars, lowercase
- Service names: 3-50 chars
- Workspace names: 3-25 chars, no "--"
