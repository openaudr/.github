# OpenAUDR

[![Spec v1.0.0](https://img.shields.io/badge/spec-v1.0.0-blue)](https://openaudr.dev/spec/v1.0.0/)
[![License Apache 2.0](https://img.shields.io/badge/license-Apache%202.0-blue)](https://github.com/openaudr/audr/blob/main/LICENSE)

**AUDR (Agent Usage Detail Record)** is an open standard for recording who initiated an agent run and how much each step cost, across every system the run passes through.

A single agent run touches several systems. The application knows the customer and the feature. The router knows the model, the tokens and the price. The provider knows the cache split. Each layer is observable on its own, yet none of them can report what one customer's agent cost over a month. AUDR is one JSON record per metered operation, carrying enough identity and attribution that the records join.

The telecom industry solved the same problem with the [Call Detail Record](https://en.wikipedia.org/wiki/Call_detail_record). AUDR applies that principle to agents.

### 🚀 Getting Started

- [Specification v1.0.0 →](https://openaudr.dev/spec/v1.0.0/)
- [JSON Schema →](https://openaudr.dev/spec/v1.0.0/audr.schema.json)
- [Example record →](https://github.com/openaudr/audr/blob/main/spec/examples/record.json)
- [openaudr.dev →](https://openaudr.dev)

### 🧩 How It Works

Every layer keeps reporting what it already reports. AUDR adds three rules that let those reports join into one record.

- **Shared run ID** — minted by the harness, passed to the router in request metadata, and echoed back. Every system that touches the run carries the same ID.
- **Clear authority per field** — the harness owns attribution (customer, environment, initiator). The router owns usage (tokens, provider). Each fact has exactly one source.
- **Strict merge rules** — the sink assembles records sharing a run and span ID. No component rewrites another's fields. Conflicts are rejected, and a correction is a new record, never a mutation.

```json
{
  "spec_version": "1.0.0",
  "record_id": "01K4N8D2J4P7Q9R3S6T8V1W5XY",
  "emitter": { "component": "router", "name": "openrouter", "version": "1.12.1" },
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

| Repository | Contents |
| --- | --- |
| [openaudr/audr](https://github.com/openaudr/audr) | The specification, JSON Schema, examples and conformance fixtures; the Python and TypeScript SDKs, adapters and sinks. |

### 🛠️ SDKs, Adapters and Sinks

All packages are open source under Apache 2.0. An adapter turns a runtime's events into records; a sink delivers records to a destination. The core SDKs include file and in-memory sinks.

| Package | Python (PyPI) | TypeScript (npm) |
| --- | --- | --- |
| [Core SDK](https://github.com/openaudr/audr/tree/main/adapters/core) | [`audr`](https://pypi.org/project/audr/) | [`@openaudr/audr`](https://www.npmjs.com/package/@openaudr/audr) |
| [LiteLLM adapter](https://github.com/openaudr/audr/tree/main/adapters/litellm) | [`audr-adapter-litellm`](https://pypi.org/project/audr-adapter-litellm/) | — |
| [NVIDIA NeMo Relay adapter](https://github.com/openaudr/audr/tree/main/adapters/nemo-relay) | [`audr-adapter-nemo-relay`](https://pypi.org/project/audr-adapter-nemo-relay/) | — |
| [Vercel AI SDK adapter](https://github.com/openaudr/audr/tree/main/adapters/vercel-ai) | — | [`@openaudr/audr-adapter-vercel-ai`](https://www.npmjs.com/package/@openaudr/audr-adapter-vercel-ai) |
| [Mastra adapter](https://github.com/openaudr/audr/tree/main/adapters/mastra) | — | [`@openaudr/audr-adapter-mastra`](https://www.npmjs.com/package/@openaudr/audr-adapter-mastra) |
| [Merge Gateway adapter](https://github.com/openaudr/audr/tree/main/adapters/merge-gateway) | — | [`@openaudr/audr-adapter-merge-gateway`](https://www.npmjs.com/package/@openaudr/audr-adapter-merge-gateway) |
| [Chargebee sink](https://github.com/openaudr/audr/tree/main/sinks/chargebee) | [`audr-sink-chargebee`](https://pypi.org/project/audr-sink-chargebee/) | [`@openaudr/audr-sink-chargebee`](https://www.npmjs.com/package/@openaudr/audr-sink-chargebee) |

### 🤝 Get Involved

Feedback from teams that apply the specification to a real cost model is the most valuable contribution at this stage.

- [Open an issue →](https://github.com/openaudr/audr/issues) — report a cost model AUDR cannot express, or a design decision that can be improved, stating where it breaks.
- [Write an adapter →](https://github.com/openaudr/audr/blob/main/adapters/CONTRIBUTING.md) — for a harness or router not yet covered.
- [Write a sink →](https://github.com/openaudr/audr/blob/main/sinks/CONTRIBUTING.md) — for a destination not yet covered.
- [CONTRIBUTING.md →](https://github.com/openaudr/audr/blob/main/CONTRIBUTING.md) — how a change to the specification is proposed, reviewed and accepted.

### 🏛️ Governance

AUDR was drafted at Chargebee, which stewards it. Governance is intended to move to an independent foundation as adoption grows.

The specification carries no prices and no rating logic, and the SDKs have no concept of plans or invoices. Licensed under [Apache 2.0](https://github.com/openaudr/audr/blob/main/LICENSE).

### 🔒 Security

Records carry no prompt or completion content, no key material and no personal data. Report a vulnerability as described in [SECURITY.md](https://github.com/openaudr/audr/blob/main/SECURITY.md), not through the public issue tracker.

### 📬 Contact

Send questions, feedback, or a cost model AUDR cannot express to audr@chargebee.com.
