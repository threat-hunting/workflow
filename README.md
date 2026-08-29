# kopal-workflows-b17

Workflowهای **تست موج B17** (نه برای مشتری؛ قابل wipe با موج).

**Inventory کامل (alias · WS · STATUS · gap):** [`INVENTORY.md`](INVENTORY.md) → SoT در `kopal-other-b17/docs/INVENTORY.md` · KM **DO-02-103**.

ممکن است B17A آن‌ها را بسازد/پین کند؛ B17C روی همان pin قضاوت کند.

## B17A · L0 — custom registry ping

| Item | Value |
|------|--------|
| Path | `workflows/wf_2mhoBq8oEFmyiwO9qfBaQH/definition.yml` |
| Action | `tools.b17_lab.ping` (from `kopal-integrations-b17`) |
| Harness | `rebrand-harness/b17a/scenarios/b17a-l0-custom-registry-ping.yaml` |
| Docs | Book **DO-09** · **DO-02-079** · **DO-02-064** · **PE-03-003** |

**Repeatable path until HOLD_PRODUCT (GitHub App git pull) lifts:** push YAML here → import via API multipart (`POST .../workflows` with `file`) or MCP `create_workflow` with the same definition envelope. Do not claim Git sync green while HOLD_PRODUCT stands.

**Promote:** `*-testidea` → `*-b17` → operational `kopal-workflows`.

## GitHub integration and built-in registry smoke pack

The read-only integration workflow definitions are documented in
[`INTEGRATION-WORKFLOWS.md`](INTEGRATION-WORKFLOWS.md). They cover the GitHub
`threat-hunting/kopal-integrations` source (`tools.b17_lab.*` and
`tools.soclib.*`) and the built-in `kopal_registry` `core.*` actions without
embedding credentials. Run the deterministic core smoke before the LDAP or
Splunk probes.
