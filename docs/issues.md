# Known Issues

Findings from a code review of commit `94c304b` (2026-09-05), which added the
architecture diagram to the README. Each entry lists the problem, the evidence,
and the suggested fix. Remove an entry once it is resolved.

## 1. Typo in the architecture diagram

**Severity:** Medium (user-facing, hero section of the README)

**Location:** `docs/images/architecture.webp`, embedded at `README.md:354`

**Problem:** The Command Classification box reads `SAFE / CAUTION / DANGEROUS /
FORRIBDEN`. The correct level name is `FORBIDDEN`, as used in the safety-model
table (`README.md:339`) and in `agent/policies/classifier.py`.

**Fix:** Regenerate the image with the corrected label. The text is rasterised,
so it cannot be patched in place.

## 2. Diagram advertises integrations that do not exist

**Severity:** Medium (sets wrong expectations for adopters)

**Location:** `docs/images/architecture.webp`

**Problem:** The diagram lists the following integrations that are not
implemented anywhere under `agent/`, `tools/`, or `adapters/`:

| Shown in diagram | Actual state |
| --- | --- |
| AWS / Azure / GCP (cloud SDKs) | Only `tools/aws/` exists. The only Azure reference is `OPENAI_BASE_URL` for Azure-hosted OpenAI. No GCP code. |
| Monitoring (Prometheus, Grafana) | `tools/observability/` covers Prometheus, Alertmanager and Loki. No Grafana integration. |
| Notifications (Email, Slack, Teams) | No email, Slack or Teams code. |

**Fix:** Regenerate the image so it reflects only what ships today, or add the
missing integrations and track them as roadmap items. Combine with issue 1 so
the image is regenerated once.

## 3. Relative image path breaks outside GitHub

**Severity:** Low (only matters if the package is published)

**Location:** `README.md:354`, `pyproject.toml:9`

**Problem:** `pyproject.toml` declares `readme = "README.md"`, so the README is
the PyPI long description. PyPI and most non-GitHub renderers do not rewrite
relative links, so `docs/images/architecture.webp` renders as a broken image
there. No `[project.urls]` block is defined either, so there is no repository
link to fall back on.

**Fix:** Point the embed at an absolute
`https://raw.githubusercontent.com/<org>/<repo>/main/docs/images/architecture.webp`
URL, or keep the image out of the README and link to it from
`docs/architecture.md`. Adding `[project.urls]` to `pyproject.toml` is worth
doing at the same time.

## 4. Two architecture diagrams that already disagree

**Severity:** Low (maintenance burden)

**Location:** `README.md:354` (image) and `README.md:356` onward (ASCII block);
`docs/architecture.md`

**Problem:** The README now shows the new image directly above the existing
40-line ASCII diagram. They describe the same system differently: the image
lists twelve numbered lifecycle stages and a Tool Executor pipeline, while the
ASCII block shows Orchestrator, Policy, Approval and Specialists. Both must be
updated on every architectural change. `docs/architecture.md`, the canonical
architecture document, does not reference the image at all.

**Fix:** Keep one source of truth. Either drop the ASCII block from the README in
favour of the image, or move the image into `docs/architecture.md` and link to
it from the README.
