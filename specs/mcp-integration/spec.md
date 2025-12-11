# MCP Integration Specification

## Purpose
Defines the Model Context Protocol (MCP) integration capabilities for claude-flow-docker, enabling AI assistants to interact with the orchestration system via SSE transport.

## Requirements

### Requirement: SSE Transport
The system SHALL expose MCP server via Server-Sent Events (SSE) transport on configurable port.

#### Scenario: Default port configuration
- **WHEN** container starts without port override
- **THEN** MCP SSE endpoint is available at port 8080

#### Scenario: Custom port configuration
- **WHEN** MCP_SSE_PORT environment variable is set
- **THEN** MCP SSE endpoint uses the specified port

### Requirement: MCP Proxy Bridge
The system SHALL use mcp-proxy to bridge stdio-based claude-flow MCP to SSE transport.

#### Scenario: Proxy initialization
- **WHEN** container starts
- **THEN** mcp-proxy launches and wraps `claude-flow mcp start`

#### Scenario: Connection establishment
- **WHEN** client connects to SSE endpoint
- **THEN** session ID is generated in format `session-cf-{timestamp}-{random}`

### Requirement: Tool Discovery
The system SHALL expose 80+ MCP tools for agent orchestration.

#### Scenario: Tool listing
- **WHEN** client requests available tools
- **THEN** complete tool catalog is returned including swarm, agent, memory, and neural tools

### Requirement: Hive Memory Persistence
The system SHALL persist hive memory to SQLite database.

#### Scenario: Memory initialization
- **WHEN** MCP server starts
- **THEN** SQLite database is created at `/workspace/project/.swarm/memory.db`

#### Scenario: Cross-session persistence
- **WHEN** container restarts
- **THEN** memory data is preserved from previous sessions

### Requirement: Log Formatting
The system SHALL format log output with human-readable timestamps and colored log levels.

#### Scenario: Timestamp formatting
- **WHEN** log entry is emitted
- **THEN** ISO timestamp is converted to `[DD.MM.YY|HH:MM]` format

#### Scenario: Level coloring
- **WHEN** INFO level log is emitted
- **THEN** `[INFO]` is displayed with green background and black text

#### Scenario: ERROR level coloring
- **WHEN** ERROR level log is emitted
- **THEN** `[ERROR]` is displayed with red background and black text
