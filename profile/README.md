# OpenAUDR

[![Spec v1.0.0](https://img.shields.io/badge/spec-v1.0.0-blue)](https://openaudr.dev/spec/v1.0.0/)
[![License Apache 2.0](https://img.shields.io/badge/license-Apache%202.0-blue)](https://github.com/openaudr/audr/blob/main/LICENSE)

**AUDR (Agent Usage Detail Record)** is an open standard for recording who initiated an agent run and how much each step cost, across every system the run passes through.

A single agent run touches several systems. The application knows the customer and the feature. The router knows the model, the tokens and the price. The provider knows the cache split. Every layer is observable on its own, and none of them can tell you what that customer's agent cost you last month. AUDR is one JSON record per metered operation, carrying enough identity and attribution that the records join.

The telecom industry solved the same problem with the Call Detail Record. AUDR is built on that principle, for agents.

### 🚀 Getting Started

- [Specification v1.0.0 →](https://openaudr.dev/spec/v1.0.0/)
- [JSON Schema →](https://openaudr.dev/spec/v1.0.0/audr.schema.json)
- [Example record →](https://github.com/openaudr/audr/blob/main/spec/v1.0.0/examples/record.json)
- [openaudr.dev →](https://openaudr.dev)

### 🧩 How It Works

Every layer keeps reporting what it already reports. AUDR adds three rules that let those reports come together into one record.

- **Shared run ID** — minted by the harness, passed to the router in request metadata, and echoed back. Every system that touches the run carries the same ID.
- **Clear authority per field** — the harness owns attribution (customer, environment, initiator). The router owns usage (tokens, provider). Each fact has exactly one source.
- **Strict merge rules** — the sink assembles records sharing a run and span ID. No component rewrites another's block. Conflicts are rejected, and a correction is a new record, never a mutation.

```json
{
  "spec_version": "1.0.0",
  "record_id": "01K4N8D2J4P7Q9R3S6T8V1W5XY",
  "emitter": { "component": "router", "name": "@audr/openrouter", "version": "0.5.1" },
  "timing": { "event_time": "2026-09-08T12:00:00.000Z" },
  "resource": {
    "provider": "anthropic",
    "type": "model",
    "name": "claude-sonnet-4-20250514",
    "operation": "generation",
    "modality": "text"
  },
  "run": { "run_id": "01K4N8B0M2C5F7H9J1L3N6P8QR", "span_id": "model-call-1" },
  "attribution": { "environment": "production", "account_id": "account-42" },
  "usage": { "llm": { "input_tokens": 1200, "output_tokens": 300 } }
}
```

### 📦 Repositories

| Repository | What it is |
| --- | --- |
| [openaudr/audr](https://github.com/openaudr/audr) | The specification, JSON Schema, examples, and conformance fixtures. |

### 🛠️ SDKs & Adapters

Reference SDKs are in progress. They will ship in this organisation and are open source under Apache 2.0.

| SDK | Status |
| --- | --- |
| TypeScript | Coming soon |
| Python | Coming soon |

Each SDK ships adapters that wrap a router or gateway so every call emits a record without changing your call sites — starting with **OpenRouter**, **LiteLLM**, and **NVIDIA NeMo Relay**. Records go wherever you point them: a file, your warehouse, an OpenTelemetry collector, or a rating engine. No account, hosted backend, or pricing configuration is needed.

### 🤝 Get Involved

The most useful thing you can give us right now is an hour with the spec and an honest account of where it breaks for a cost model you have and we haven't imagined.

- [Open an issue →](https://github.com/openaudr/audr/issues) — especially if you think a design decision can be improved. Tell us specifically where it breaks.
- [Write an adapter →](https://github.com/openaudr/audr) — for a harness or router we haven't reached yet. A conformant adapter is roughly 200 lines against the shared fixtures.
- [CONTRIBUTING.md →](https://github.com/openaudr/audr/blob/main/CONTRIBUTING.md) — how a change to the spec is proposed, reviewed, and accepted.

### 🏛️ Governance

AUDR was drafted at Chargebee, and is being improved with collaboration across the ecosystem. Granular cost and usage instrumentation are foundational to agent unit economics — the infrastructure every team building or monetizing agents will need. We believe that infrastructure should be open, neutral, and community-owned. As adoption grows, the goal is to move cost governance to an independent foundation.

The spec carries no prices and no rating logic, and the SDKs have no concept of plans or invoices. Stewarded by [Chargebee](https://www.chargebee.com). Licensed under [Apache 2.0](https://github.com/openaudr/audr/blob/main/LICENSE).

### 🔒 Security

Records carry no prompt content, no key material, and no PII. To report a vulnerability, follow [SECURITY.md](https://github.com/openaudr/audr/blob/main/SECURITY.md) — please don't use the public issue tracker.

### 📬 Contact Us

- Questions, feedback, or a cost model AUDR can't express? Reach us at audr@chargebee.com
