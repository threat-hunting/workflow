# B17 integration workflow pack

These definitions are executable test artifacts for the B17 workspace. They
keep the source of custom actions and the built-in action surface explicit:

| Alias | Source | Action | Writes |
|---|---|---|---|
| `b17-lvl-a-github-registry-ping` | GitHub `threat-hunting/kopal-integrations` | `tools.b17_lab.ping` + `core.transform.reshape` | None |
| `b17-lvl-a-github-ad-readonly` | GitHub `threat-hunting/kopal-integrations` | `tools.soclib.ad.test_connectivity` + `core.transform.reshape` | None |
| `b17-lvl-a-github-splunk-readonly` | GitHub `threat-hunting/kopal-integrations` | `tools.soclib.splunkes.test_connectivity` + `core.transform.reshape` | None |
| `b17-lvl-a-core-registry-smoke` | Kopal built-in `kopal_registry` | `core.transform.reshape` + `core.require` | None |

## Registry contract

The custom source is the GitHub repository configured in Kopal as:

```text
git+ssh://git@github.com/threat-hunting/kopal-integrations.git
```

The workflow definitions do not carry SSH keys, passwords, tokens, or LDAP
values. Kopal resolves the existing organization credentials at execution:

- `soclib_active_directory` for the LDAP probe (the established AD/LDAP
  configuration; no new LDAP setting is introduced).
- `soclib_splunk_es` for the Splunk connectivity probe.

If an old local `kopal_registry_b17` entry is still enabled at the same time as
the GitHub source, disable the duplicate before importing these workflows;
otherwise the action key can be ambiguous. The GitHub `b17_lab.py` compatibility
action is intentionally used only by the ping smoke.

## Safe execution order

1. Run `b17-lvl-a-core-registry-smoke` first; it is deterministic and has no
   external dependency.
2. Run `b17-lvl-a-github-registry-ping` to prove the GitHub registry source.
3. Run `b17-lvl-a-github-ad-readonly` to validate the existing LDAP secret and
   connectivity. It does not query users or mutate directory objects.
4. Run `b17-lvl-a-github-splunk-readonly` only when `soclib_splunk_es` is
   configured; it checks connectivity only and does not create/update data.

Record the workflow run ID and returned `meta.action`/`success` fields in the
test report. A missing credential is a configuration finding, not a reason to
embed a secret in YAML.
