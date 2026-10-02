# Shamwari AI

**An African AI companion built in Zimbabwe.**

_Shamwari_ means _friend_ in Shona. A friend serves; a friend does not
control.

[shamwari.ai](https://shamwari.ai) · [Docs](https://docs.shamwari.ai) ·
[The Nyuchi Architecture, §9](https://github.com/nyuchi/.github/blob/main/profile/canonical/NYUCHI_ARCHITECTURE.md#9-shamwari--the-ai-layer)

---

## What Shamwari is

Shamwari answers by citing the source rather than guessing it:
Zimbabwean law, tax and policy, retrieved with the provision and its
effective date. It is designed in three layers:

- **Mind**: an on-device, open-weight model. Personal data never leaves
  the device.
- **Ground**: Zimbabwean law, tax and policy, retrieved with citations.
- **Cloud**: routed inference for community and platform questions,
  never for personal data.

Two rules are enforced in code: personal-scope content is never sent to
a third-party model, and only open-weight output trains Shamwari Mind.

## Where it is today

Shamwari is being built in public and is **not a product yet**.
[shamwari.ai](https://shamwari.ai) and
[docs.shamwari.ai](https://docs.shamwari.ai) are live; the core service
and the gateway are written and tested but not deployed, and the Ground
corpus is still empty. The
[`shamwari` README](https://github.com/shamwari-ai/shamwari#where-this-actually-is-right-now)
keeps the current status.

Inside Mukoko, Shamwari features already run on Anthropic Claude through
Cloudflare AI Gateway. The localised Shamwari model is **designed**, per
the Nyuchi Architecture.

## Repositories

| Repository                                                              | What it is                                                                     |
| ----------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| [`shamwari`](https://github.com/shamwari-ai/shamwari)                   | The main repository: architecture, scope rules and the core build. Apache 2.0. |
| [`shamwari-core`](https://github.com/shamwari-ai/shamwari-core)         | The core service and its authoritative scope gate (Python, FastAPI).           |
| [`shamwari-gateway`](https://github.com/shamwari-ai/shamwari-gateway)   | The edge API gateway on Cloudflare Workers.                                    |
| [`shamwari-mind`](https://github.com/shamwari-ai/shamwari-mind)         | The Mind training pipeline, QLoRA configuration and evaluation harness.        |
| [`shamwari-sandbox`](https://github.com/shamwari-ai/shamwari-sandbox)   | `code.shamwari.ai`, the sandbox host for personal-scope artefact execution.    |
| [`shamwari-web`](https://github.com/shamwari-ai/shamwari-web)           | [shamwari.ai](https://shamwari.ai), the public site.                           |
| [`shamwari-platform`](https://github.com/shamwari-ai/shamwari-platform) | `platform.shamwari.ai`, the API key and usage console.                         |
| [`docs`](https://github.com/shamwari-ai/docs)                           | [docs.shamwari.ai](https://docs.shamwari.ai).                                  |

## The canonical documents

- [The Nyuchi Architecture](https://github.com/nyuchi/.github/blob/main/profile/canonical/NYUCHI_ARCHITECTURE.md),
  which wins wherever documents disagree
- [The Mukoko Manifesto](https://github.com/mukoko-dev/.github/blob/main/profile/canonical/MUKOKO_MANIFESTO.md)
- [The Bundu Order](https://github.com/bundu-labs/.github/blob/main/profile/canonical/BUNDU_ORDER.md)

## Contributing

Engineering rules and the lint gate are shared across the estate from
[`nyuchi/.github`](https://github.com/nyuchi/.github). Read this
organisation's
[`CONTRIBUTING.md`](https://github.com/shamwari-ai/.github/blob/main/CONTRIBUTING.md)
and [`nyuchi/.github`'s `AGENTS.md`](https://github.com/nyuchi/.github/blob/main/AGENTS.md)
before opening a PR.

Shamwari is Bundu Foundation IP, sold commercially under Nyuchi Africa.
