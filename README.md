# YAYF
Yet Another YAML File (YAYF) - A declarative alternative to the humble Dockerfile

## Status
Early Design

## Basic Overview
Generate Dockerfiles from declarative YAML.

Create YAYFfile -> yayf -> docker build

## Planned Phases
### Phase 0 - Basic
Near(__*__) parity with Dockerfile keywords

__*__ probably everything except ADD and ARG

### Phase 1 - Template
Expand [Go templates](https://golang.org/pkg/text/template/). Nice alternative to ARG with variable expansion, as well as advanced things like conditionals and ranges for those crazy enough.

### Phase 2 - Enhanced
Add extra functionality to existing syntax. For example, allowing 'files'(__*__) to download, verify, and extract files (alternative to ADD, with explicit unpacking).

__*__ Draft name. Basic version mirrors COPY

### Phase 3 - Dialect
Expand existing syntax with more declarative directives such as 'users', 'groups', 'packages' (__*__). Requires dialect system to handle different package managers, etc. _Might_ also allow custom user directives

__*__ Draft ideas, not final
